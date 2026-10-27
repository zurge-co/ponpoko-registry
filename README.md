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

```json
{
  "version": 1,
  "surfaces": [
    {
      "id": "editor",
      "name": "Editor",
      "repo": "zurge-co/ponpoko-surface-editor",
      "ref": "main",
      "description": "A code editor: open a folder, browse it, edit files in tabs, search and format them.",
      "doors": []
    }
  ]
}
```

| Field | Rule |
| --- | --- |
| `version` | exactly `1`; the host refuses any other index version |
| `surfaces` | at most 64 entries |
| `id` | kebab-case, unique, not a reserved built-in id |
| `name` | 1 to 64 characters |
| `repo` | `owner/repository` on GitHub — the repository the host clones |
| `ref` | a branch or tag the host may check out, or `""` for the default branch |
| `description` | at most 240 characters |
| `doors` | only the eight host doors; an index never widens that vocabulary |

The doors are `host.pty`, `host.background`, `host.storage`, `host.context`,
`host.chat.send`, `host.form`, `tool.notify` and `host.secret`. Both surfaces
listed here reach the host through `tool.file` and `backend.call`, which the
host opens on the bridge rather than through a door, so both declare none.

A broken entry fails the whole fetch, not just that entry: the host reads this
file all at once, so a change here is checked against a real `poko
surface-registry` before it is pushed.

## What is not here

No build, no dependencies, no per-surface copy of the code. The surfaces live
in their own repositories:

- [`zurge-co/ponpoko-surface-editor`](https://github.com/zurge-co/ponpoko-surface-editor)
- [`zurge-co/ponpoko-surface-terminal`](https://github.com/zurge-co/ponpoko-surface-terminal)

## Adding a surface

1. Publish the surface's own repository, with a manifest whose `id` matches the
   id you are about to add.
2. Add one entry here, on `main`.
3. Run `poko surface-registry` against a build that reads this file and confirm
   the id appears with its repository.

An install is still chosen by a human: this list says what *official* means, it
does not install anything, and there is no sandbox.
