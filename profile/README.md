# FDKEY

> Free, MIT-licensed verification middleware that admits AI agents / LLMs and rejects humans, scripts, and weak models.
It's the inverse of CAPTCHA. reCAPTCHA proves you're human; FDKEY proves you're an AI.

One npm or PyPI install, your API key in .env, then a few lines of code marking which tools or routes need verification. 

I am making a project intended for AI agents and I did not find a tool that could easily prove whether someone is an AI agent and not a human. So I made one. The VPS can handle far more traffic than I am using and it costs me peanuts.
Puzzle generators run locally on my PC, so generating more costs nothing. For some I know the answer right away. For some I do not.

Some puzzles can't be easily validated, and for those I capture the answers of LLMs solving them, from which I build a distribution. Before that, they have no influence on the result.
For this reason alone it makes sense to make the tool free for normal use. The more users it has, the stronger it becomes. Puzzles get retired once used enough times and replaced with new ones.

And because it is a security tool to some extent, I believe transparency is in order — you can add me on LinkedIn: https://cz.linkedin.com/in/tomas-chvatal.
A paid tier is not planned for now. If you need higher limits for your API key, just send me a message on Discord or email, tell me what you are using it for, and I will give you larger limits. I just don't want my server to get DDoSed.

FDKEY doesn't mean anything. I registered fdkey.com because it was a nice URL for 11 dollars. If you have a good idea what the FD could stand for, share it on the FDKEY Discord server ([link](https://discord.gg/8BDQxG3sHR)).

One last thing to explain is why the VPS and the puzzle generation are not open source. If they were, you could generate answers for some puzzle types using a script. And if you ran a parallel service, you would split the statistical distribution of the answers, making it weaker.


---

## See it work

### Try as an AI agent

Paste this into Claude Desktop, Cursor, Cline, ChatGPT with MCP, or any agent that can connect to an MCP server:

> Visit `https://mcp.fdkey.com/mcp` and test out the tools — can you complete the FDKEY puzzle challenge to unlock them?

A real model will hit the live demo MCP server, get back the challenge, solve the six puzzles, and unlock the protected tools — all from inside its own MCP client. You'll watch verification happen in real time.

### Try as a human

Head to **[fdkey.com](https://fdkey.com)** and scroll to the Demo section. 15-second clock, 3 random puzzles from the live bank, clipboard locked. Most humans don't pass it. Most LLMs do.

---

## What's here

- **[`sdks`](https://github.com/fdkey/sdks)** — drop-in middleware for MCP servers (TypeScript + Python) and HTTPS APIs (TypeScript). Plus Rust verification primitives. All MIT.

```bash
npm install @fdkey/mcp        # MCP middleware (TypeScript)
npm install @fdkey/http       # HTTPS-API middleware (TypeScript)
pip install fdkey             # MCP middleware (Python / FastMCP)
cargo add fdkey               # Verification primitives (Rust)
```

Get an API key at [app.fdkey.com](https://app.fdkey.com).

---

## How it works

1. Your server uses one of the SDKs.
2. A caller hits a protected route or tool. The SDK returns a challenge — six semantic puzzle types designed so LLMs solve them statistically and humans / scripts can't at speed.
3. The caller submits answers. The SDK forwards them server-to-server to `api.fdkey.com` using your API key (the agent never holds it).
4. The scoring service signs an Ed25519 JWT verifying the caller's capability score and tier. The SDK verifies it offline.
5. The session is marked verified server-side. Subsequent calls pass through; your handler sees the score and tier.

**The agent never holds a token.** Verification stays in the integrator's session. Every agent verifies for itself; there's no cross-agent token transfer.

If the FDKEY scoring service is unreachable, the SDKs default to fail-open — your protected route still serves traffic, just without the verification context. FDKEY is verification, not gating; an FDKEY outage shouldn't brick your workflow.

---

## Why it's free

The puzzle scoring service runs on a single ~$10 / month VPS. If traffic grows to where it warrants paid tiers I'll add some, but the SDKs stay MIT-licensed forever and the entry tier of the scoring service stays free. Every code path in the SDKs is open source — fork, audit, deploy your own copy.
If someone had this as their full time job it would cost more. So if you are reading this using wayback machine in 2040 and ask yourself why it has paid tiers now if the infra is so cheap. It is because I never priced in the work I put in to making this work.

---

## Use cases

Concrete situations where FDKEY is the right tool:

- **MCP servers exposed over HTTP that only AI agents should reach.** Tools that shouldn't be poked at by a script. You have a news feed or coupon you are willing to share with an personal AI agent but not a scraper. 
- **Agentic API gateways.** REST or GraphQL APIs you want to expose to AI agents but not to humans or bots. **Register as AI agent** button. 
- **Agent-only marketplaces.** AgentHub-style platforms, agent freelancer networks. Bidders need to be agents, not people pretending to be agents.
- **Capability-gated dangerous tools.** Some tools shouldn't be reachable by callers below a certain LLM tier. Read `req.fdkey.tier` and gate accordingly.
- **Agent-driven payment rails.** Agent stablecoin wallets, x402-style payments. The prior is overwhelmingly that the caller should be an agent — promotion gaming or pricing-API exploration is the alternative. Future is wild. 

---

## Contributing

I'm a single developer; outside contributions help a lot.

The most-asked-about port today is **Go** — the module path is reserved at `github.com/fdkey/sdks/go` and the wire format is documented; the implementation is up for grabs.

Other welcome contributions:

- New framework adapters (Koa, Hapi, aiohttp, Tower, etc.).
- Edge cases that break the "every agent for itself" model — file an issue with a repro.
- Deployment guides for non-Node frameworks.
- New puzzle type designs. Today the SDKs serve Type 1 (semantic MCQ) and Type 3 (semantic ranking) in production. Types 2, 4, 5, 6 are designed but not deployed.

See [`CONTRIBUTING.md`](https://github.com/fdkey/sdks/blob/main/CONTRIBUTING.md) in the SDK repo.

- I am not asking for any money donations. 

---

## Links

- **Project home / live demo / human challenge**: [fdkey.com](https://fdkey.com)
- **Sign up + manage API keys**: [app.fdkey.com](https://app.fdkey.com)
- **Live demo MCP server**: [mcp.fdkey.com/mcp](https://mcp.fdkey.com/mcp)
- **SDKs**: [github.com/fdkey/sdks](https://github.com/fdkey/sdks)
- **License**: MIT (every file)
