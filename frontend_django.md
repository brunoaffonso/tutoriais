### Tutorial: Django + Vite + Tailwind CSS (v4) + DaisyUI + HTMX (passo a passo)

## Pré-requisitos
- **Python** >= 3.12 (projeto Django já criado)
- **Node.js** + **npm** instalados
- Estrutura sugerida:
  - `frontend/` (Vite)
  - `static/dist/` (saída do build do Vite)
  - templates Django em `manutencao_web/**/templates/**/*.html`

---

## 1) Criar o frontend com Vite
Na raiz do projeto (onde está o `manage.py`):

```bash
npm create vite@latest frontend -- --template vanilla
cd frontend
npm install
```

---

## 2) Instalar Tailwind v4 + DaisyUI + PostCSS (dependências)
Ainda em `frontend/`:

```bash
npm i -D tailwindcss postcss autoprefixer @tailwindcss/vite
npm i -D daisyui
```

---

## 3) Instalar HTMX
Em `frontend/`:

```bash
npm i htmx.org
```

---

## 4) Configurar o Vite para gerar build para o Django
Edite `frontend/vite.config.js` para:
- gerar em `../static/dist`
- criar `manifest.json`

Exemplo:

```js
import { defineConfig } from "vite"
import tailwindcss from "@tailwindcss/vite"

export default defineConfig({
  plugins: [tailwindcss()],
  build: {
    outDir: "../static/dist",
    emptyOutDir: true,
    manifest: true,
    rollupOptions: {
      input: "./src/main.js",
    },
  },
})
```

---

## 5) Configurar Tailwind + DaisyUI
Crie/edite `frontend/tailwind.config.js` com:
- `content` apontando para templates Django
- plugin do DaisyUI

Exemplo:

```js
import daisyui from "daisyui"

export default {
  content: [
    "../manutencao_web/**/templates/**/*.html",
    "./src/**/*.{js,css}",
  ],
  theme: {
    extend: {},
  },
  plugins: [daisyui],
  daisyui: {
    themes: ["light", "dark"],
  },
}
```

---

## 6) Importar Tailwind e HTMX no entrypoint do Vite
Edite `frontend/src/style.css` para incluir Tailwind:

```css
@import "tailwindcss";

/* aqui você coloca seus estilos adicionais */
```

Edite `frontend/src/main.js` para importar HTMX e o CSS:

```js
import "htmx.org"
import "./style.css"
```

---

## 7) Criar integração no Django para ler o manifest do Vite
No Django, crie um template tag (ex.: `core/templatetags/vite.py`) que:
- lê o manifest gerado pelo Vite
- injeta CSS e JS do entrypoint no template

Exemplo (tag única que injeta CSS + JS):

```python
import json
from pathlib import Path

from django import template
from django.conf import settings
from django.templatetags.static import static
from django.utils.safestring import mark_safe

register = template.Library()


def _load_manifest():
    manifest_path = (
        Path(settings.BASE_DIR) / "static" / "dist" / ".vite" / "manifest.json"
    )
    return json.loads(manifest_path.read_text())


@register.simple_tag
def vite_tags(entry="src/main.js"):
    manifest = _load_manifest()
    chunk = manifest[entry]

    tags = []
    for css in chunk.get("css", []):
        tags.append(f'<link rel="stylesheet" href="{static("dist/" + css)}">')

    tags.append(
        f'<script type="module" src="{static("dist/" + chunk["file"])}"></script>'
    )
    return mark_safe("\n".join(tags))
```

---

## 8) Garantir que o Django serve a pasta `static/` do projeto
No `settings.py`:

```python
STATIC_URL = "/static/"

STATICFILES_DIRS = [
    BASE_DIR / "static",
]
```

---

## 9) Usar no template base do Django
No template base (ex.: `templates/index.html` ou `base.html`):

```html
{% load vite %}
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Meu sistema</title>
    {% vite_tags "src/main.js" %}
  </head>
  <body>
    {% block content %}{% endblock %}
  </body>
</html>
```

---

## 10) Build do frontend e execução do Django
Gere os assets:

```bash
cd frontend
npm run build
```

Depois rode o Django normalmente:

```bash
python manage.py runserver
```

---

### Checklist de validação
- Existe `static/dist/.vite/manifest.json`
- Existe `static/dist/assets/*.css` e `static/dist/assets/*.js`
- O HTML final da página (View Source) contém:
  - `<link rel="stylesheet" href="/static/dist/assets/...css">`
  - `<script type="module" src="/static/dist/assets/...js"></script>`
- Tailwind/DaisyUI aplicando classes nos templates
- HTMX funcionando (atributos `hx-*` respondendo)
