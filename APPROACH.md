# Approach

Last updated: 2026-10-06

## Goal

I build a portfolio of web apps and libraries.
Agents write the code. I define what to build and I approve the result.

## Principles

1. **Specs are the source.** Each feature has a Markdown spec. Agents read the spec and write the code.
2. **Write little by hand.** I describe ideas in a prompt. Agents write the specs and the code. I review and approve.
3. **Everything lives on GitHub.** Code, specs, reviews, and releases are all public and have a history.
4. **Automate the full cycle.** A feature goes from idea to production with no manual steps, except my approval.
5. **Trust comes from review.** A different agent reviews each change and tries to find problems.
6. **A human makes the final decision.** I approve each feature one time: the spec and the code together, in one pull request.

## The Cycle

1. **Define.** I describe a feature to an agent. The `/spec` skill writes `specs/NNN-feature-name.md`, shows it to me, and pushes it on the branch `build/NNN-feature-name`. It opens no pull request.
2. **Build.** The push starts a GitHub Action. A build agent reads the spec, adds the code to the same branch, and opens one pull request with the spec and the code. A bot opens it, so I am never the author of a pull request.
3. **Review.** A review agent does an adversarial review of the pull request against the spec.
4. **Fix.** If the reviewer requests changes, the build agent fixes them. After 3 rounds, the loop stops and the pull request gets the `needs-human` label.
5. **Verify.** CI runs on each push.
6. **Approve.** I approve and merge the pull request when I am satisfied with the spec and the code.
7. **Ship.** CD releases a new package version or deploys the web app.

I can stop a build while it runs, if the spec is wrong.

## The Review Agent

- It uses a different model from the build agent: Claude Sonnet reviews, and Claude Opus builds. So the two agents make different mistakes.
- It has a limited tool set. It can read code and post a review, but it cannot change files or push. A guard detects a push during the review.
- It sees only the spec, the diff, and the repo. It does not see the reasoning of the build agent.
- It looks for bugs, gaps between the spec and the code, missing tests, and security problems. It does not praise the code.

## Automation

| Step | Done by | Trigger |
|---|---|---|
| Spec | `/spec` skill, from my prompt | My prompt |
| Build | Build agent (`build.yml`) | Push of a new spec to a `build/*` branch |
| Review | Review agent (`review.yml`) | Pull request opens or changes |
| Fix | Build agent (`fix.yml`) | Reviewer requests changes |
| Tests | CI (`ci.yml`) | Each push to a pull request |
| Merge gate | GitHub branch protection | CI must pass, and I must approve. Nobody can bypass it. Agents cannot merge. |
| Release or deploy | CD (`release.yml` or `deploy.yml`) | Merge to `main` |

### Structure

- **Account:** All repos are in my personal GitHub account, `leanucci`. I can transfer them to an organization later.
- **Central repo (`leanucci/workflows`):** It is public. It has these parts:
  - `.github/workflows/`: the reusable workflows. Projects call them by reference, so a fix in one place applies to all projects.
  - `prompts/`: the agent prompts. The workflows use them by reference. `prompts/common.md` has the rules for all agents, in CI and on my computer.
  - `skeleton/`: the start files for a new project. The agent that creates a project copies them one time.
  - `stacks/`: the start files and rules for each stack.
  - `bin/setup-repo`: a script that sets the labels and branch protection of a new repo.
  - `skills/`: the Claude Code skills for my side of the cycle. `/new-project` creates a project. `/spec` turns my idea into a spec.
- **Project repos:** Each project contains one caller workflow (`agent.yml`), the CI and release callers of its stack, and its own files: specs, `CLAUDE.md`, and `CHANGELOG.md`. `agent.yml` has all triggers and permissions, so changes to the central repo need no change in the projects.
- **Secrets:** A personal account has no shared secrets. The agent that creates a project sets its secrets with `gh secret set`.
- **Models:** The build and fix agents use Claude Opus. The review agent uses Claude Sonnet.
- **Local copies:** All repos are in subfolders of `/Users/lean/work`.

### Stacks and Targets

- I choose the stack and the deploy target for each project in the project prompt. The agent records them in the project `CLAUDE.md`.
- Libraries go to the package registry of their language, for example RubyGems for Ruby.
- The central repo gets workflows for a stack or a target when the first project needs it.

### Versions

- Libraries use semantic versions.
- Each spec has a "Release" field: `major`, `minor`, `patch`, or `none`. The agent selects it from my prompt. I check it when I review the pull request.
- The build agent changes the version number and adds an entry to `CHANGELOG.md` in the same code pull request.
- When the version number changes on `main`, `release.yml` creates a git tag and publishes the package.
- Web apps deploy on each merge to `main`. They do not need a version number.

### Technical Notes

- Agents use the Claude GitHub App or a separate token. A pull request that a workflow opens with the default token does not start other workflows.
- Each build, review, and fix round costs API money. The 3-round limit controls this cost.

## Projects

[PROJECTS.md](PROJECTS.md) lists all projects in the portfolio.

- Existing projects can join the portfolio before they use the agent cycle.
- Move an existing project to the cycle only after the cycle works on a new project. Make each move a spec.

## Open Decisions

None at this time.

## Change Log

- 2026-10-01: First version.
- 2026-10-01: The cycle starts when a spec file merges to `main`. Added a spec approval step.
- 2026-10-01: Agents write specs from my prompt. Added the review agent rules, the fix loop, the automation table, and the repo structure.
- 2026-10-01: Use the personal account `leanucci`, not an organization. Each repo gets its own secrets.
- 2026-10-01: The stack is chosen for each project. Stack workflows are added when needed.
- 2026-10-01: Deploy targets are chosen for each project. The spec sets the version change. Releases need no extra pull request.
- 2026-10-01: Added `bravo` and `wsaa-ruby` to the portfolio, in `PROJECTS.md`. They move to the agent cycle later.
- 2026-10-01: Renamed the file from `MANIFESTO.md` to `APPROACH.md`.
- 2026-10-01: Removed the separate template repo. The start files are in `skeleton/` in `leanucci/workflows`.
- 2026-10-01: Wrote the first version of `leanucci/workflows`. Shared agent rules moved to `prompts/common.md`. Added the models.
- 2026-10-01: A bot opens spec pull requests, so I can approve them. Branch protection has no admin bypass.
- 2026-10-01: Added the `/spec` and `/new-project` skills.
- 2026-10-05: The review agent now uses Google Antigravity (Gemini), a different provider from the build agent.
- 2026-10-06: One pull request for each feature, with the spec and the code. No spec pull request. One caller workflow for each project.
- 2026-10-06: Removed the Antigravity reviewer. The Gemini API failed with capacity and billing errors. Claude is the only reviewer again.
