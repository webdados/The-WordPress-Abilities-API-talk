# Speaker Notes: WordPress Abilities API
## Marco Almeida · Webdados / Naked Cat Plugins

---

## Slide 1: Title

_No notes needed. Let the slide land. Pause before speaking._

---

## Slide 2: About me

_Let this breathe. Don't read it out. Just say hi._

---

## Slide 3: Section 1: What is the Abilities API?

Your plugin can do a lot. But does WordPress know that? Does Claude? Does anything outside your own code?

Right now, probably not.

---

## Slide 4: Timeline

The Abilities API started as a Composer package you had to install manually. In WordPress 6.9 it landed in core (no plugin, no Composer entry needed) and shipped the first three core abilities. WordPress 7.0 added the JavaScript client (`@wordpress/abilities`), hybrid abilities, and the WP AI Client in core, which is the provider-agnostic PHP layer for connecting to AI models. WordPress 7.1 added a unified `meta.public` flag (one place to say "this ability is meant for external clients"), lifecycle filters around execution, and filtering in `wp_get_abilities()`.

One thing worth clarifying if it comes up: the **Abilities Explorer**, the admin screen for browsing and testing registered abilities, is **not** in WordPress core. It ships as part of the official **AI plugin** (wordpress.org/plugins/ai). It's a great dev tool, just not built-in. Install the plugin if you want a visual interface during development.

Maybe show the Alfred plugin as another example of an MCP consumer that can talk to WordPress abilities.

---

## Slide 5: Register once. Every surface discovers it.

Those "Coming" rows are why this matters. The list will only grow. You register your abilities today, and every new surface WordPress ships picks them up automatically. You don't rewire anything.

**Notes on "Coming" items, in case anyone asks:**

- **Workflows API**: chains abilities into named, reusable multi-step sequences. Think WP patterns but for actions instead of blocks. They'll appear in the Command Palette, admin menus, and custom plugin UIs.
- **A2A (Agent-to-Agent)**: an open protocol for AI agents to communicate directly with each other. One agent could call another agent's abilities as part of a larger workflow, no human in the loop.
- **WebMCP**: an in-browser variant of MCP, so browser-based AI extensions or assistants could call WordPress abilities from the frontend without needing a server proxy.
- **UTCP (Universal Tool Calling Protocol)**: an emerging lightweight alternative to MCP, still being defined. It's on the community's radar, not on WordPress's roadmap.

That's why the last row says "Exploring", not "Coming". WebMCP is the one with real momentum: the 7.2 roadmap lists WebMCP experiments in the AI plugin.

---

## Slide 6: Section 2: Why better than hooks?

What happens when you call `do_action` with the wrong arguments?

To be clear: we're not talking about templating hooks like `the_content` or `wp_head`. We're talking about functionality hooks, the kind that trigger business logic, like `woo_dpd_portugal_issue_label`. Those are the ones the Abilities API replaces and improves upon.

---

## Slide 7: Nothing. Silently. It just runs.

Hooks are powerful but dumb. They don't validate input. They don't document output. They don't check permissions unless you remember to add that yourself, and we all know how that ends. Half the WordPress plugin vulnerability database says hi.

---

## Slide 8: The validation chain

The validation chain is strict: input schema → permission callback → execute callback → output schema. One failure anywhere returns a clean `WP_Error`. Your callback never sees bad data, and never runs for the wrong user.

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

These aren't just documentation. They map directly to MCP hints that control how AI agents behave. An agent sees `destructive: true` and asks for confirmation before running. `readonly: true` and it calls freely. You're declaring intent in a way machines understand.

---

## Slide 10: Hooks vs Abilities

Spend a moment on the bottom row. "Works with AI agents?" is the new one. That's the thing hooks fundamentally cannot do, because they're not discoverable, not self-describing, and have no contract for input or output.

---

## Slide 11: Section 3: How to use it

Four functions. That's all you need to get started. There are a few more (unregister, has, and the category equivalents), but these four are what you'll use.

---

## Slide 12: The functions

The rest is JSON Schema, which you already know from the REST API. If you've written a REST endpoint, you know how to write an ability.

_Footnote on the slide lists the other functions (unregister, has, the category getters). Don't mention it; it's there for anyone who wonders._

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

## Slide 13: WP-CLI

**Important:** The slide has a strikethrough on the old info, use that as a moment. Originally I had "requires WP-CLI 2.13, install as a separate package." Then Alain Schlesser told me the nightly already bundles the ability command, so you just update to nightly. And the next stable won't be 2.13, it'll be 3.0. This is what happens when you prepare a talk about moving-target technology the week it ships. Keeps you humble.

So the actual flow today: `wp cli update --nightly --allow-root`, and the command is there. Staying on stable (2.12)? Then it's still the separate package: `wp package install wp-cli/ability-command`. Once 3.0 ships, it's built in for everyone.

After that, it's invaluable during development. Inspect schemas, confirm registration, run abilities directly from the terminal, and without AI hallucination risk. When you test via CLI you control the input directly. Not the LLM. If it breaks here, it's your code, not the model getting creative with the parameters.

---

## Slide 14: Backwards compat guard

One line. Protects you on sites still running 6.8 or older. Cheap insurance.

---

## Slide 15: What core ships today

Three abilities. All read-only. All in the `site` or `user` category. They're deliberately minimal; the goal of 6.9 was to ship the infrastructure, not prescribe every ability.

What they return:
- `core/get-site-info`: name, description, URL, admin email, language, WordPress version
- `core/get-user-info`: the current user's ID, display name, login, roles and locale. 7.1 added first and last name, nickname, bio and URL
- `core/get-environment-info`: environment type, PHP version, database server, WordPress version

Since 7.1 all three accept an optional `fields` input to return only what you ask for.

**Say this on stage (yellow box):** since 7.1, all three set `meta.public => true`. That's the new unified flag: one place to say "this ability is meant for external clients". REST uses it as its default, and the MCP Adapter (0.6+) exposes public abilities, so on a current site Claude can see these three out of the box.

The part worth stressing: exposure isn't authorisation. `public` decides who can *see* an ability. The `permission_callback` decides who can *run* it. The 7.1 dev note says it outright: don't treat any exposure flag as a security boundary. We'll use the same flag on slide 24.

**Only if asked (the blue box line):** a merge proposal for 7.1 added `core/read-settings`, `core/read-content` and `core/read-users`. It didn't land. The 7.2 roadmap keeps new abilities in the AI plugin until they prove themselves, with write abilities after that.

---

## Slide 16: Section 4: Abilities API and the MCP Adapter

Not a chatbot bolted onto your admin. This is your actual store data, your actual permissions, your actual business logic, accessible through natural language.

Note the rename from earlier versions of this talk: this section used to be framed as "WooCommerce MCP." As of WooCommerce 10.9, the WooCommerce-specific MCP bridge is deprecated. WooCommerce abilities are now reached through the standard **WordPress MCP Adapter**, the same generic surface any plugin's abilities go through. WooCommerce is a consumer of it, not a separate product.

---

## Slide 17: Install the MCP Adapter

Say plainly: this is not a WordPress.org plugin (yet). Publishing it there is on the 7.2 roadmap. For now you get it from GitHub, `github.com/WordPress/mcp-adapter`: Releases, download the plugin zip, upload and activate like any other plugin. Or one WP-CLI command that does the same thing. Composer still works, but the project no longer recommends it.

No feature flag, no settings screen to visit. Activating the plugin is enough: it creates a default MCP server automatically. It doesn't turn every public ability into its own MCP tool. It exposes three tools, discover, get ability info and execute, and the agent uses those to find and run any public ability. Two transports ship out of the box: HTTP at `/wp-json/mcp/mcp-adapter-default-server`, and STDIO via `wp mcp-adapter serve --user=<admin>` for local dev.

This replaces the old `woocommerce_feature_mcp_integration_enabled` flag entirely; there's nothing WooCommerce-specific to turn on anymore.

---

## Slide 18: Connect Claude Code: Prerequisites

Good news here: the setup got simpler, not more complex. The old flow needed a local Node proxy; this one doesn't. Claude Code talks HTTP straight to the adapter's endpoint.

What you do need: a WordPress account allowed to do whatever you'll ask. The WooCommerce abilities check the same capabilities as the WooCommerce REST API, so a Shop Manager or an administrator works. And an **Application Password** for that account. Create it under Users → Your Profile → Application Passwords. Name it, click Add, and copy the password immediately, WordPress only shows it once.

---

## Slide 19: Connect Claude Code: One command

Two steps on screen: base64-encode `username:application-password` (standard HTTP Basic Auth), then `claude mcp add --transport http` pointing straight at the adapter's default server URL, with that encoded string in an `Authorization: Basic` header.

No `npx`, no proxy process running in the background translating protocols. Claude Code is a native MCP HTTP client now, so it just talks to the endpoint directly. One command, restart Claude Code, done.

---

## Slide 20: What WooCommerce exposes

Seven canonical abilities out of the box: the current list as of WooCommerce 10.9:

  - `woocommerce/orders-query`: find orders
  - `woocommerce/order-add-note`: add order note
  - `woocommerce/order-update-status`: update order status
  - `woocommerce/products-query`: find products
  - `woocommerce/product-create`: create product
  - `woocommerce/product-update`: update product
  - `woocommerce/product-delete`: delete/trash/restore product

Enough to build genuinely useful workflows. Let me show you.

**If someone asks "didn't this used to be nine?"** Yes. The old REST-bridge beta had 9: products covered list, get, create, update, delete (5), orders covered list, get, create, update (4). The canonical set trades that structure for 7: products keep 4 ops with `query` absorbing list+get into one call, orders drop to 3, losing standalone `order-create` and `order-get`, gaining `order-add-note`, which the old bridge never had. Net fewer tools, but each one is a proper schema-defined domain operation instead of a thin REST wrapper.

---

## Slide 21: Live demo: WooCommerce abilities

_Run the four demo prompts live. Go slow. Let each result land before moving to the next._

1. "List all processing orders from the last 7 days"
2. "Which products have stock below 5?"
3. "What's my total revenue this week, broken down by day?" (**check if processing orders are included in the revenue figure or only completed ones**)
4. "Update product #42 stock to 0"

What the room sees on screen: Claude calls `mcp-adapter-discover-abilities` once, then `mcp-adapter-execute-ability` with the ability name (`woocommerce/orders-query` and so on) as a parameter. Point at that parameter, not the tool name.

---

## Slide 22: Section 5: Creating your own abilities

WooCommerce's built-in abilities are just the start. Your plugin can join that conversation. Note that the example we'll show is WooCommerce-specific (registering its own category, using WooCommerce permissions), but the principle is exactly the same for any WordPress ability in any context.

---

## Slide 23: Step 1: Register a category

Register your category on `wp_abilities_api_categories_init`. This groups your abilities in the Abilities Explorer, the CLI output, and any UI that lists abilities by category.

**Only if asked:** `wp_register_ability_category_args` (filter, 6.9) lets any plugin change a category's arguments as it's registered.

---

## Slide 24: Step 2: Register the ability

Walk through the anatomy slowly:
- `input_schema`: JSON Schema object. `order_id` is required, `volumes` is optional, defaults to 1
- `output_schema`: only `tracking_number` and `label_url`, both required. No `success` boolean, no `error_message` string. If something goes wrong, the execute callback returns a `WP_Error`, that's the contract all ability consumers expect. Don't duplicate error handling in the output schema.
- `permission_callback`: inline, explicit, per-ability. No more scattered `current_user_can()` checks
- `meta.public`: this is the important one to call out. One line, new in WordPress 7.1, and it's "register once" in a nutshell: it tells every channel that this ability is meant for external clients. REST uses it as its default, the **WordPress MCP Adapter** exposes it, and future channels will read the same flag. Without it, the ability still works from PHP and WP-CLI, but no REST or MCP client will find it.

  Channel-specific flags still work and win when set: `show_in_rest`, or `'mcp' => array( 'public' => true )` for MCP only. You'll see those in plenty of code written before 7.1, including WooCommerce's own abilities.

  And the same reminder as slide 15: `public` is exposure, not authorisation. The `permission_callback` is what protects it.
- `meta.annotations`: for this ability, not readonly, not destructive, not idempotent (it creates something in the courier API)

**Only if asked:** `wp_register_ability_args` (filter, 6.9) lets any plugin change an ability's arguments as it's registered, e.g. to set `meta.public` on an ability someone else wrote.

---

## Slide 25: Step 3: The legacy filter

The second argument to the filter is the ability ID string, not a `WP_Ability` object. Use `str_starts_with` to match the entire namespace. One filter, all your abilities.

**Say this on stage:** as of WooCommerce 10.9, `woocommerce_mcp_include_ability` is deprecated. It only ever scoped the old WooCommerce-specific MCP bridge, and that bridge is now on its way out. WooCommerce abilities (yours included) are exposed through the standard WordPress MCP Adapter instead, the Adapter from slide 17. Set `meta.public` on the ability, as on slide 24, and it's discovered automatically. No filter needed. The bridge itself is still shipped (WooCommerce 11.1 still has it) but marked for removal.

I'm keeping this slide in the talk because it's still what you'll see in most existing tutorials and blog posts today, and it's a good illustration of how the filter pattern works, just flag it as legacy.

---

## Slide 26: Section 6: Live demo

Real plugin. Real courier API. The store is a demo install, because you don't want to watch me accidentally ship 200 packages to my own house live on stage.

---

## Slide 27: The prompt

Type this live into Claude Code:

---

Get all processing orders.

For each order, create a DPD shipping label for next Friday, 1 volume per order, unless the order contains items from the "Large Items" category, in which case add 1 extra volume per unit sold of that category.

Once all labels are created, generate the end-of-day report and request a collection with the note "ring the bell".

For every order where a label was created, send an SMS to the customer with the shipping date and tracking number.

Then mark each of those orders as completed.

---

Before running: A word of warning. Don't run this in production without testing first. Preferably not while your client is watching their Slack in real time. This prompt will also consume a frankly embarrassing number of tokens. Think of it as hiring a very capable intern and paying per word they think. Don't try this at home, or at least not on a live store.

_Run the prompt. Stay calm. Let it work._

**While it runs:** every call shows up as `mcp-adapter-execute-ability`. Read out the ability names as they scroll past: `woocommerce/`, `woo-dpd-portugal/`, `webdados-toolbox/`. Three plugins, three authors, one prompt.

Once it's done: let the applause land, then press next. "How cool was that?" fades in on its own, nothing shows before you press. Pause. Press next again for "Well... it depends." to fade in underneath. Press next once more to move to the following slide. If you ever step backward from slide 28 into this one, both lines come back fully visible, and prev from there hides them one at a time, so you can safely rewind mid-talk without losing your place.

---

## Slide 28: Same abilities, smarter caller

This slide backs the "well, it depends" turn. Talk through what Claude actually did to pull off that one prompt.

**Say this on stage:** walk the left column first. Point out the namespaces on each pill, not just the ability names. `woocommerce/orders-query`, `woo-dpd-portugal/create-shipping-label`, `webdados-toolbox/send-sms`. Three different plugins, three different authors, all speaking the same Abilities API, all callable from the same prompt. That's worth a beat on its own.

Then walk what Claude actually had to do to get there. Finding the orders was one call. But the volume rule, "1 volume per order, unless it's a Large Items category item, then add 1 extra volume per unit", meant Claude had to look up the product category for every single line item, one `products-query` call at a time, looped, uncached, because it has no reason to know it should cache. Between every one of those calls, Claude is also the one doing the counting, checking each result, and deciding what to call next. That reasoning happens in tokens, every single time, even though the logic never changes. Then it's one `create-shipping-label`, one `order-add-note`, one `order-update-status`, one `send-sms`, per order. For 15 orders, that's not 8 tool calls, it's closer to 60, plus every round trip and every decision in between costs tokens and latency.

Now the turn: if this is the exact same job, same rules, every single morning, none of that reasoning is needed at all. That's the right column. One custom ability, `my-custom-abilities/process-daily-orders`, takes a date. Same "one prompt" experience for whoever triggers it, but now the prompt calls one ability, not sixty.

**The part worth lingering on:** the custom ability isn't a rewrite. It calls the exact same abilities Claude called, `orders-query`, `products-query`, `create-shipping-label`, and the rest, just from PHP instead of from an LLM. The `products-query` lookups are still looped, they still happen once per product, but now they're cached, so the same product's category is only ever resolved once, not once per order it appears in. Same abilities. Same "register once" foundation from earlier in the talk. The only thing that changed is who's doing the calling, and whether that caller needs to reason about it or can just execute it.

**Close the loop for the room:** that PHP doesn't write itself. Someone still has to build the loop, sequence the calls correctly, and handle what happens when a label fails to generate or an SMS bounces. That's the developer's job, and it's a good one to have. It's exactly what turns "AI can do this" into "AI does this cheaply, every day, without supervision." Prompting an agent is for exploratory, judgment-heavy, or one-off work. A fixed daily job with fixed rules is deterministic code. Dressed up as a prompt, it's still deterministic code, just a slower and more expensive version of it, until a developer wraps it properly.

---

## Slide 29: Section 7: What this changes for you

You've been writing hooks for years. This isn't a replacement. It's an upgrade.

---

## Slide 30: Register once. Let everything in.

Every ability you register today is automatically available to PHP, REST, WP-CLI, MCP, JavaScript, and whatever surfaces come next (the Command Palette and the Workflows API are on the way). You don't rewire anything.

On the last bullet: this is a practical tip worth emphasising. If you have an ability that creates shipping labels for all processing orders, the ability itself should query the orders, apply the business logic, and return a clean result. Don't ask the agent to fetch orders first, then loop, then decide volumes. That's slower, more expensive in tokens, and you're trusting the model with logic that should live in your code.

**>>> NOTE <<<**
Worth calling out explicitly: this is "register once" playing out in the real world. WooCommerce deprecated its own MCP bridge (the `woocommerce_mcp_include_ability` filter we saw on slide 25) in favor of the shared WordPress MCP Adapter covered in Section 4. It's still shipped for now, marked for removal. None of the abilities themselves changed. Only the transport did. If you'd registered abilities the way slide 24 shows, WooCommerce's transition cost you nothing. That's the whole pitch of this slide, proven by a real deprecation that happened between writing this talk and giving it.

---

## Slide 31: References

_Leave this slide up during Q&A. Invite people to scan/copy the links._

---

## Slide 32: Questions or suggestions?

_Done. Breathe. Take questions._

The slides and notes are on GitHub: `github.com/webdados/The-WordPress-Abilities-API-talk`. The QR code on screen goes there.
