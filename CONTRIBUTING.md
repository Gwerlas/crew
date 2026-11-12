Development guide
=================

This guide keeps what is specific to the role: how to check a change, how to
release it, the rules that are its own. The rest is in the
[engineering handbook][handbook], which binds every contribution here. As an
Ansible role, it is also bound by the family's reference guide, the
[`gwerlas.system` collection's CONTRIBUTING.md][collection-guide], and by the
[principles][collection-principles] that guide enforces. The pages this role's
work touches most:

- [Collection guide][collection-guide]: tags, modules before tasks, a file the
  distribution ships, variables, changelog;
- [Code][handbook-code]: choices, tests, YAML;
- [Git][handbook-git]: commits, issue references;
- [Documentation][handbook-documentation]: who reads what, where a rationale
  lives, Markdown;
- [CI][handbook-ci];
- [Releases][handbook-releases];
- [Issues and merge requests][handbook-mr].

What the role does follows crew's own
[setting-up guide][crew-setting-up]: a task that departs from it says why in a
comment, and a change to that guide upstream is a change here.

Checking a change
-----------------

The pipeline runs ansible-lint twice (at the root, and on the role installed
under `roles/crew/`, where `var-naming[no-role-prefix]` can tell which role a
variable belongs to), markdownlint, ShellCheck over `jobs/`, and the changelog
checks. Each runs locally with the same command as its job in
[`.gitlab-ci.yml`](.gitlab-ci.yml).

The role has no Molecule scenario yet, so nothing converges it in CI: a change
to a task is checked by running it on a host, then running it again to see it
changes nothing. Say in the merge request on which distribution, and with which
crew version.

Releasing
---------

What a tag publishes, and how to number and log a release, is the handbook's
[Releases][handbook-releases] page. Here the `import` job pushes the role to
Ansible Galaxy.

Each merge request adds a fragment under `changelogs/fragments/`. The release
merge request runs `antsibull-changelog release --version X.Y.Z --date
YYYY-MM-DD`, since `changelogs/config.yaml` sets the project in "other
project" mode, which has no `galaxy.yml` to take them from. The
`changelog-release` job, which `import` waits for, fails a tag that leaves a
fragment behind or that `changelogs/changelog.yaml` does not hold a release
for; `jobs/changelog-release` runs the same check locally, with
`CI_COMMIT_TAG` set.

[`.gitattributes`](.gitattributes) lists what stays out of the archive users
install: CI, the job scripts, the changelog sources, linter settings, this
guide. A new file that only serves development belongs in that list.

[handbook]: https://gitlab.com/yoanncolin/handbook/-/blob/main/README.md
[handbook-code]: https://gitlab.com/yoanncolin/handbook/-/blob/main/code.md
[handbook-git]: https://gitlab.com/yoanncolin/handbook/-/blob/main/git.md
[handbook-documentation]: https://gitlab.com/yoanncolin/handbook/-/blob/main/documentation.md
[handbook-ci]: https://gitlab.com/yoanncolin/handbook/-/blob/main/ci.md
[handbook-releases]: https://gitlab.com/yoanncolin/handbook/-/blob/main/releases.md
[handbook-mr]: https://gitlab.com/yoanncolin/handbook/-/blob/main/issues-and-merge-requests.md
[collection-guide]: https://gitlab.com/yoanncolin/ansible/collections/system/-/blob/main/CONTRIBUTING.md
[collection-principles]: https://gitlab.com/yoanncolin/ansible/collections/system/-/blob/main/docs/principles.md
[crew-setting-up]: https://gitlab.com/yoanncolin/agents/crew/-/blob/main/docs/setting-up.md
