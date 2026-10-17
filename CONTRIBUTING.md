<!-- About the template itself: ./setup removes this file from a new project. -->
# Contributing

Issues and pull requests are welcome on GitHub.

The GitHub repository is a mirror, and its `main` branch is written only from the
maintainer's side. So a pull request is taken as a proposal, not merged: when a change is
accepted, the maintainer applies it, it lands on `main` as a new commit whose message
credits you, and the pull request is closed with a link to that commit.

What helps:

- **An issue first** for anything larger than a fix, so that we agree on the change
  before you write it.
- **What the change is for and how you checked it.** CI sets up a fresh copy of the
  template, builds a toy program and runs its test through `bin/verify`; a change to
  `setup`, `shape/test/` or `work/bin/verify` should keep that passing.
- **Keep the template generic.** Examples use the imaginary voucher shop, and nothing
  may assume a particular forge, editor, agent or machine.
- **Keep the two sides apart.** The shaper's rules are in `shape/AGENTS.md`, the worker's
  in `work/AGENTS.md`. A change that lets either side see into the other undoes the point
  of the template.

Contributions are accepted under the repository's MIT licence.
