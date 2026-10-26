# Shaper and worker: a two-agent project template

A layout for building software with two agents that are kept apart on purpose. It is for
anything that can be tested separately from its implementation: a command-line program, a
web service, a background job runner, a library through its public interface. The tests
drive it from outside, and the worker only sees the failures.

```
shape/    the shaper: decides what gets built, writes spec records and acceptance tests.
          Never looks into work/.
work/     the worker: writes the program. Never looks into shape/. Learns what to build
          from the output of bin/verify.
```

The tests use what is built the way its users would, so the worker has to build the
behaviour rather than fit the assertions. Each agent is told its rules in its folder's
`AGENTS.md`; `CLAUDE.md` in each folder points at it.

The test kit ships with helpers for programs that are run as a command, in a folder, with
files, as a starting point. Any other kind of target gets a small helper of the project's
own, wired in through the `include` line in `shape/test/test_helper.rb`. For a web
service, that helper starts it on a free port once `bin/build` has built it, waits until
it answers, sends requests, and asserts on the status and the body. A library is driven
through a small harness program in `work/` that exposes its public interface as commands
or requests, since the tests themselves are Ruby.

## Status

0.2.0, an early public release. The layout came out of a project that is still built
this way; the kit and the agents' instructions may change before 1.0. It works with any
coding agent that reads `AGENTS.md` or `CLAUDE.md`, and the tests use Ruby's Minitest
whatever language the program is written in.

0.2.0 changes how the shaper chooses tests: it now tests where two rules meet, races
between copies of the program started separately, and output promised to repeat exactly,
and it writes a table for cases that differ only in their data.

## How a piece of work flows

1. The shaper and the owner agree what to build. The shaper writes a dated record in
   `shape/spec/`, then the tests, and commits both on a branch from `main`.
2. The worker continues on that branch. `bin/verify` builds the program and runs the test
   files the branch adds or changes, which is how the work in hand is defined: there is no
   list to maintain.
3. The worker makes them pass and commits. The owner merges the branch.

Sessions run one at a time. Both agents share one working tree, so the next piece of work
starts after the previous branch is merged.

## Getting the template

The repository is marked as a template, so **Use this template** on its page gives you a
new repository with these files and no history. That is the intended way; a fork keeps a
link back here and suggests you mean to contribute, which a new project does not. Without
a forge account, a shallow clone with the history dropped does the same:

```
git clone --depth 1 <the clone URL on the repository page> my-project
rm -rf my-project/.git
```

Then, in the new folder:

```
./setup vouchers
```

where `vouchers` is the name of the executable the tests will run (for a service, the one
that starts it). The script checks the tools, writes the name where the kit and the
worker's instructions read it, repairs the `work/FORMATS.md` link if it arrived as a plain
file, starts a Git repository on `main` when there is none, removes the files that belong
to the template rather than to your project (`CONTRIBUTING.md`, the template's own CI and
itself), and commits. It prints the next steps: fill in "This project" at the end of
`work/AGENTS.md`, rewrite this README for your project, start the shaper, start the
worker.

**An existing codebase** has to live under `work/`: move it there with `git mv` so its
history follows, add this template's files around it, and run `./setup` with the name of
its executable. The move is the one manual step; nothing automates it on purpose.

## Needs

- Ruby 3.0 or later, with Minitest (`gem install minitest` where it is not bundled)
- Git 2.28 or later
- `diff`
- a file system that keeps symbolic links. On Windows, enable them in Git
  (`core.symlinks`) or run `./setup`, which recreates the link.

## What is here

| Path | What it is |
|---|---|
| `setup` | one-time setup of a new project; commits its changes and deletes itself |
| `CONTRIBUTING.md` | how to contribute to the template; `setup` removes it |
| `.github/workflows/check.yml` | the template's CI: sets up a fresh copy and runs a first test through `bin/verify`; `setup` removes it |
| `shape/AGENTS.md` | the shaper's role, session routine and Git rules |
| `shape/TESTING.md` | how to choose, write and read tests; generic, with an imaginary voucher shop as the example |
| `shape/FORMATS.md` | formats and contracts the worker must know and a failing test cannot show; `work/FORMATS.md` is a symbolic link to it |
| `shape/spec/` | dated records of what was decided, one per piece of work |
| `shape/test/run.rb` | the runner that `work/bin/verify` calls: picks the branch's test files, builds, runs. `TRUNK` names the branch work starts from |
| `shape/test/test_helper.rb` | `PROGRAM` and the base class; the `include` line is where project modules go |
| `shape/test/support/` | the kit: `commands.rb` (run and assert), `workspace.rb` (folders, files, Git), `exact_text.rb` (whole-output diffs), `fixtures.rb`, `target.rb`. They depend on each other. The project's own helpers go here too |
| `shape/test/fixtures/workspaces/` | starting folders for tests |
| `shape/test/expected/` | exact outputs, written by hand from the spec |
| `work/AGENTS.md` | the worker's role and a "This project" section for the owner to fill |
| `work/bin/verify` | runs the suite under `../shape` |
| `work/.gitignore` | build output and the built program |

## Renaming a folder

`work` appears in `shape/AGENTS.md`, `shape/TESTING.md`, `shape/FORMATS.md`, the comments
and two failure messages in `shape/test/`, the fallback path in `shape/test/support/target.rb`,
`setup`, `CLAUDE.md` and this file. `shape` appears in `work/AGENTS.md`, `work/bin/verify`,
the `work/FORMATS.md` link, `setup`, `CLAUDE.md` and this file. `grep -rn` for either name
finds them all.

## Contributing

Issues and pull requests are welcome on GitHub. The GitHub repository is a mirror, so a
pull request is taken as a proposal: an accepted change is applied by the maintainer and
lands on `main` as a new commit that credits you. `CONTRIBUTING.md` has the details.

## Licence

MIT; see `LICENSE`.
