# claude-marketplace

A [Claude Code](https://claude.com/claude-code) plugin marketplace by David Haubenstricker. Hosts opinionated plugins for C# development workflows.

> **Status:** Experimental. APIs and conventions may change.

## Plugins

| Plugin | Description |
|---|---|
| [`csharp-code-guidelines`](csharp-code-guidelines/) | C# structural and functional code guidelines applied automatically when writing or reviewing C# code. |
| [`csharp-write-tests`](csharp-write-tests/) | Generate C# unit tests for a class or file using project conventions (NUnit, `Assert.That`, `Create` helpers, `TestHelpers`). |

## Installation

Add this marketplace to Claude Code, then install the plugins you want.

```
/plugin marketplace add dhaub/claude-marketplace
/plugin install csharp-code-guidelines
/plugin install csharp-write-tests
```

To update later:

```
/plugin marketplace update claude-marketplace
```

To remove:

```
/plugin uninstall <plugin-name>
/plugin marketplace remove claude-marketplace
```

## Usage

Plugins ship as **skills** that Claude Code auto-invokes based on user intent.

- **`csharp-code-guidelines`** — auto-applies whenever Claude writes, modifies, or reviews C# in your repo. Not user-invocable; it activates implicitly.
- **`csharp-write-tests`** — triggers on phrases like *"write tests for `Foo.cs`"*, *"add unit tests"*, *"generate tests"*. Reads the target file, mirrors existing `*.UTest` patterns, builds, runs the filtered test set.

Both encode opinionated conventions (functional/data object split, immutability, `Create<T>()` helpers over inline mocks). They override default C# style — read each [`SKILL.md`](csharp-code-guidelines/skills/code-guidelines/SKILL.md) before adopting.

## Repository Layout

```
.claude-plugin/marketplace.json    # marketplace manifest, lists plugins
csharp-code-guidelines/
  .claude-plugin/plugin.json       # plugin manifest
  skills/code-guidelines/SKILL.md  # skill definition + frontmatter
csharp-write-tests/
  .claude-plugin/plugin.json
  skills/write-tests/SKILL.md
```

## Contributing

See [CLAUDE.md](CLAUDE.md) for architecture and the steps to add a new plugin.

## License

[MIT](LICENSE)
