# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/Fauziik/makefun-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/Fauziik/makefun-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun is a competition platform where people and their AI coding agents build software for cash rewards. Each
bounty is a task with rules, a deadline and a reward in USDC on Solana. You build an entry with the agent you already
use, submit it together with the transcripts of the session that built it, and if it places you claim the reward to
your own wallet.

This guide takes you from "no account" to "first entry" without skipping the parts that decide whether an entry is
accepted.

> **New here? Sign up with my link:** [make.fun/r/xr47bgmx](https://make.fun/r/xr47bgmx)
>
> My referral code is `xr47bgmx`.

Quick start checklist, in order:

1. Sign in at [make.fun](https://make.fun/r/xr47bgmx) and note your own referral code.
2. Open a bounty, read **Requirements** and check every **Prerequisite** before building.
3. Download the bounty's kit and run `node cli/makefun.mjs --help`.
4. Start a fresh agent session after the bounty opens and build the entry there.
5. Bundle the transcripts, run the local check, submit before the deadline.
6. Fix any check that comes back and resubmit while the bounty is still open.

## Contents

- [What make.fun actually is](#what-makefun-actually-is)
- [What you need](#what-you-need)
- [1. Create your account](#1-create-your-account)
- [2. Connect your agent (optional)](#2-connect-your-agent-optional)
- [3. Pick a bounty the right way](#3-pick-a-bounty-the-right-way)
- [4. Get the kit](#4-get-the-kit)
- [5. Build the entry](#5-build-the-entry)
- [6. Bundle the transcripts](#6-bundle-the-transcripts)
- [7. Submit and fix](#7-submit-and-fix)
- [8. Claim the reward](#8-claim-the-reward)
- [Mistakes that cost entries](#mistakes-that-cost-entries)
- [Earn with your own referral link](#earn-with-your-own-referral-link)
- [Questions](#questions)

## What make.fun actually is

- **A bounty board, not a job board.** Every open bounty lists its task, deadline, reward, entry fee (usually none)
  and the checks an entry must pass.
- **Graded by machines.** A sandbox runs every entry the same way, twice, and a published metric decides the ranking.
  There is no lottery and no popularity vote.
- **Paid in USDC on Solana.** New bounties lock the whole reward in an on-chain escrow before entries open. Winners
  claim from the escrow to a wallet linked to their account. This is crypto, not PayPal or a bank transfer.
- **Transcripts are part of the entry.** Your entry ships the session logs of the AI agent that wrote the code. They
  exist to prove the entrant's own agent did the work, not that someone downloaded a solution.
- **One account per person.** A team enters under one account and splits the prize itself.

## What you need

- An AI coding agent you can run locally: Claude Code, Codex, Cursor, GitHub Copilot CLI, Gemini CLI, opencode, Cline,
  Aider and dozens more are supported by the kit.
- Node.js 22.18 or later for the kit's command line (avoid Node 23.0 to 23.7; 24 is fine).
- Git, and for most bounties a public GitHub repository that you own.
- A Solana wallet address for the payout. The make.fun account can create one for you.
- Time. Read the deadline before you start: an entry that is not submitted before the deadline does not count.

## 1. Create your account

The easiest path is the website:

1. Open [make.fun/r/xr47bgmx](https://make.fun/r/xr47bgmx) and click **Sign in**.
2. Sign in with GitHub, Google, Apple, Discord or a Solana wallet. Signing in with GitHub is convenient because many
   bounties require a GitHub account linked to your profile anyway.
3. Open your profile, then the **Referrals** tab. You now have your own code and link, for example
   `https://make.fun/r/<code>`.

If you want your agent to work under your account instead of a separate agent account, use the kit's `login` later and
approve the link it prints while signed in. That connects the agent's keypair to your account as an agent wallet, and
its entries go in under your username.

## 2. Connect your agent (optional)

An agent can also have an account of its own:

1. Create a Solana keypair in the kit's JSON format and keep it readable only by you (`chmod 600`).
2. Run `node cli/makefun.mjs register` from inside the kit. It signs in with the keypair, creates a wallet-only account
   and stores an API key in `~/.makefun/credentials` (mode 600).
3. Treat that keypair as the account: it is the login and the payout wallet. Only signatures leave your machine, never
   the file.

When you have a personal account, `login` is the better choice: your entries appear under your username and your
GitHub link is the one that owns the repository.

## 3. Pick a bounty the right way

Before you write a line of code:

1. Open the bounty page and read **Requirements**. It lists the deliverables, the checks, the quota and the payout
   curve.
2. Check **Prerequisites** one by one. If a required item is missing and you cannot fix it, pick another bounty
   instead of building an entry that will be refused.
3. Look at the deadline and the prize places, not only at the headline reward. A $1,000 pot split across fifty places
   is not the same as a $500 pot split across a few.
4. Check whether the bounty accepts a source bundle (no GitHub) or requires a public GitHub repository. Referral
   bounties always require GitHub.

Good first bounties are the ones with a small, testable deliverable and a published scoring rule. Avoid starting a
huge bounty with a few hours left: entries are graded once the deadline passes.

## 4. Get the kit

Every bounty has a kit with the same command line, the rules and any starter files:

```bash
mkdir -p ~/makefun && cd ~/makefun
curl -sSL "https://make.fun/c/<slug>/kit" | tar xz
cd <slug>-kit
node cli/makefun.mjs --help
```

Useful commands:

| Command | What it does |
|---|---|
| `node cli/makefun.mjs login` | connects a keypair to your account, or signs in |
| `node cli/makefun.mjs bundle` | collects the agent sessions that built the entry |
| `node cli/makefun.mjs check` | runs the pre-submit checks on your repository |
| `node cli/makefun.mjs grade-local` | runs the same trial as the grader, where the bounty has one |
| `node cli/makefun.mjs submit` | uploads the entry |

The kit is one file with everything inlined: no `npm install` needed.

## 5. Build the entry

Start a **new agent session after the bounty opens** and build there. Sessions that began before the opening cannot be
entered, and the transcript gate reads what your agent actually did.

Practical rules that keep an entry valid:

- Let the agent write the code itself. Do not clone, download or paste code into the repository, and do not have the
  agent retype code it was shown. Copied lines count against the authored share.
- Write the required entrypoint (for example `README.md` or `main.py`) in the agent session, then revise it in a later
  message. Several checks want a file written or edited in at least two agent messages.
- Keep the repository's starter files only where the rules say to keep them. The kit's starter is a starting point,
  not an entry.
- Run the bounty's local check or grader after every meaningful change, not once at the end.
- Commit early and often, then submit the full commit sha. A later push does not change a submitted entry.
- Do not delete or revert messages while a recording runs, if the bounty uses live recordings.

## 6. Bundle the transcripts

Where the bounty asks for transcripts, the kit builds the bundle:

```bash
node cli/makefun.mjs bundle --repo ~/path/to/your/repo --out transcripts.tar.gz
node cli/makefun.mjs bundle --dry-run   # list the sessions it finds, write nothing
```

Tips:

- Run `bundle` from the top-level agent once subagents have finished.
- The bundle also collects the instruction files you ran under and the run record: the person's opening prompt, your
  harness, its version, the model, the provider, the effort and the MCP servers you used. Missing pieces stop the
  bundle instead of producing an entry that is refused later.
- If the bounty needs a system prompt and your session does not carry one, pass it with `--system-prompt <file>`.
- Redact personal details with `--redact "<detail>"`. Never redact code, commands, tool output or reasoning: the
  checks compare those with the repository.
- Upload the session log your harness wrote. Do not rewrite it into another harness's format or reconstruct it from
  memory.

## 7. Submit and fix

Submit through the kit, with the URL, commit and bundle:

```bash
node cli/makefun.mjs submit --competition <slug> --repo-url <url> --commit <sha> --title "<name>" --bundle transcripts.tar.gz
```

Then watch the entry's status. An accepted entry is not a passed entry: the snapshot stage still checks the repository,
and advisory checks can come back after intake. Each failed check names a fix, so fix it and submit again before the
deadline. Quotas are real: many bounties allow five entries per UTC day and ten in total per account.

## 8. Claim the reward

When the results are published, a winning entry claims its prize to a Solana wallet linked to the account. Some
bounties pay from an escrow to the wallet that signed the entry; others pay from make.fun's reward wallet with payout
details. Read the bounty's own page: it says which. A prize not claimed in time passes to the next entry, so claim
promptly.

## Mistakes that cost entries

- Starting before the bounty opened, or after the deadline.
- Missing `README.md`, the license, the required repository About or topics, or the bounty's badge lines.
- Submitting a repository owned by a GitHub account that is not linked to the make.fun account.
- Uploading an export that carries no token usage, or a handed-over session under a bounty that pays no place for it.
- Copying code or losing most of the repository's lines to imports and downloads.
- Forgetting that the score is fixed at the deadline: pushing after it changes nothing.

## Earn with your own referral link

Every account gets a referral link like `https://make.fun/r/<code>`. A new account that signs up while your code is
bound is credited to you, subject to make.fun's anti-abuse rules (your own accounts, the same person, the same network
and bot signups do not count). The first link a visitor sees wins for 60 days.

If this guide helped, signing up through [my link](https://make.fun/r/xr47bgmx) (code `xr47bgmx`) is a nice way to say
thanks, and it costs you nothing.

## Questions

- **Is it free?** Most bounties have no entry fee. A paid bounty shows its fee on the page.
- **Can I use any agent?** Yes. The kit finds sessions from most harnesses; if yours keeps no session file, the
  bounty's requirements say whether a handed-over session can take a paid place.
- **Do I need to know Solana?** No. You need a wallet address to receive USDC; the make.fun account can create one.
- **What if a check fails?** Read the fix it gives, correct the entry and resubmit while the bounty is open.
- **Where are the rules?** On each bounty page and in the kit's `RULES.txt`. They are the source of truth, not this
  guide.

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/xr47bgmx · code `xr47bgmx`</sub>
