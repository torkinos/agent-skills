# Pre-publish checks

Run these against the working tree and the diff before proposing anything. A push is irreversible, so this comes *before* the commit message, not after it.

The right-hand column is as binding as the left. Those items are not findings, and raising them turns a useful check into noise the user learns to skim.

| Check | What to look for | Not a finding |
|---|---|---|
| Credentials and tokens | API keys, private keys, `.pem` and `.p12` files, connection strings carrying passwords, OAuth tokens, session cookies, cloud access keys — in code, tests, fixtures, logs, comments, and in the commit message itself. Any hit blocks the handoff. | Obvious placeholders whose value was never real, such as `sk-test-xxxx` or a fixture password used only by the test that reads it. |
| Environment and local config | `.env` and its variants, `*.local.*`, editor and IDE directories, `.DS_Store`, credential files — tracked or untracked. Confirm `.gitignore` already covers them, because the check has to happen *before* the user runs `git add`, not after. | A committed `.env.example` carrying placeholder values. |
| Personal and customer data | Real names, email addresses, phone numbers, account identifiers, screenshots containing real data, and seed data copied from production. | Synthetic fixture data that is obviously invented. |
| Internal surface | Internal hostnames, private URLs, staging credentials, and links to private trackers left in code comments where they will outlive the branch. | Links in a pull request description, where every reader already has access. |
| Large and generated files | Build output, `dist/`, minified bundles, images over about a megabyte, database dumps, model weights, coverage reports. | Lockfiles the project tracks deliberately. |
| Debug leftovers | `console.log`, `print`, `debugger`, commented-out code, TODOs describing work this change finished, temporary feature flags, and tests skipped or disabled to get green. This is the `clean-as-you-go` contract applied at the last possible moment. | A deliberate structured log statement that belongs in the shipped code. |
| Unrelated edits | Files touched by accident: an IDE reformat, a formatting sweep, a stray version bump, whitespace-only changes in files the work never needed. | Mechanical churn the change genuinely forces, such as a signature updated at every call site. |
| Untracked strays | Scratch files, `.bak` copies, screenshots, notes, and one-off scripts written during the work. Say for each whether it should be deleted, ignored, or kept. | Files the user explicitly asked for as part of the deliverable. |

## Publication surfaces

Version control is not the only way work becomes public. Each of these is gated exactly like a push, and each needs its own explicit instruction:

- Pull request and issue comments, reviews, and approvals.
- Releases, tags, and changelog publication.
- Package registries: `npm publish`, `pnpm publish`, `yarn publish`, `cargo publish`, `poetry publish`, `twine upload`.
- Gists, pastebins, and any "share a link" flow.
- Container and artifact registries: `docker push`, and pushes to a private registry, which is still an outward action.
- Deploys: `vercel`, `netlify`, `fly`, `wrangler`, `eas`, `gcloud`, `aws`, and any project script that wraps one.
- CI triggers that publish or deploy as a side effect of running.
- Documentation sites and status pages.
- Any message sent to Slack, email, or a webhook.

A private repository is not an exception. Access can widen later, forks and mirrors persist, and history is not erased by a subsequent delete.
