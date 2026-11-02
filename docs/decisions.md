# Decisions

Agreed on 2026-10-30. Source plan: AppFactory Launch Plan (private Claude artifact).

## Product

- **Primary segment:** agencies, consultancies and small teams that start many small apps. Free tier for individual developers as top of funnel.
- **First template:** .NET minimal API plus React/Vite SPA on Lambda. A TypeScript template comes later if interviews ask for it.
- **Price hypotheses (to test, not final):** Free (1 app), Pro about 19 EUR/month (5 apps), Team about 79 EUR/month (25 apps).

## Architecture

- **Control plane:** AWS serverless in the owner's account (Lambda, DynamoDB, Step Functions, CodeBuild), region `eu-west-1`.
- **Customer apps:** deployed into the customer's own AWS account through a cross-account role (bring your own cloud).
  - Role trusts only the control-plane account and requires a per-customer external ID.
  - Permission boundary plus `appfactory` tag condition limit what it can touch.
  - Terraform state lives in a bucket in the customer's account.
- **Customer repos:** created in the customer's GitHub account or org through a GitHub App.
- **Targets:** `ITargetProvider` from the start with `AwsTarget` as the only implementation, so other clouds are new classes later.
- **Edge:** Cloudflare free plan for DNS, TLS and static hosting.
- **Hosted tier:** deferred until the beta shows demand. Options: Cloudflare Workers for Platforms (TypeScript) or one AWS account per customer.

## Business

- **Country:** Portugal. Confirm entity type (self-employed vs Lda) and VAT treatment with a contabilista.
- **Payments:** merchant of record first (Paddle, Polar or Lemon Squeezy) so cross-border VAT is handled for us. Revisit near 5,000 EUR/month revenue.
- **Employer:** read the employment contract (IP, side-business, exclusivity) before building further.
- **Effort:** about 10 hours a week, about 28 weeks to public launch. First revenue after Phase 4 via the CLI. UI (Phase 5) starts only on the owner's go-ahead.

## AWS account

- Free Plan account for Phases 0 to 2 only. It closes after six months or when credits run out. Upgrade to the Paid plan before the private beta.
- A second AWS account (a new email works) is needed to stand in for a customer in the cross-account spike.
- Enable root MFA and create an admin role before any other work. Add an AWS Budget alarm.
