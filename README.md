Crew
====

[![pipeline status](https://gitlab.com/yoanncolin/ansible/roles/crew/badges/main/pipeline.svg)](https://gitlab.com/yoanncolin/ansible/roles/crew/-/commits/main)

Set up the workers of [crew][], the skills that run a team of coding-agent
workers on a GitLab backlog, on the host they run on.

GitLab project: [yoanncolin/ansible/roles/crew][project]

What it does
------------

The role does what crew's [setting-up guide][setting-up] does by hand, steps 1
to 6 and 8, at the crew release `crew_version` names:

1. the workers' user, its group shared with you, its lingering user manager,
   and your home closed to it (mode `0750`, its group not one the workers are
   in);
2. the runners your profiles name in your configuration: Claude Code for the
   workers, and their token, when a profile runs on `claude-code`; the Kilo
   CLI, configured on a local Ollama, when one runs on `kilo`;
3. the workers' GitLab tokens and git credential stores;
4. the shared directories, and the run files directory;
5. the crew release, checked out root-owned, and the installed copy: both
   skills, their version file, your team's configuration, brief fragments and
   helpers from `crew_config_dir`, and the GitLab scopes the workers hold a
   token for;
6. the sudo rule that lets you, and you alone, run the launcher as the
   workers;
7. step 7, the skills in your own session, is yours: the guide says how;
8. the daily renewal of the GitLab tokens, a timer of the workers' user
   manager, which also runs five minutes after boot.

The tokens you give are the first install's only. The renewal rotates each one
before it expires, in the workers' home, and the role never writes a token
over one the workers already hold: putting the first one back would hand them
a revoked token.

Run it again after every change to your `.crew` directory, and to upgrade crew:
the skills it installs are exactly what the release holds, and the helpers what
`crew_config_dir` holds, so a helper you removed is taken away from the
workers. Upgrade your session's plugin to the same release: the launcher
refuses a new run when the two differ.

Requirements
------------

- systemd, for the workers' user manager, and `loginctl`;
- `git`, `curl` (Claude Code's installer), `sudo`, and `setfacl` (the `acl`
  package on Debian) with a file system that supports ACLs where
  `crew_shared_dir` lives;
- gitlab.com: crew's launcher works with no other GitLab yet;
- npm on the workers' `PATH`, when a profile runs on `kilo`;
- become: every task runs as root.

The play runs as you, the owner, with become on the role: `crew_owner` is read
from the facts, and a play run as root would make root the owner, which the
role refuses.

Supported platforms: Debian 12 and 13, and Gentoo.

Dependencies
------------

The collections in [requirements.yml](requirements.yml):
[ansible.posix][] for the shared directories' default ACL, and
[community.general][] 4.3 or later for the sudo rule.

```sh
ansible-galaxy collection install ansible.posix 'community.general:>=4.3.0'
```

Variables
---------

The defaults, with what each one is for, are in [defaults/main/](defaults/main/),
one file for the workers' setup and one for each of Claude Code and Kilo, and
`ansible-doc -t role` shows them from
[meta/argument_specs.yml](meta/argument_specs.yml). The ones you set:

| Variable             | What it holds                                                                                 |
| -------------------- | --------------------------------------------------------------------------------------------- |
| `crew_config_dir`    | Your team's `.crew` directory on the controller. Required.                                    |
| `crew_gitlab_tokens` | The workers' GitLab tokens, by group or project path. Required, vault them.                   |
| `crew_claude_token`  | The token from `claude setup-token`. Required when a profile runs on `claude-code`, vault it. |
| `crew_version`       | The crew release to install, the one your session's plugin is at.                             |

`crew_shared_dir`, `crew_runs_dir` and `crew_root` must match `crew.shared`,
`crew.runs` and the directory of `crew.launcher` in your configuration: the
role refuses a launcher it does not install.

### Versions of Claude Code and Kilo

`crew_claude_version` and `crew_kilo_version` are `latest` by default. The
role installs Claude Code when it is missing, then leaves it to update itself;
Kilo, installed in the workers' home, cannot update itself, so the role
installs its latest release on every run. Set a version to hold the workers
there: the role installs it whenever another is in place, and turns the tool's
own update off, for the workers alone (Claude Code's through the environment
of their user manager, Kilo's in their `kilo.json`).

Example playbook
----------------

```yaml
- name: Workers of the team
  hosts: workstation
  roles:
    - role: gwerlas.crew
      become: true
      vars:
        crew_config_dir: "{{ playbook_dir }}/.crew"
        crew_version: X.Y.Z  # the release your session's plugin is at
        crew_claude_token: "{{ vault_crew_claude_token }}"
        crew_gitlab_tokens:
          example-group: "{{ vault_crew_gitlab_token }}"
```

Licence
-------

[BSD 3-Clause](LICENSE).

[crew]: https://gitlab.com/yoanncolin/agents/crew
[project]: https://gitlab.com/yoanncolin/ansible/roles/crew
[setting-up]: https://gitlab.com/yoanncolin/agents/crew/-/blob/main/docs/setting-up.md
[ansible.posix]: https://galaxy.ansible.com/ui/repo/published/ansible/posix/
[community.general]: https://galaxy.ansible.com/ui/repo/published/community/general/
