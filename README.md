<h1 align="center">template.mq</h1>

A lightweight [Mustache](https://mustache.github.io/)/Handlebars-style templating engine
implemented as an [mq](https://github.com/harehare/mq) module. Renders `{{ }}` templates
against mq values (dicts, arrays, and scalars) — handy for turning data into Markdown/text
documents, complementing the built-in `md` builder module.

## Features

- Variable interpolation: `{{name}}` (HTML-escaped), `{{{name}}}` / `{{&name}}` (unescaped)
- Dotted path lookups: `{{a.b.c}}`
- Current context: `{{.}}` / `{{this}}`
- Sections: `{{#name}}...{{/name}}` — loops over arrays, renders once for truthy
  dicts/scalars, skips falsy values
- Inverted sections: `{{^name}}...{{/name}}` — renders only when `name` is falsy
- Comments: `{{! comment }}`
- Standalone-line whitespace trimming, so section/inverted/comment tags on their own
  line don't leave blank lines behind

Falsy values are `None`, `false`, and empty arrays; everything else — including `0` and
`""` — is truthy, per the Mustache spec.

## Installation

Copy `template.mq` to your mq module directory, or place it anywhere and reference it with `-L`.

```sh
cp template.mq ~/.local/mq/config/
```

## Usage

```sh
mq -L /path/to/modules -I raw \
  'import "template" | template::template_render(., {"name": "World"})' template.txt
```

If you copied it to the mq built-in module directory:

```sh
mq -I raw 'import "template" | template::template_render(., {"name": "World"})' template.txt
```

## API

### `template_render(template, data)`

Renders the `template` string against `data` and returns the resulting string.

| Input | Type | Description |
|---|---|---|
| `template` | String | A Mustache/Handlebars-style template |
| `data` | Any | The root context (typically a dict) |

Raises an error for unterminated tags or mismatched/unopened section closing tags.

## Example

```sh
mq -L . -I raw 'let t = import "template"
| t::template_render(
    "# {{title}}\n\n{{#items}}\n- {{name}}: {{price}}\n{{/items}}\n{{^items}}\nNo items.\n{{/items}}\n",
    {"title": "Order", "items": [{"name": "Widget", "price": 10}, {"name": "Gadget", "price": 20}]}
  )' <<< ""
```

```markdown
# Order

- Widget: 10
- Gadget: 20
```

## Compatibility

Requires [mq](https://github.com/harehare/mq) v0.6 or later.

## License

MIT
