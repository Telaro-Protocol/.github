<!--
  GitHub organization profile README for `Telaro-Protocol`.

  To publish at https://github.com/Telaro-Protocol :
    1. Create a public repo named `.github` under the org
       (github.com/Telaro-Protocol/.github)
    2. Inside that repo, create the path `profile/README.md`
    3. Paste the content below (everything beneath this comment)
    4. Push. GitHub renders it as the org landing page.

  Source of truth lives here. Copy when changes are needed.
-->

<h1 align="center">Telaro Protocol</h1>

<p align="center">
  <strong>A reputation layer for AI agents on Solana.</strong><br/>
  Agents post a USDC bond. If they harm a user, the bond pays the user automatically.
</p>

<p align="center">
  <a href="https://telaro.xyz">Website</a> ·
  <a href="https://github.com/Telaro-Protocol/Telaro-Protocol">Monorepo</a> ·
  <a href="https://www.npmjs.com/package/@telaro/sdk">SDK on npm</a> ·
  <a href="https://github.com/Telaro-Protocol/Telaro-Protocol/blob/main/docs/OVERVIEW.md">Docs</a>
</p>

<p align="center">
  <img width="2172" height="724" alt="Telaro Protocol" src="https://github.com/user-attachments/assets/f3044771-ab84-4fd7-806d-b36840671686" />
</p>

## What is Telaro?

The Solana agent economy already moves real money. Eliza has roughly $25M AUM. SendAI has 100k installs. Drift is delegating capital to bots. And nobody has solved the simplest question: which agent should you trust with your money?

Right now every DApp writes its own allowlist. Every user trusts a Twitter screenshot. A bot that rugs people on Monday is back on Wednesday with a new name, because reputation lives off-chain and costs nothing to throw away.

Telaro fixes that. Agents post a USDC bond at the program level. If they harm a user, the bond pays out automatically through a 7-day dispute lifecycle. DApps can require a minimum bond and a minimum score before delegating capital, and they do it with a single CPI call into the Telaro program. Reputation becomes something you can verify on chain.

## For agent operators

You run an agent (a trading bot, a Drift LP, a SendAI agent, anything that custodies user capital). You bond up at [telaro.xyz/builder](https://telaro.xyz/builder).

1. Connect a wallet. Post 100 USDC or more.
2. Mint a soulbound AgentID NFT (Metaplex Core). Your Trust Card is generated and updates as your score moves.
3. Log every action through `record_action`. The indexer scores you on tenure, success rate, dispute count, and how much bond you carry per dollar of volume.

Your bond earns yield while it sits. The first 50% of the gross yield is yours, the protocol keeps the other 50%. At a high score you also unlock the boost ladder: you put up less of your own USDC and the pool fronts the rest at 3 to 6% APY.

## For DApp builders

Gating capital by reputation is one Anchor CPI in your own program:

```rust
use telaro::cpi::{view_bond, accounts::ViewBond};

view_bond(
    CpiContext::new(telaro_program, ViewBond { agent: agent_account.into() }),
    1_000_000_000u64,  // min_bond = 1000 USDC
    700u16,            // min_score
)?;
// Reaching here means the agent meets your policy. Delegate safely.
```

The call reverts atomically if the agent is under-bonded, low-scored, or has been frozen by an active dispute. No off-chain allowlist, no signature games. The same SDK works from a server, a wallet, or directly inside another Anchor program.

```bash
npm install @telaro/sdk @solana/web3.js @solana/spl-token
```

We also ship typed adapter packages so most integrations are a few lines:

```bash
npm install @telaro/sendai        # Solana Agent Kit
npm install @telaro/eliza         # ElizaOS
npm install @telaro/langchain     # LangChain
npm install @telaro/llamaindex    # LlamaIndex
npm install @telaro/openai-agents # OpenAI Agents
# Also: @telaro/autogen, @telaro/crewai, @telaro/buzz, @telaro/goat
```

```bash
npm install @telaro/jupiter    # swaps
npm install @telaro/marginfi   # lend / borrow
npm install @telaro/drift      # perps
npm install @telaro/kamino     # vaults
npm install @telaro/voltr      # yield strategies
```

Full setup guide: [INSTALL.md](https://github.com/Telaro-Protocol/Telaro-Protocol/blob/main/INSTALL.md).

## For end users

You used a bonded agent and something went wrong. Go to [telaro.xyz/disputes](https://telaro.xyz/disputes), click the agent, file a claim. You pay a 5% deposit and submit an evidence link. The operator has 7 days to respond.

* If they accept, the bond pays you and your deposit comes back.
* If they reject, you can escalate. The protocol arbiter rules within 7 days.
* If the operator ghosts the full window, anyone can crank the timeout and you get paid anyway.

The full claim history is public on the disputes board. Operators cannot hide a dispute by rejecting it. Every claim leaves a permanent row.

## For underwriters

If you want yield, deposit USDC into the singleton underwriter pool at [telaro.xyz/restake](https://telaro.xyz/restake). You receive stUSDC LP tokens. The pool earns from arbiter fees on resolved claims, slashing penalties retained by the treasury, and the protocol's share of bond float yield. When a slashing event exceeds an agent's own bond, the pool absorbs the gap, and that's how your stUSDC can move down. The premium share is what you are paid for taking that risk.

## The numbers (for investors)

Five live revenue lines on devnet, plus one Q3 launch target.

| Line | What it is |
|---|---|
| Bond float yield | 50% protocol take on bond USDC routed to Kamino + MarginFi |
| Arbiter fees | 2% of `claimed_amount` on resolved escalations |
| Slashing penalties | 1% on accept path + 1% on reject path |
| Pool take | 30% of underwriter pool yield flow |
| Pro / Enterprise API | $99 / $499 monthly tier for DApp integrators (Q3 target) |

ARR scenarios driven by bonded TVL and pool TVL. Formulas live on [telaro.xyz/pricing](https://telaro.xyz/pricing).

| Year | Bonded TVL | Pool TVL | ARR |
|---|---|---|---|
| 2026 | $2M | $1M | $80k |
| 2027 | $10M | $10M | $535k |
| 2028 | $50M | $100M | $4.2M |

The growth driver is the pool, not the bond. Bond TVL has a natural ceiling tied to how many agents exist. Pool TVL scales with any LP willing to take on slashing risk for yield, and the protocol takes 30% of the pool's flow.

## What's actually shipped

40 instructions live on devnet. Registration, bonding, action logging, claims, escalation, the arbiter, yield routing, the tsUSDC receipt mint, the underwriter pool, the score feed, and the credit ladder. The full bankrun test suite (37 tests) runs in about 3 seconds.

| Component | Status |
|---|---|
| Anchor program (40 ix) | Live on devnet |
| `@telaro/sdk` | Published on npm |
| Indexer + scorer | onLogs + Helius webhook into SQLite |
| AgentID NFT | Metaplex Core soulbound |
| Underwriter pool | Real deposit / withdraw on devnet |
| 37 of 37 bankrun tests | Passing |
| Real Kamino + MarginFi yield CPI | Scripted oracle on devnet, real CPI coming with mainnet |
| Jito NCN registration | Scaffold in `programs/telaro_ncn`, integration after Jito governance pass |
| Mainnet program ID | Reserved, holding for audit |

## Repo layout

Everything lives in [Telaro-Protocol/Telaro-Protocol](https://github.com/Telaro-Protocol/Telaro-Protocol).

| Layer | What it is |
|---|---|
| `programs/telaro` | Anchor program. 40 instructions. |
| `sdk/` | TypeScript SDK. Published as `@telaro/sdk`. |
| `indexer/` | Off-chain reputation engine + WebSocket API. |
| `web/` | Next.js dashboard. Leaderboard, Trust Card, builder console, boost ladder, restake. |
| `packages/` | 16 framework and DApp adapters. |

## Verify your install

```ts
import { TelaroClient, MIN_BOND_USDC, PROGRAM_ID } from "@telaro/sdk";

console.log(PROGRAM_ID.toBase58());
// 3DUrvVWEziYLtEbiDtfxqh1ioXRFX6DNvV4iTsGed2rs (devnet)

console.log(MIN_BOND_USDC);
// 1000000n (1 USDC, 6 decimals)
```

## Status

Devnet is live. SDK is on npm. Mainnet deploy is scheduled after the audit pass.

Built for the Solana Frontier Hackathon 2026, AI / Agent Economy track with Metaplex Core.

## Contact

- Web: [telaro.xyz](https://telaro.xyz)
- Email: `admin@idolly.ai`
- X: [@telaroai](https://x.com/telaroai)

<sub><a href="https://github.com/Telaro-Protocol/Telaro-Protocol">Read the code</a> · <a href="https://telaro.xyz">Open the demo</a></sub>
