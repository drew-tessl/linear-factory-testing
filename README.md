# linear-factory-testing

Disposable fixture repository for testing the Tessl Linear trigger path and
launch sandboxes. Nothing here is production code, and anything in it may be
deleted or force-pushed at any time.

## What is in here

- `tessl.json` — trigger configuration. One trigger, `linear-delegation`,
  listening for `linear:AgentSessionEvent.created`, which is the event Linear
  emits when an issue is delegated to an agent.
- `plugins/issue-solver/` — a vendored copy of the kikimora issue-solver skill.
  Vendored rather than referenced from the registry because the plugin is
  marked `"private": true` and a registry ref would not resolve.

## Dry run is ON

`tessl.json` sets `"triggersDryRun": true`. Trigger config is read and matched,
but no run is launched. Set it to `false` to let deliveries actually run.

## Two things this repo cannot do on its own

1. **Trigger dispatch needs the backend pointed here.** The production backend
   resolves Linear deliveries to one repository through the
   `LINEAR_TRIGGER_PROJECT_REPO_URL` secret. It is a single value per instance.
   Until it names this repository, deliveries are ignored.

2. **The action names an environment that must exist.** `solve-linear-issue`
   names the `linear-factory-testing` workspace environment. A run with no
   environment mints a read-only GitHub token, so opening a pull request needs
   that environment to exist and to declare `githubAccess: write`.

## Running the skill directly, without Linear

The sandbox half can be proved on its own, with no webhook and no trigger:

```
tessl launch skill --cloud \
  --repo drew-tessl/linear-factory-testing \
  --agent claude-code \
  --env-file <path to a .env with GH_TOKEN / GITHUB_TOKEN> \
  --base-branch main \
  --wait \
  file:plugins/issue-solver
```

The skill takes required inputs (`ISSUE_IDENTIFIER`, `BASE_BRANCH`,
`BRANCH_NAME`). Passing them needs a CLI build carrying `--input`, which the
`nightly.20260922` release does not have.
