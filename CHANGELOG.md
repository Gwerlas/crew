# gwerlas\.crew Release Notes

**Topics**

- <a href="#v0-1-0">v0\.1\.0</a>
    - <a href="#release-summary">Release Summary</a>
    - <a href="#minor-changes">Minor Changes</a>

<a id="v0-1-0"></a>
## v0\.1\.0

<a id="release-summary"></a>
### Release Summary

First release\: the role that sets up crew\'s workers\, extracted from the playbook that ran them\, at crew 0\.22\.0\.

<a id="minor-changes"></a>
### Minor Changes

* Set up crew\'s workers on a host\, as crew\'s setting\-up guide does by hand\, steps 1 to 6 and 8\: the workers\' user\, the runners the team\'s profiles name \(Claude Code\, the Kilo CLI\) at <code>latest</code> or a pinned version\, the GitLab tokens\, the shared directories\, the installed copy of a crew release with the team\'s configuration\, the sudo rule and the daily token renewal\.
