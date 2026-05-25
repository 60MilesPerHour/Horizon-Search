# Search syntax

Horizon lets you target specific engines, categories, and languages directly
from your search query. See {{link('preferences', 'preferences')}} for the full
list of available engines, categories, and languages.

## `!` Select engine and category

Prefix with `!` to target an engine or category:

- Search Wikipedia for **paris**:

  - {{search('!wp paris')}}
  - {{search('!wikipedia paris')}}

- Search in the **map** category for **paris**:

  - {{search('!map paris')}}

- Image search:

  - {{search('!images Wau Holland')}}

Abbreviations are accepted. Modifiers are chainable — for example,
{{search('!map !ddg !wp paris')}} searches the map category, DuckDuckGo, and
Wikipedia simultaneously for **paris**.

## `:` Select language

Prefix with `:` to filter by language:

- {{search(':fr !wp Wau Holland')}}

## `!!<bang>` External bangs

Use `!!` to jump directly to an external search engine (DuckDuckGo bangs):

- {{search('!!wfr Wau Holland')}}

Note: your search is sent directly to the external engine — Horizon cannot
protect your privacy for these queries.

## `!!` Redirect to first result

Including `!!` in your query (separated by spaces) redirects you to the first
result automatically — similar to "I'm Feeling Lucky":

- {{search('!! Wau Holland')}}

Use with caution — the destination cannot be verified in advance.

## Special Queries

The {{link('preferences', 'preferences')}} page lists keywords for special
built-in queries. A few examples:

- Random UUID: {{search('random uuid')}}
- Average of numbers: {{search('avg 123 548 2.04 24.2')}}
- Browser user agent: {{search('user-agent')}}
- Hash a string: {{search('md5 lorem ipsum')}}

[DuckDuckGo]: https://duckduckgo.com/bang
