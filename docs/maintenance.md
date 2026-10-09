# Maintenance

## 1. Validation

```bash
claude plugin validate ./
```

## 2. Agent models

To change an agent's model: the `model:` line in the frontmatter under `agents/`. The change has no effect while `CLAUDE_CODE_SUBAGENT_MODEL` is set, see section 4 "Notes" of the README.

## 3. Versions

`version` in `.claude-plugin/plugin.json` is set. Users stay on this version until you raise it. Dependencies without a version range follow the current state of the other plugins. For fixed version ranges, tag the releases with `claude plugin tag --push` and add the ranges to `dependencies`.

Source: [Claude Code docs, host a marketplace](https://code.claude.com/docs/en/plugins/host-marketplace) and [plugin dependencies](https://code.claude.com/docs/en/plugins/dependencies).

## 4. Section references

Agents and docs refer to sections by number and name, e.g. `section 3 "Review"`, also across plugins. When sections of a skill or README are added, removed or reordered, update every reference in all four repositories. Find them in each repository with:

```bash
grep -rnE '[Ss]ections? [0-9]+ "' agents docs
```
