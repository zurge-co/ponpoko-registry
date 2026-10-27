# Ponpoko registry

The curated list of *official* Ponpoko surfaces. The host app fetches
`index.json` on this branch and treats the ids it names as the official ones.

```
https://raw.githubusercontent.com/zurge-co/ponpoko-registry/main/index.json
```

A surface is not published here unless its own repository is public and can be
cloned and built on a bare machine — the index names the repository `poko`
clones, and everything after that is the surface's own business.

## The file

Index version 2 is the current one. It carries two things: the capability
catalog that owns the vocabulary, and the surfaces that speak it.

```json
{
  "version": 2,
  "capabilities": {
    "atoms": [
      {
        "id": "code.edit",
        "label": "open, edit and save source files",
        "synonyms": ["edit", "editing", "แก้โค้ด"]
      }
    ]
  },
  "surfaces": [
    {
      "id": "editor",
      "name": "Editor",
      "repo": "zurge-co/ponpoko-surface-editor",
      "ref": "main",
      "description": "A code editor: open a folder, browse it, edit files in tabs, search and format them.",
      "doors": [],
      "capabilities": ["code.edit"],
      "ui": ["ui.tabs", "ui.sidebar"],
      "backend": ["backend.process"],
      "data": ["data.local-files"],
      "license": "NOASSERTION",
      "maintenance": { "status": "active" },
      "not_for": ["sheets.grid", "pdf.read"]
    }
  ]
}
```

## The catalog owns the vocabulary

Every word an entry uses — a capability, a `ui`, a `backend`, a `data` atom or
a `not_for` veto — is resolved through `capabilities.atoms`. A word the catalog
does not name is refused, and so is a word two atoms claim: one word names one
atom. That is what makes matching deterministic instead of a guess.

An atom is `area.name` in lowercase ASCII, at most three dot-separated
segments, such as `code.edit` or `data.local-files`. Its `label` says what it
means to the human, and its `synonyms` carry the words a request is likely to
use, in English or Thai.

| Field | Rule |
| --- | --- |
| `id` | `area.name`, lowercase, 2 to 3 segments |
| `label` | 1 to 96 characters; what the human reads |
| `synonyms` | up to 24 words, each claimed by exactly one atom |

## An entry

| Field | Rule |
| --- | --- |
| `version` | exactly `2`; version `1` is still read, as legacy |
| `surfaces` | at most 64 entries |
| `id` | kebab-case, unique, not a reserved built-in id |
| `name` | 1 to 64 characters |
| `repo` | `owner/repository` on GitHub — the repository the host clones |
| `ref` | a branch or tag the host may check out, or `""` for the default branch |
| `description` | at most 240 characters |
| `doors` | only the eight host doors; an index never widens that vocabulary |
| `capabilities` | at least one atom; what the surface actually does |
| `ui` | at least one atom; a surface is a screen, so it says what the screen shows |
| `backend` | atoms for the local process it needs, or `[]` |
| `data` | atoms for what it reads and keeps, or `[]` |
| `license` | one SPDX identifier, or `NOASSERTION` when the repo states none |
| `maintenance` | `status` of `active`, `maintenance` or `dormant`, and optionally `last_release` as `YYYY-MM-DD` |
| `not_for` | atoms this surface must never be recommended for; it may not repeat an atom the entry already declares |

The doors are `host.pty`, `host.background`, `host.storage`, `host.context`,
`host.chat.send`, `host.form`, `tool.notify` and `host.secret`. Both surfaces
listed here reach the host through `tool.file` and `backend.call`, which the
host opens on the bridge rather than through a door, so both declare none.

A broken entry fails the whole fetch, not just that entry: the host reads this
file all at once, so a change here is checked against a real `poko
surface-registry` before it is pushed.

## What version 1 still means

A host that reads this file also reads version 1 (`id`, `name`, `repo`, `ref`,
`description`, `doors`), but only as *legacy*: a v1 entry carries no
capabilities, so it can be named and cloned, never matched, recommended or used
as the shape of a new surface. Version 1 is a name list; version 2 is the
decision surface.

## What is not here

No build, no dependencies, no per-surface copy of the code. The surfaces live
in their own repositories:

- [`zurge-co/ponpoko-surface-editor`](https://github.com/zurge-co/ponpoko-surface-editor)
- [`zurge-co/ponpoko-surface-terminal`](https://github.com/zurge-co/ponpoko-surface-terminal)

## Adding a surface

1. Publish the surface's own repository, with a manifest whose `id` matches the
   id you are about to add.
2. Add the atoms it needs to `capabilities.atoms` if they are not there yet,
   and one entry that declares all seven dimensions.
3. Run `poko surface-registry` against a build that reads this file and confirm
   the id appears with its capability atoms.

An install is still chosen by a human: this list says what *official* means, it
does not install anything, and there is no sandbox.
