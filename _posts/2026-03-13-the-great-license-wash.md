---
layout: post
title: "How Coding Agents Are Empowering Open Source But Can Turn Thier Best Practices Against Them"
date: 2026-03-13
---

*Your tests are your spec. Your spec is all an agent needs. Your license may be at risk.*

**TL;DR:** The threshold for reconstructing software solely from its test suite has dropped dramatically. What once required hundreds of millions in justification, a small team with API credits can now attempt. **To test this, I deleted all implementation code from two popular open-source projects (100K+ GitHub stars each) and pointed a coding agent at their test suites with one objective: make the tests pass.** It rebuilt a CLI-based AI coding agent to 99.8% across 11,000+ tests and a whiteboarding app to 95%. The generated code has minimal overlap (1–10%) with the originals outside of spec-driven boilerplate — and in a clean room setup, can potentially be considered entirely new code, free from the original license.


**Note:** *The views and opinions expressed in this post are my own and do not reflect those of my employer.*

---

## The Double-Edged Sword

Coding agents are changing what's possible in software development. A single developer with the right tools can now accomplish what once required a team of ten. For open-source projects — many of which are maintained by small, resource-constrained teams - this is genuinely exciting. Agents can help maintainers triage issues, scaffold tests, migrate APIs, and keep up with the relentless pace of modern software.

But the same capabilities that help build open source can also be turned against it. And the threat isn't just the flood of AI-generated spam contributions that's already burning out maintainers. There's a quieter, more structural problem emerging - one that strikes at the heart of how open-source licensing works.

I've been running an experiment that demonstrates it.

## The Experiment

I took a popular open-source whiteboarding app — MIT-licensed, 100K+ GitHub stars, 150k+ lines of code, with a rich React-based editor - stripped out every implementation file, and pointed a coding agent at the remaining test suite. The instruction was simple: make the tests pass.

I'm intentionally not naming the specific projects I reconstructed. The point of this post is the systemic implications, not the individual projects - and naming them would make this about those maintainers rather than about the structural problem facing all of open source.

No reading the original source code. No architecture docs. Just: here are 121 test files, figure out what the code should do.

Here's where the project stands today:

| Package | Pass | Fail | Total | Status |
|---------|------|------|-------|--------|
| math | 26 | 0 | 26 | Complete |
| common | 77 | 0 | 77 | Complete |
| element | 416 | 0 | 452 | Complete |
| utils | 24 | 0 | 26 | Complete |
| editor | 681 | 12 | 702 | 97% |
| app | 5 | 0 | 5 | Complete |
| **Total** | **1,229** | **12** | **1,288** | **~95%** |

The 12 remaining failures are all in a single test file dealing with multiplayer conflict reconciliation - one of the most complex areas of collaborative editing. Every other test file is green.

But the numbers only tell part of the story. Here's what actually happened, visually.

The screenshot below is what the agent produced when its only goal was to make the tests pass. No styling instructions. No design guidance. Just: satisfy the test assertions.

<img src="/blog/images/v0-raw-ui-unstyled-toolbar-basic-rectangle.png" width="600" alt="v0 — Raw HTML, unstyled toolbar" />

*Raw HTML buttons, unstyled form controls, plain text labels. But it works — you can draw shapes, select tools, change colors. The tests ensured the functionality was there.*

That's what test-driven reconstruction gets you: correct behavior with no visual polish. The drawing engine, the shape model, the tool switching — all functional, because that's what the tests specified. Everything the tests *didn't* specify — icons, layout, spacing, visual design — is completely absent.

Now here's what happened after a single follow-up prompt asking the agent to make the UI look more polished:

<img src="/blog/images/v4-context-menu-multiple-shapes-artist-style.png" width="600" alt="Polished UI — icon toolbar, color palette, context menu, multiple shapes" />

*Icon toolbar, full color palette, context menus, selection handles, multiple shape types — visually close to the original app. I never showed the agent any screenshots of the original UI, though we can't rule out that the model's training data included images of similar open-source tools.*

The point isn't the power of a single prompt. It's that passing the tests already did the hard work - building the functional foundation. Once the core behavior was in place, the UI was a tractable problem. The tests gave the agent the spec; the rest was polish.

To stress-test the approach on a larger codebase, I ran a second experiment against a popular open-source coding CLI - Apache-2.0 licensed, also 100K+ stars, with over 11,000 tests across a TypeScript monorepo:

| Package | Pass | Fail | Total | Pct |
|---------|------|------|-------|-----|
| core | 5,052 | 6 | 5,084 | 99.9% |
| cli | 5,829 | 9 | 5,842 | 99.8% |
| server | 102 | 0 | 102 | 100% |
| sdk | 16 | 0 | 16 | 100% |
| ide-extension | 40 | 0 | 41 | 100% |
| **Total** | **11,054** | **17** | **~11,200** | **~99.8%** |

The 17 remaining failures are a mix of integration tests with hardcoded environment assumptions, platform-specific tests not suited to the development environment, and a couple of timing-sensitive edge cases.

And just like the whiteboarding app, the reconstructed coding agent actually works. Here's its terminal UI running a real prompt:

<img src="/blog/images/12-cli-tui-running-explain-code-repo-blurred.png" width="600" alt="Rebuilt coding agent TUI — accepting and processing a prompt" />

*The rebuilt terminal interface, complete with ASCII art logo, processing a real query. The agent reads the prompt, explores the repository, and generates a response.*

And here's what's under the hood — the full set of tools the reconstructed agent registered:

<img src="/blog/images/15-cli-tui-tools-command-blurred.png" width="600" alt="Rebuilt coding agent — /tools command showing all registered tools" />

*The `/tools` command listing all nine built-in tools — file editing, search, shell execution, web fetching. This isn't a test-passing stub. It's a working coding agent.*

The point of these experiments isn't to show that you can one-shot a perfect clone. Let me be honest about what these reconstructions actually are:

- *Is the output perfect?* No.
- *Does it have bugs?* Definitely.
- *Does it run slow?* Yes.
- *Is this something you should attempt as a small team on a weekend?* Absolutely not.
- *Could a company that sees commercial value in bypassing a license do this?* **Absolutely yes.**

**The threshold for reconstructing software from its test suite has dropped dramatically.** This is no longer a project that requires a large tech firm with thousands of engineers — a project that would only be justified if it had the potential to generate or save hundreds of millions of dollars. A small team with API credits can now attempt what used to be economically unthinkable. The result won't be perfect, but it'll be a starting point that would have been impossibly expensive to reach just two years ago.

## Why Someone Would Do This

When a company wants to use an open-source library but its license is too restrictive, the options have traditionally been: comply, negotiate a commercial license, or build from scratch. That third option used to be prohibitively expensive — only viable for the largest companies facing the largest stakes. Coding agents have changed that calculus entirely.

**The License Wash** — using an agent to reconstruct software from its test suite, producing technically original code that replicates the behavior of the original without copying its implementation. No attribution required? No license file to carry forward?

The whiteboarding app I reconstructed is MIT-licensed — already one of the most permissive licenses in open source. If this works against MIT, the implications for more restrictive licenses are even more significant.

## How I Did It

For both projects, the process followed the same pattern:

1. Clone the original repository
2. Delete every implementation file - keep only tests, snapshots, mocks, and configs
3. Point a coding agent at the test suite and let it work bottom-up through the dependency graph — starting with pure utility libraries and building toward the full application layer

The resulting code is structurally different from the originals, often architecturally different, but functionally equivalent where the tests can verify.

## What Tests Don't Tell You

Before going further, it's important to be honest about the limitations here. Passing all the tests doesn't mean you've perfectly replicated the original software.

**Non-functional requirements are the biggest gap.** Unit tests overwhelmingly verify functional behavior — given input X, expect output Y. They rarely test performance characteristics, memory footprint, thread safety, or algorithmic complexity. An agent-generated implementation could pass every test while using an O(n²) algorithm where the original uses O(1). Under load, that difference could be catastrophic.

**Tests suffer from survivorship bias.** They only cover the edge cases the original developers thought to write down. Undocumented behaviors, silent fallback mechanisms, and subtle security guardrails that exist in the implementation but aren't explicitly asserted in any test file — all of these would be missed by an agent working purely from the test suite.

**Maintainability is a real concern.** Agent-generated code that passes tests may not be idiomatic, readable, or easy for human engineers to maintain. An organization adopting a reconstructed codebase could be trading licensing costs for technical debt.

That said, the same agents that built the reconstruction can also maintain it - monitoring upstream repos, selectively cherry-picking bug fixes and features. Unlike traditional forks that either keep up with everything or gradually rot, an agent-maintained fork can be selective.

And critically: you don't need a flawless clone to undermine a licensing model. Getting 80–90% of the way there — with the remaining gaps being tractable engineering problems - is still dramatically cheaper than building from scratch, and still enough to change the calculus around whether to pay for a commercial license.

## Which Repos Are Most Exposed

Not all repositories are equally vulnerable. The key factors are test density (how many tests per line of source code - a high ratio means proportionally *less* implementation to generate), API test coverage (what percentage of public APIs appear in the test suite), and language ecosystem (TypeScript and Python tests read like specifications; C++ and Go tests are harder for agents to parse).

I analyzed 15 major repositories and scored them using a composite **Test Coverage Score** combining API coverage with test density, out of 100. Caveats apply - these are static proxies, not certainties, but the patterns are informative:

| Repository | Language | License | Lines of Code | Test LOC/Total LOC | API Test Coverage | Test Cases/1K Source LOC | Coverage Score |
|------------|----------|---------|---------------|--------------------|-------------------|--------------------------|-----------------|
| flutter | Dart | BSD-3-Clause | 3,255,286 | 33.51% | 79.02% | 10.11 | 49.6 |
| n8n | TypeScript | Sustainable Use | 2,186,201 | 38.61% | 39.81% | 23.68 | 43.6 |
| pytorch | Python/C++ | BSD-3-Clause | 3,689,335 | 30.09% | 39.47% | 13.51 | 33.2 |
| langchain | Python | MIT | 330,295 | 38.39% | 22.41% | 17.52 | 28.7 |
| React | JavaScript | MIT | 722,543 | 45.73% | 24.74% | 14.63 | 27.0 |
| TypeScript | TypeScript | Apache-2.0 | 4,153,968 | 88.88% | 43.12% | 4.89 | 26.5 |
| go | Go | BSD-3-Clause | 3,218,362 | 19.06% | 40.60% | 3.57 | 23.9 |
| langflow | Python | MIT | 476,409 | 40.25% | 16.01% | 14.83 | 22.8 |
| llama.cpp | C++ | MIT | 625,719 | 6.08% | 40.34% | 0.27 | 20.4 |
| electron | C++ | MIT | 216,514 | 26.59% | 6.14% | 16.15 | 19.2 |
| excalidraw | TypeScript | MIT | 164,616 | 24.05% | 17.11% | 9.39 | 17.9 |
| opencode | TypeScript | MIT | 230,511 | 15.56% | 17.25% | 7.99 | 16.6 |
| transformers | Python | Apache-2.0 | 1,532,443 | 25.19% | 4.47% | 9.70 | 11.9 |
| rust | Rust | MIT / Apache-2.0 | 3,710,282 | 35.62% | 11.91% | 5.82 | 11.8 |

### What the Coverage Score Actually Predicts

The coding agent scores 74.8, the highest in this set. The whiteboarding app scores 17.9. But lower test density doesn't mean a lower pass rate — it means the agent has to work harder.

> **Although the whiteboarding app is significantly smaller, reconstructing it consumed roughly the same token usage as the much larger coding agent.** The agent spent far more effort per test navigating ambiguity. It still reached 95%.

The score predicts *cost and difficulty*, not whether reconstruction will succeed. A high score means a clear blueprint. A low score means more guessing and backtracking, but the agent gets there anyway.

### The Best Practices Paradox

The uncomfortable pattern: **the projects that invest the most in comprehensive testing - the ones we celebrate as well-maintained — publish the most complete blueprints for reconstruction.** Every best practice in software engineering - write thorough tests, aim for high coverage, document edge cases in assertions, simultaneously makes a project more vulnerable to this kind of approach. Projects with sparse test coverage are paradoxically harder to clone, not because they're better protected, but because there's less specification to work from.

My colleague Michael Bleigh [made a similar observation](https://mbleigh.dev/posts/slop-forks/) when writing about what he calls "slop forks" — agent-generated reconstructions of open-source projects. As he put it, the moat became the drawbridge: the very engineering rigor that makes a project robust also makes it legible to agents.

## The Legal Gray Area

I'm not a lawyer, and some of the legal questions here are genuinely unresolved. For permissive licenses like MIT and Apache-2.0, reconstruction from tests, if done in clean room setup with two teams, in my opinion, possibly produces code with no licensing tie to the original. For copyleft licenses like GPL and AGPL, the derivative work question is murkier and untested in court.

To quantify this, I ran academic plagiarism detection (copydetect) on the generated code against the originals. The whiteboarding app showed just **1.3% overlap**. The coding CLI showed **13.7%**, concentrated in spec-driven boilerplate like config schemas and OAuth flows where any independent implementation would converge. **Both projects pass 95–99.8% of tests while producing overwhelmingly independent code.**

The **dual-licensing business model** faces the most direct pressure. Many open-source businesses offer a free copyleft version alongside a paid commercial license - that's their revenue model. If a company can point an agent at the public test suite and reconstruct a functionally equivalent version with no licensing strings attached, the incentive to pay for a commercial license erodes significantly. This is especially true for internal tools that never get distributed, where even the copyleft question becomes moot.

**Again, just to clarify, I am not saying companies should start doing this. I am just highlighting the risk that this could start happening lot more often now.**

## The Flip Side: Insurance Against License Changes

This dynamic cuts both ways.

When HashiCorp switched Terraform from the Mozilla Public License to the Business Source License in August 2023, the community was furious. Within two weeks, over 100 companies and 400 individuals pledged to create OpenTofu, a fork that would keep Terraform open. Community contributions to Terraform plummeted from 21% to 9% of pull requests.

OpenTofu forked the existing codebase. But with today's agents, a community wouldn't necessarily need the old code - just the test suite from before the license change. They could rebuild a fresh version from behavioral specifications alone, potentially faster than a traditional fork.

This reframes the license wash as something more nuanced than a pure threat. It's also a form of insurance against corporate capture. If a company rug-pulls a community project, the community has a faster, more flexible path to rebuilding — and with agents maintaining the fork, they can selectively absorb upstream improvements while charting their own course.

## What Comes Next

I don't have clean solutions. The problems are structural. But a few observations seem worth making.

**Tests are becoming a form of IP.** If your test suite fully describes your software's behavior, you've published something closer to a specification than a quality assurance tool. Projects may need to think more carefully about what their test suites reveal — or accept that behavioral specifications, not implementations, are the real asset worth protecting. Some projects are already responding: tldraw [moved their test suite to a private repository](https://mbleigh.dev/posts/slop-forks/) in early 2026, treating tests as proprietary even while keeping the source code open.

**Licensing frameworks face new pressure.** The legal structures governing open source were designed for a world where reimplementation was expensive enough to be self-limiting. Whether or not current licenses technically cover test-driven reconstruction, the practical barrier has shifted. The community will need to grapple with this.

**The verification bottleneck is the defining problem.** The cost of producing code is dropping fast. The cost of verifying code isn't. Every downstream challenge — spam contributions, license washing, supply chain risks — traces back to this growing asymmetry between the cost of creation and the cost of review.

---

Both experiments have reached a point where the results speak for themselves. The whiteboarding app reconstruction climbed from zero to 95% — with a working, visually recognizable whiteboard produced entirely from test specifications. The coding agent reconstruction hit 99.8% across 11,000 tests — with its interactive terminal UI working end-to-end.

**The question isn't whether agents can reconstruct software from its tests. These experiments suggest they can — imperfectly, but meaningfully.**

**The question is what the open-source community does with that knowledge.**

---

## References

- Michael Bleigh, [*Welcome to the age of the Slop Fork*](https://mbleigh.dev/posts/slop-forks/) (February 2026)
- Cloudflare, [*How we rebuilt Next.js with AI in one week*](https://blog.cloudflare.com/vinext/) (February 2026)
- tldraw, [*Move tests to closed source repo*](https://github.com/tldraw/tldraw/issues/8082) (February 2026)
