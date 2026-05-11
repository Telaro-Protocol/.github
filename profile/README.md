<!--
  GitHub organization profile README for `telaro-protocol`.

  To publish at https://github.com/telaro-protocol :
    1. Create a public repo named `.github` under the org
       (github.com/telaro-protocol/.github)
    2. Inside that repo, create the path `profile/README.md`
    3. Paste the content below (everything beneath this comment)
    4. Push — GitHub renders it as the org landing page

  Source of truth lives here; copy when changes are needed.
-->

<p align="center">
  <img src="https://telaro.xyz/logo.svg" width="128" alt="Telaro" />
</p>

<h1 align="center">Telaro Protocol</h1>

<p align="center">
  <strong>The bonded reputation layer for AI agents on Solana.</strong><br/>
  <em>AI agents lie. USDC bonds don't.</em>
</p>

<p align="center">
  <a href="https://telaro.xyz">Website</a> ·
  <a href="https://telaro.xyz/leaderboard">Live devnet</a> ·
  <a href="https://github.com/telaro-protocol/agent-reputation-layer">Monorepo</a> ·
  <a href="https://github.com/telaro-protocol/agent-reputation-layer/blob/main/docs/OVERVIEW.md">Docs</a>
</p>

---

## What is Telaro?

Telaro is a Solana primitive that makes AI agents stake their own capital before touching yours.

- **Agents post a USDC bond.** Slash on misbehavior, claim on dispute, withdraw on a 30-day cooldown.
- **DApps gate capital with one CPI call.** *"Only let agents with score > 700 LP into our pool"* becomes one line of Anchor code.
- **Users get auto-paid on disputes.** When an agent fails, the bond pays the victim — not a reputation badge that says "sorry".

Identity layers (Metaplex MIP-13) and payment rails (MCP, x402) shipped in 2025. What's missing is **accountability**. Telaro is that missing primitive.

## The monorepo

Everything ships in one place — [**telaro-protocol/agent-reputation-layer**](https://github.com/telaro-protocol/agent-reputation-layer).

| Layer | What it is |
|---|---|
| `programs/telaro` | Anchor program — bond escrow, slashing, claims, on-chain scores |
| `sdk/` | `@telaro/sdk` — TS client (PDAs, borsh decoders, REST, score math) |
| `indexer/` | Off-chain reputation engine + WebSocket API |
| `web/` | Next.js dashboard — leaderboard, credit ladder, restake, disputes |
| `packages/` | 16 framework / DApp / UI adapters |

## Install

```bash
npm install @telaro/sdk @solana/web3.js @solana/spl-token
```

**Framework adapter** — makes your agent auto-record every action:

```bash
npm install @telaro/sendai        # Solana Agent Kit
npm install @telaro/eliza         # ElizaOS
npm install @telaro/langchain     # LangChain
npm install @telaro/llamaindex    # LlamaIndex
npm install @telaro/openai-agents # OpenAI Agents
# + @telaro/autogen, @telaro/crewai, @telaro/buzz, @telaro/goat
```

**DApp adapter** — gate capital by score, auto-record outcomes:

```bash
npm install @telaro/jupiter    # swaps
npm install @telaro/marginfi   # lend / borrow
npm install @telaro/drift      # perps
npm install @telaro/kamino     # vaults
npm install @telaro/voltr      # yield strategies
```

Full install guide → [INSTALL.md](https://github.com/telaro-protocol/agent-reputation-layer/blob/main/INSTALL.md).

## Verify

```ts
import { TelaroClient, MIN_BOND_USDC, PROGRAM_ID } from "@telaro/sdk";

console.log(PROGRAM_ID.toBase58());
// → 3DUrvVWEziYLtEbiDtfxqh1ioXRFX6DNvV4iTsGed2rs (devnet)

console.log(MIN_BOND_USDC);
// → 1000000n  (1 USDC, 6 decimals)
```

## Status

- **Devnet**: live — program `3DUrvVWEziYLtEbiDtfxqh1ioXRFX6DNvV4iTsGed2rs`
- **Mainnet**: program ID reserved, scheduled deploy post-review
- **Submission**: Solana Frontier Hackathon 2026 (Accelerator / Standout / Metaplex side tracks)

## Contact

- Web: [telaro.xyz](https://telaro.xyz)
- Email: `hi@telaro.xyz`
- Twitter / X: [@telaroxyz](https://twitter.com/telaroxyz)
- Founder: `develop@idolly.ai`

---

<sub>Built for the agent economy that's now arriving. <a href="https://github.com/telaro-protocol/agent-reputation-layer">Read the code</a> · <a href="https://telaro.xyz">Open the demo</a></sub>
