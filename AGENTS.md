# Repository instructions

- The source of truth for this repository's skill is `.agents/skills/rtk-safe-execution/SKILL.md`.
- Run `python3 -m unittest discover -s .agents/skills/rtk-safe-execution/tests -v` after changing the skill or its scripts.
- Preserve the original command exit status and unfiltered raw logs; never treat RTK's shortened view as authoritative evidence.
- Do not auto-install RTK, enable global hooks, change external repositories, or publish raw logs as part of a skill change.
- Do not compress official CI records, security audit output, credentials, or authorization evidence.
- Keep this repository as the reviewed source; other repositories must adopt it explicitly via their own PRs.
