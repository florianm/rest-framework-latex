# REST Framework LaTeX Plugin

A simple plug-n-play LaTeX renderer for Django REST Framework.

## Dependencies

Currently the LaTeX plugin requires `lualatex` - to install this on Ubuntu:

```bash
sudo aptitude install texlive-latex-extra texlive-xetex
```

This will probably take some time due to the size of LaTeX (around 1GB)

## Using the Renderer

You can then configure the renderer in your settings or on each view:

```python
REST_FRAMEWORK = {
  'DEFAULT_RENDERER_CLASSES': [
    'rest_framework_latex.renderers.LatexRenderer',
  ]
}
```

### `LATEX_RESOURCES` Setting

The `LATEX_RESOURCES` directory contains the base template environment e.g.
any images or static resources to include in your template. This must be set for
the renderer to work:

```python
LATEX_RESOURCES = '/home/user/path_to_resources'
```

This works just like `TemplateHTMLRenderer` but by setting a `latex_name` on
your view:

```python
from rest_framework import viewsets

from rest_framework_latex import renderers


class SomeViewSet(viewsets.ViewSet):
  """
  """
  renderer_classes = [
    renderers.LatexRenderer,
  ]

  latex_name = 'directory/latexfile.tex'
```

### Latex Templates

The TeX file used for rendering will be pushed through Django's templating
system. This will cause some issues whereby you want to do something like:

```latex
\textt{{{ some_variable }}}
```

To get around this issue you will need to do something like the following:

```latex
\textt{% templatetag openbrace %}{{ some_variable }}{% templatetag closebrace %}
```

## How it works

The renderer works by creating a new tempoary directory, and then copying
over the `LATEX_RESOURCES` directory into the new tempoary directory.

Next it renders the TeX file into the tempoary directory.

Then it runs lualatex over the TeX file, and this will produce the PDF file
we read into memory.

Then we delete the tempoary directory and return the PDF to the client.
