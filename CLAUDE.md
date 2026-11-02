# WebFactory

A paid service that turns an app idea into a GitHub repo, a deployed AWS environment, a CI/CD pipeline and a live signed-in URL. Customers' apps run in the customer's own AWS account and repos live in the customer's own GitHub (bring your own cloud). This repo is the factory itself, the control plane.

Decisions and the reasoning behind them are in [docs/decisions.md](docs/decisions.md). Read it before changing architecture.

## Stack

- .NET (latest LTS) and Aspire for the factory API, steps and local dev.
- AWS serverless control plane: Lambda, DynamoDB, Step Functions, CodeBuild.
- Terraform for infrastructure, state in S3 with native locking.
- GitHub App (Octokit) for creating repos in customer accounts.
- Region `eu-west-1`.

## Layout

```
docs/                    decisions and runbooks
infra/bootstrap/         control plane, applied once by hand
infra/modules/           reusable Terraform modules (app, factory)
spikes/                  throwaway proofs for Phase 0, deleted or promoted later
src/                     factory code (added from Phase 3)
tests/
```

Only create a folder when it has content.

## Rules

- Backend first. Do not write frontend or UI code, and do not touch any web project, unless the user explicitly asks.
- Every customer-facing role in Terraform or CloudFormation needs a permission boundary and an `appfactory` tag condition.
- No secrets, keys, account IDs for customers or `.tfvars` in git. The GitHub App private key lives in SSM Parameter Store.
- Each run step must be idempotent and unit tested, so Step Functions redrive never creates duplicates.
- Reserved concurrency and throttles on every public Lambda. The account has no hard spend cap.
- Nothing from any employer's internal tools is reused here, in code, names or design.

## Working conventions

- Commands are PowerShell. Give one command at a time with a short explanation. The user runs them.
- Never start long-running dev servers or `aspire start` in the background.
- Do not commit or push unless asked. Work on a branch, not `main`.
- Build the solution before calling a change done.
