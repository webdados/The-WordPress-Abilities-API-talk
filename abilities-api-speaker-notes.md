# Speaker Notes — WordPress Abilities API
## Marco Almeida · WordPress Lisboa Meetup · June 2026

---

## Slide 1 — Title

_No notes needed. Let the slide land. Pause before speaking._

---

## Slide 2 — About me

_Let this breathe. Don't read it out. Just say hi._

---

## Slide 3 — Section 1: What is the Abilities API?

Your plugin can do a lot. But does WordPress know that? Does Claude? Does anything outside your own code?

Right now — probably not.

---

## Slide 4 — Timeline

The Abilities API started as a Composer package you had to install manually. In WordPress 6.9 it landed in core — no plugin, no Composer entry needed — and shipped the first three core abilities. WordPress 7.0 added the JavaScript client (`@wordpress/abilities`), hybrid abilities for chaining capabilities into workflows, and the WP AI Client in core, which is the provider-agnostic PHP layer for connecting to AI models.

One thing worth clarifying if it comes up: the **Abilities Explorer** — the admin screen for browsing and testing registered abilities — is **not** in WordPress core. It ships as part of the official **AI Experiments plugin** (wordpress.org/plugins/ai). It's a great dev tool, just not built-in. Install the plugin if you want a visual interface during development.

Maybe show the Alfred plugin as another example of an MCP consumer that can talk to WordPress abilities.

---

## Slide 5 — Register once. Every surface discovers it.

That last column — "Coming" — is why this matters. The list will only grow. You register your abilities today, and every new surface WordPress ships picks them up automatically. You don't rewire anything.

**Notes on "Coming" items — in case anyone asks:**

- **Workflows API** — chains abilities into named, reusable multi-step sequences. Think WP patterns but for actions instead of blocks. They'll appear in the Command Palette, admin menus, and custom plugin UIs.
- **A2A (Agent-to-Agent)** — Google's open protocol for AI agents to communicate directly with each other. One agent could call another agent's abilities as part of a larger workflow, no human in the loop.
- **WebMCP** — an in-browser variant of MCP, so browser-based AI extensions or assistants could call WordPress abilities from the frontend without needing a server proxy.
- **UTCP (Universal Tool Calling Protocol)** — an emerging lightweight alternative to MCP, still being defined. WordPress is tracking it as a potential future consumer.

---

## Slide 6 — Section 2: Why better than hooks?

What happens when you call `do_action` with the wrong arguments?

To be clear: we're not talking about templating hooks like `the_content` or `wp_head`. We're talking about functionality hooks — the kind that trigger business logic, like `woo_dpd_portugal_issue_label`. Those are the ones the Abilities API replaces and improves upon.

---

## Slide 7 — Nothing. Silently. It just runs.

Hooks are powerful but dumb. They don't validate input. They don't document output. They don't check permissions unless you remember to add that yourself — and we all know how that ends. Half the WordPress vulnerability database says hi.

---

## Slide 8 — The validation chain

The validation chain is strict: input schema → permission callback → execute callback → output schema. One failure anywhere returns a clean `WP_Error`. Your callback never sees bad data, and never runs for the wrong user.

---

## Slide 9 — Annotations

These aren't just documentation. They map directly to MCP hints that control how AI agents behave. An agent sees `destructive: true` and asks for confirmation before running. `readonly: true` and it calls freely. You're declaring intent in a way machines understand.

---

## Slide 10 — Hooks vs Abilities

Spend a moment on the bottom row. "Works with AI agents?" — that's the new one. That's the thing hooks fundamentally cannot do, because they're not discoverable, not self-describing, and have no contract for input or output.

---

## Slide 11 — Section 3: How to use it

Four functions. That's the whole API surface you need to learn.

---

## Slide 12 — The functions

The rest is JSON Schema, which you already know from the REST API. If you've written a REST endpoint, you know how to write an ability.

---

## Slide 13 — WP-CLI

**Important:** The slide has a strikethrough on the old info — use that as a moment. Originally I had "requires WP-CLI 2.13, install as a separate package." Then Alain Schlesser told me the nightly already bundles the ability command, so you just update to nightly. And the next stable won't be 2.13 — it'll be 3.0. This is what happens when you prepare a talk about moving-target technology the week it ships. Keeps you humble.

So the actual flow today: `wp cli update --nightly --allow-root` — that's it. No separate package install needed.

After that — invaluable during development. Inspect schemas, confirm registration, run abilities directly from the terminal — and without AI hallucination risk. When you test via CLI you control the input directly. Not the LLM. If it breaks here, it's your code, not the model getting creative with the parameters.

---

## Slide 14 — Backwards compat guard

One line. Protects you on sites still running 6.8 or older. Cheap insurance.

---

## Slide 15 — What core ships today

Three abilities. All read-only. All in the `site` or `user` category. They're deliberately minimal — the goal of 6.9 was to ship the infrastructure, not prescribe every ability. Core keeps the count small so agents don't get overwhelmed with tools before the filtering API matures.

What they return:
- `core/get-site-info` — name, URL, description, language, timezone, date/time formats
- `core/get-user-info` — current user's login, display name, email, roles
- `core/get-environment-info` — WordPress version, PHP version, active theme, multisite status

What's coming in 7.1+: `core/get-active-theme`, `core/list-plugins`, `core/get-site-health`, `core/get-settings`, `core/update-settings`, and an expanded `core/update-user-info`. Together with the three 6.9 abilities, these give an agent everything it needs to orient itself on a new site before taking action.

---

## Slide 16 — Section 4: Abilities API and WooCommerce MCP

Not a chatbot bolted onto your admin. This is your actual store data, your actual permissions, your actual business logic — accessible through natural language.

---

## Slide 17 — Enable WooCommerce MCP

Still in developer preview as of WooCommerce 10.8. Use a staging site.

---

## Slide 18 — Connect Claude Code: Prerequisites

Node.js 22 or later is required by the proxy. It's the first thing to check if things break unexpectedly.

---

## Slide 19 — Connect Claude Code: One command

`npx -y` downloads and runs the proxy on demand — no permanent install needed. The proxy runs locally on your machine and translates MCP protocol to HTTP requests that WordPress understands.

---

## Slide 20 — What WooCommerce MCP exposes

Nine abilities out of the box. Enough to build genuinely useful workflows. Let me show you.

---

## Slide 21 — Live demo: WooCommerce MCP

_Run the four demo prompts live. Go slow. Let each result land before moving to the next._

1. "List all processing orders from the last 7 days"
2. "Which products have stock below 5?"
3. "What's my total revenue this week, broken down by day?" — **check if processing orders are included in the revenue figure or only completed ones**
4. "Update product #42 stock to 0"

---

## Slide 22 — Section 5: Creating your own abilities

WooCommerce's built-in abilities are just the start. Your plugin can join that conversation. Note that the example we'll show is WooCommerce-specific — registering in the WooCommerce category, using WooCommerce permissions — but the principle is exactly the same for any WordPress ability in any context.

---

## Slide 23 — Step 1: Register a category

Register your category on `wp_abilities_api_categories_init`. This groups your abilities in the Abilities Explorer, the CLI output, and any UI that lists abilities by category.

---

## Slide 24 — Step 2: Register the ability

Walk through the anatomy slowly:
- `input_schema` — JSON Schema object. `order_id` is required, `volumes` is optional, defaults to 1
- `output_schema` — only `tracking_number` and `label_url`, both required. No `success` boolean, no `error_message` string. If something goes wrong, the execute callback returns a `WP_Error` — that's the contract all ability consumers expect. Don't duplicate error handling in the output schema.
- `permission_callback` — inline, explicit, per-ability. No more scattered `current_user_can()` checks
- `meta.annotations` — for this ability: not readonly, not destructive, not idempotent — it creates something in the courier API

---

## Slide 25 — Step 3: Expose on WooCommerce MCP

The second argument to the filter is the ability ID string — not a `WP_Ability` object. Use `str_starts_with` to match the entire namespace. One filter, all your abilities.

Important caveat: this filter is only needed because we're integrating with the **WooCommerce MCP endpoint**. If your ability wasn't WooCommerce-specific and you were targeting the standard WordPress MCP Adapter instead, you wouldn't need this — abilities are already exposed automatically to the shared adapter once registered.

---

## Slide 26 — Section 6: Live demo

Real plugin. Real courier API. The store is a demo install — because you don't want to watch me accidentally ship 200 packages to my own house live on stage.

---

## Slide 27 — The prompt

Type this live into Claude Code:

---

Get all processing orders.

For each order, create a DPD shipping label for next Friday — 1 volume per order, unless the order contains items from the "Large Items" category, in which case add 1 extra volume per unit sold of that category.

Once all labels are created, generate the end-of-day report and request a collection with the note "ring the bell".

For every order where a label was created, send an SMS to the customer with the shipping date and tracking number.

Then mark each of those orders as completed.

---

Before running: A word of warning. Don't run this in production without testing first. Preferably not while your client is watching their Slack in real time. This prompt will also consume a frankly embarrassing number of tokens. Think of it as hiring a very capable intern and paying per word they think. Don't try this at home — or at least not on a live store.

_Run the prompt. Stay calm. Let it work._

---

## Slide 28 — Section 7: What changes for you

You've been writing hooks for years. This isn't a replacement. It's an upgrade.

---

## Slide 29 — Register once. Let everything in.

Every ability you register today is automatically available to PHP, REST, WP-CLI, MCP, the Command Palette, and whatever surfaces come next. You don't rewire anything.

On the last bullet: this is a practical tip worth emphasising. If you have an ability that creates shipping labels for all processing orders, the ability itself should query the orders, apply the business logic, and return a clean result. Don't ask the agent to fetch orders first, then loop, then decide volumes — that's slower, more expensive in tokens, and you're trusting the model with logic that should live in your code.

**>>> FORWARD-LOOKING NOTE (say this on stage) <<<**
WooCommerce 10.9, due 12 days after this talk (June 23), introduces canonical domain abilities and a new shared MCP adapter endpoint. The setup shown here still works, but the landscape is shifting. Worth watching. See: developer.woocommerce.com/2026/05/12/mcp-abilities-api-10-9/

---

## Slide 30 — References

_Leave this slide up during Q&A. Invite people to scan/copy the links._

---

## Slide 31 — Questions or suggestions?

_Done. Breathe. Take questions._

The slides will be shared in the meetup event comments as soon as possible after the talk. The GitHub repo is already live at `github.com/webdados/The-WordPress-Abilities-API-talk`.
