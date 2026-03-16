---
name: agentcash-onboarding
description: Set up AgentCash for your AI agent — wallet, core skill, and optional invite credits
homepage: https://agentcash.dev
metadata:
  version: 2
---

# AgentCash Onboarding

AgentCash gives your AI agent pay-per-call access to premium APIs via x402 micropayments. This skill guides you through a one-time setup: wallet, optional invite credits, and the core AgentCash skill.

## Onboarding Flow

When the user wants to set up or onboard into AgentCash:

1. **Run onboard** — With or without an invite code:
   - **If the user provided an invite code:** run `npx agentcash@latest onboard <code>` so they get free credits as part of setup.
   - **If no invite code:** run `npx agentcash@latest onboard`. This installs the core skill and prompts the user to open a link to get credits (for example deposit or redeem later).

After that, the user can check balance with `npx agentcash balance`, open the funding flow with `npx agentcash fund`, inspect funding addresses only when needed with `npx agentcash accounts`, or redeem an invite code later with `npx agentcash redeem <code>`.

## Quick Start

### 1. Run onboard (with or without invite code)

**With invite code:**

```bash
npx agentcash@latest onboard <invite-code>
```

**Without invite code:**

```bash
npx agentcash@latest onboard
```

This sets up the wallet and core integration. Without a code, the user will be prompted to open a link to add credits.

### 2. Check your balance

```bash
npx agentcash balance
```

Shows total USDC balance. If balance is 0, direct the user to `npx agentcash fund` or https://agentcash.dev/onboard to get free credits. Only use `npx agentcash accounts` if the user needs deposit links or network-specific addresses.

## Triggers

Use this skill when the user says they want to:

- Set up AgentCash, onboard, or get started with AgentCash
- Use paid APIs with AgentCash and need initial setup
- Install or add the AgentCash skill
- Use an invite code to get credits during setup

## After Onboarding

Once onboarding is done, read the **agentcash** (core) skill for:

- Discovering endpoints: `npx agentcash discover <origin>`
- Making paid requests: `npx agentcash fetch <url>`
- Wallet: balance, fund, redeem, and deposit details

## Support

- **Homepage**: https://agentcash.dev
- **Deposit**: User deposit links are shown in `npx agentcash accounts`
