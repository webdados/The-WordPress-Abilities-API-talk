# Speaker Notes: WordPress Abilities API (30-minute version)
## Marco Almeida · WordPress Day for AI 2026 · Faro · October 24, 2026

30-minute slot including 5 minutes of Q&A. Target: 24 to 25 minutes of content. If running long, Q&A is the buffer.

Markers used below:
- **Skip if behind:** cut it without losing the thread.
- **Only if asked:** never say it unprompted, keep it for Q&A.

---

## Slide 1: Title

_No notes needed. Let the slide land. Pause before speaking._

---

## Slide 2: About me

_Don't read it out. Say hi and move on. Ten seconds._

---

## Slide 3: Section 1: What is the Abilities API?

Your plugin can do a lot. But does WordPress know that? Does Claude? Does anything outside your own code?

Right now, probably not.

---

## Slide 4: Timeline

Started as a Composer package. Landed in core in WordPress 6.9, with three core abilities. WordPress 7.0 added the JavaScript client, hybrid abilities, and the WP AI Client, the provider-agnostic PHP layer for talking to AI models. WordPress 7.1 added one flag, `meta.public`, that tells every channel an ability is meant for external clients. Remember that one, it comes back twice.

**Skip if behind:** the Abilities Explorer, the admin screen for browsing and testing abilities, is not in core. It ships with the AI plugin (wordpress.org/plugins/ai). Great dev tool, just not built in.

---

## Slide 5: Register once. Every surface discovers it.

MCP is a plugin, the MCP Adapter, not core. Then look at the "Coming" rows. That's why this matters. You register your abilities today, and every surface WordPress ships later picks them up. You don't rewire anything.

**Only if asked** (the "Coming" items):

- **Workflows API**: chains abilities into named, reusable multi-step sequences.
- **A2A (Agent-to-Agent)**: an open protocol for agents calling each other's abilities.
- **WebMCP**: MCP in the browser, no server proxy. The 7.2 roadmap has WebMCP experiments in the AI plugin.
- **UTCP**: an emerging lightweight alternative to MCP. On the community's radar, not on WordPress's roadmap. Hence "Exploring", not "Coming".

---

## Slide 6: Section 2: Why better than hooks?

What happens when you call `do_action` with the wrong arguments?

We're talking about functionality hooks, the ones that trigger business logic, like `woo_dpd_portugal_issue_label`. Not templating hooks like `the_content`.

---

## Slide 7: Nothing. Silently. It just runs.

_The slide says it. Read the punchline, pause for the laugh, move on._

---

## Slide 8: The validation chain

Input schema, permission callback, execute callback, output schema. One failure anywhere returns a clean `WP_Error`. Your callback never sees bad data, and never runs for the wrong user.

**Only if asked (hooks along the chain, in the order they fire):**
1. `wp_ability_invoked` (action, 7.1): every call, before anything else. Good for auditing.
2. `wp_pre_execute_ability` (filter, 7.1): return a value to skip everything else (cache, rate limit, maintenance mode).
3. `wp_ability_normalize_input` (filter, 7.1): adjust the input before it's validated.
4. `wp_ability_validate_input` (filter, 7.1): extra rules on top of the input schema.
5. `wp_ability_permission_result` (filter, 7.1): add your own authorisation policy to the permission callback's answer.
6. `wp_before_execute_ability` (action, 6.9): input valid, permission granted, callback about to run.
7. `wp_ability_execute_result` (filter, 7.1): change or recover the callback's result.
8. `wp_ability_validate_output` (filter, 7.1): extra rules on top of the output schema.
9. `wp_after_execute_ability` (action, 6.9): after a successful run.

---

## Slide 9: Annotations

These map directly to MCP hints. An agent sees `destructive: true` and asks for confirmation. `readonly: true` and it calls freely. You're declaring intent in a way machines understand.

---

## Slide 10: Hooks vs Abilities

Point at the bottom row. "Works with AI agents?" is the one hooks can't do: they're not discoverable, not self-describing, and have no contract for input or output.

---

## Slide 11: Section 3: How to use it

Four functions. That's all you need to get started.

---

## Slide 12: The functions

Register a category, register an ability, discover, execute. The rest is JSON Schema, which you already know from the REST API. If you've written a REST endpoint, you can write an ability.

_Footnote on the slide lists the other functions (unregister, has, the category getters). Don't mention it; it's there for anyone who wonders._

**Skip if behind:**
- WP-CLI has an `ability` command: `wp ability list`, `wp ability get`, `wp ability run`. It's built into the nightly (`wp cli update --nightly`); on stable 2.12 it's `wp package install wp-cli/ability-command`. It's how you test with your own input, no model in the loop. If it breaks there, it's your code, not the LLM getting creative with the parameters.
- Supporting sites older than 6.9? Wrap your registration in `if ( function_exists( 'wp_register_ability' ) )`. One line.

**Only if asked (every function, one line each):**
- `wp_register_ability_category( $slug, $args )`: registers a category (label, description). Call it on `wp_abilities_api_categories_init`.
- `wp_register_ability( $name, $args )`: registers an ability with its schemas, callbacks and meta. Call it on `wp_abilities_api_init`.
- `wp_get_abilities( $args )`: every registered ability. Since 7.1 it filters by `category`, `namespace` or `meta`.
- `wp_get_ability( $name )`: one ability as a `WP_Ability` object, or `null`.
- `$ability->execute( $input )`: runs the whole chain (input validation, permission, callback, output validation) and returns the result or a `WP_Error`.
- `wp_has_ability( $name )`: whether an ability is registered.
- `wp_unregister_ability( $name )`: removes an ability, e.g. to replace another plugin's with your own.
- `wp_get_ability_category( $slug )` / `wp_get_ability_categories()`: one category, or all of them.
- `wp_has_ability_category( $slug )` / `wp_unregister_ability_category( $slug )`: the category versions of has and unregister.

**Only if asked (discovery hooks, 7.1):** `wp_get_abilities_item_include` decides per ability whether it makes the list; `wp_get_abilities_result` filters the final list.

---

## Slide 13: What core ships today

Three abilities, all read-only: site info, current user, environment. Deliberately minimal: 6.9 shipped the infrastructure, not every ability.

The yellow box is the part to say out loud. Since 7.1 all three are marked `meta.public`, the flag from the timeline, so REST and the MCP Adapter expose them. Claude can see them out of the box.

But exposure isn't authorisation. `public` decides who can *see* an ability; the permission callback decides who can *run* it. The 7.1 dev note says so in as many words. We'll use the same flag when we build our own.

**Skip if behind:** 7.1 added more user fields (name, bio, URL) and a `fields` input to ask for only what you need.

**Only if asked:** read abilities for settings, content and users were proposed for 7.1 and didn't land. They're in the AI plugin for now; core wants proof of adoption first.

---

## Slide 14: Section 4: Abilities API and the MCP Adapter

Not a chatbot bolted onto your admin. Your actual store data, your actual permissions, your actual business logic, through natural language.

**Skip if behind:** WooCommerce used to ship its own MCP bridge. Since WooCommerce 10.9 it's deprecated, and WooCommerce abilities go through the standard WordPress MCP Adapter like any other plugin's.

---

## Slide 15: Connect Claude Code

Three steps, and none of them is WooCommerce-specific.

One: install the MCP Adapter. It's not on WordPress.org yet, so download the zip from the GitHub releases page and upload it like any plugin. Activate it and that's it: it creates a default MCP server. No feature flag, no settings screen. It doesn't turn each ability into its own tool: it gives the agent three, discover, inspect and execute, and every public ability is reachable through those.

Two: an Application Password, for a user who can actually do what you're going to ask. For WooCommerce, a Shop Manager is enough: the abilities check the same capabilities as the REST API. WordPress shows it once, so copy it.

Three: one command. Base64 the username and password, send it as a Basic auth header, and point Claude Code at the adapter's endpoint. Claude Code speaks HTTP natively, so there's no Node and no proxy. Restart Claude Code, done.

**Skip if behind:** the WP-CLI one-liner on the slide does the download and activation in one go; publishing on WordPress.org is on the 7.2 roadmap; the STDIO transport (`wp mcp-adapter serve --user=<admin>`) for local dev; that this replaced the old WooCommerce feature flag.

---

## Slide 16: What WooCommerce exposes

Seven abilities out of the box. Orders: query, add a note, update status. Products: query, create, update, delete. Enough to build useful workflows. Let me show you.

**Only if asked** ("didn't this used to be nine?"): yes, the old bridge had 9 thin REST wrappers. The canonical 7 are proper schema-defined operations.

---

## Slide 17: Live demo

_One prompt, live. Keep it under 90 seconds._

"What were my total sales in the last year, broken down by month?"

While it runs: the tool on screen is `mcp-adapter-execute-ability`; point at its ability name parameter, `woocommerce/orders-query`. A real ability with a real schema, nothing written for this demo.

When the answer lands, point at two things: Claude decided what counts as a sale (the cancelled order is left out), and Claude did the adding up. "It guessed what 'sales' means and did the maths itself. Hold that thought." It pays off on slide 25.

Then gesture at the other prompts on screen: "Stock checks, order lists, updates, all the same way. You'll see much more later." Move on.

**Rehearsed on Sept 28, 2026:** one call, 14 orders, €1,770 (June €930, September €840), one cancelled order excluded. On the day there will also be the October orders staged for the second demo, still processing, so October shows up too. Watch whether Claude counts processing orders as sales, and say which way it went: that's the judgment call worth pointing at.

---

## Slide 18: Section 5: Creating your own abilities

WooCommerce's abilities are just the start. Your plugin can join in. The example is WooCommerce-specific, but the pattern is the same for any plugin.

---

## Slide 19: Step 1: Register a category

One call on `wp_abilities_api_categories_init`. It groups your abilities in the Explorer, the CLI, and any UI that lists them. Ten seconds, move on.

**Only if asked:** `wp_register_ability_category_args` (filter, 6.9) lets any plugin change a category's arguments as it's registered.

---

## Slide 20: Step 2: Register the ability

Two things to say out loud:

- `permission_callback`: inline, explicit, per ability. No more scattered `current_user_can()` checks.
- `'public' => true`: the 7.1 flag we met on the core slide. One line tells REST, the MCP Adapter and whatever comes next that this ability is meant for external clients. Without it, it still works from PHP and WP-CLI, but no REST or MCP client will find it. And it's exposure, not authorisation: the permission callback above is what protects it.

**Skip if behind:** channel-specific flags still work and win when set, like `'mcp' => array( 'public' => true )`. Most code written before 7.1 uses them, WooCommerce's own abilities included.

**Skip if behind:**
- `input_schema`: `order_id` required, `volumes` optional, defaults to 1.
- `output_schema`: just `tracking_number` and `label_url`. No `success` flag, no `error_message`. Errors come back as `WP_Error`, which is the contract every consumer expects.
- `annotations`: not readonly, not destructive, not idempotent. It creates something in the courier API.

**Only if asked:** `wp_register_ability_args` (filter, 6.9) lets any plugin change an ability's arguments as it's registered, e.g. to set `meta.public` on an ability someone else wrote.

---

## Slide 21: Section 6: Live demo

Real plugin. Real courier API. The store is a demo install, because you don't want to watch me ship 200 packages to my own house.

---

## Slide 22: The prompt

Type (or paste) this into Claude Code:

---

Get all processing orders.

For each order, create a DPD shipping label for next Friday, 1 volume per order, unless the order contains items from the "Large Items" category, in which case add 1 extra volume per unit sold of that category.

Once all labels are created, generate the end-of-day report and request a collection with the note "ring the bell".

For every order where a label was created, send an SMS to the customer with the shipping date and tracking number.

Then mark each of those orders as completed.

---

Before running, one line: "Don't run this in production without testing first. And it eats tokens like a very capable intern paid per word they think."

_Run it. Stay calm. Let it work._

**While it runs:** every call shows up as `mcp-adapter-execute-ability`. Read out the ability names as they scroll past: `woocommerce/`, `woo-dpd-portugal/`, `webdados-toolbox/`. Three plugins, three authors, one prompt.

Once it's done: let the applause land, then press next. "How cool was that?" fades in. Pause. Press next again for "Well... it depends." Press next once more to move on. Stepping backward from slide 23 into this one shows both lines, and prev from there hides them one at a time.

---

## Slide 23: Same abilities, smarter caller

This backs the "well, it depends".

Left column: what Claude just did. One `orders-query`. Then, for the volume rule, a `products-query` per line item to check the category, uncached, because it has no reason to cache. Then a label, a note, a status change and an SMS, per order. Even for these few orders that's dozens of calls, and between every one Claude is counting, checking and deciding, in tokens. On a real morning with a real order volume, multiply that.

Right column: if it's the same job with the same rules every morning, none of that reasoning is needed. One custom ability, `my-custom-abilities/process-daily-orders`, takes a date. It calls the exact same abilities, from PHP instead of from an LLM, with the product lookups cached. Same abilities. The only thing that changed is who's calling them.

**Shorten if behind** (one sentence instead of the paragraph): someone still has to write that PHP: the loop, the sequencing, what happens when a label fails or an SMS bounces. That's the developer's job. Prompting is for exploratory, one-off, judgment-heavy work. A fixed daily job is deterministic code, and dressing it up as a prompt just makes it slower and more expensive.

---

## Slide 24: Section 7: What this changes for you

You've been writing hooks for years. This isn't a replacement. It's an upgrade.

---

## Slide 25: Register once. Let everything in.

One registration, every surface: PHP, REST, WP-CLI, MCP, the Command Palette, and whatever comes next.

Last bullet: already made on slide 23, and it's the payoff for slide 17's sales total, where the agent guessed what counts and did the maths. Point at it, five seconds.

The yellow box is the proof: WooCommerce deprecated its own MCP bridge and moved to the shared Adapter (the old bridge is still shipped, marked for removal). Nobody's abilities had to change, only the transport. That happened between writing this talk and giving it.

---

## Slide 26: References

_Leave this up during Q&A if the next slide isn't needed. Invite people to take a photo._

---

## Slide 27: Questions or suggestions?

_Done. Breathe. Take questions._

The slides and notes are on GitHub: `github.com/webdados/The-WordPress-Abilities-API-talk`. The QR code on screen goes there.
