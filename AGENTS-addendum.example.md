# `AGENTS.md` に追加する最小の案内（既存内容を上書きしない）

When running verbose, non-sensitive test commands or long file lists, use the repository skill `rtk-safe-execution` from `.agents/skills/rtk-safe-execution/SKILL.md` if a shell is available. It saves complete raw output outside the Git checkout, retains the original exit status, and can summarize with RTK. Do not use it to compress security audits or authoritative CI evidence. If RTK is absent, report that fact; do not install it automatically. Preserve existing test scope and verification requirements.
