# The WordPress Abilities API
## And how to interact with WooCommerce using human language

**Talk by [Marco Almeida](https://marcoalmeida.pt/) ([Webdados](https://webdados.pt), [Naked Cat Plugins](https://nakedcatplugins.com))**  
[X: @marcoalmeidapt](https://x.com/marcoalmeidapt) · [WordPress.org: @webdados](https://profiles.wordpress.org/webdados/)  
- WordPress Lisboa Meetup · June 11, 2026 (see version 1.1)
- WordPress Faro Meetup · September 3, 2026 (see version 2.0) · [Watch the recording on WordPress.tv](https://wordpress.tv/2026/09/28/the-wp-abilities-api-and-how-to-interact-with-woocommerce-using-human-language/)
- WordPress Day for AI 2026 · Faro · October 24, 2026 (30-minute version, see version 3.0)

---

## About this talk

The WordPress Abilities API, introduced in WordPress 6.9, gives WordPress a central registry of named, self-describing units of functionality. Register what your plugin can do once, and every surface that understands abilities can discover and use it: PHP, REST API, WP-CLI, JavaScript, MCP (AI agents), and whatever comes next.

This talk covers what the Abilities API is, why it's a meaningful step forward from hooks, how to use it, and how WooCommerce 10.9 exposes canonical domain abilities through the shared **WordPress MCP Adapter**, letting you interact with your store through natural language using Claude Code.

The live demo uses [DPD Portugal for WooCommerce](https://nakedcatplugins.com/shop/woocommerce-plugins/dpd-portugal-for-woocommerce/) to show a complete real-world workflow: creating shipping labels, generating end-of-day reports, sending customer SMS notifications, and completing orders, all from a single prompt.

---

## Two versions

| Version | Length | Slides | Deck | Speaker notes |
|---|---|---|---|---|
| Full talk | 60 minutes | 32 | [Open the 60-minute deck](https://webdados.github.io/The-WordPress-Abilities-API-talk/abilities-api-talk.html) | [`abilities-api-speaker-notes.md`](abilities-api-speaker-notes.md) |
| Short talk | 30 minutes | 27 | [Open the 30-minute deck](https://webdados.github.io/The-WordPress-Abilities-API-talk/abilities-api-talk-30min.html) | [`abilities-api-speaker-notes-30min.md`](abilities-api-speaker-notes-30min.md) |

Both cover the same ideas and share the same facts. The 30-minute version is tighter:

- No separate WP-CLI and backwards-compatibility slides (both points live on in the speaker notes)
- No legacy `woocommerce_mcp_include_ability` filter slide
- Installing the MCP Adapter and connecting Claude Code fit on a single slide
- The first live demo is a single prompt; the full workflow demo stays
- Speaker notes mark what to skip when running behind

---

## Talk structure

1. **Introduction**: What is the Abilities API? Timeline. Consumers.
2. **Technical**: Why better than hooks? Validation chain. Annotations.
3. **How to use**: Functions, WP-CLI (60-minute version), core abilities.
4. **Abilities API & the MCP Adapter**: Install the adapter, connect Claude Code, live demo.
5. **Creating your own abilities**: Register, define schemas, expose on MCP.
6. **Live demo**: DPD Portugal for WooCommerce full workflow.
7. **What this changes for you**: The bigger picture.

---

## Files

| File | Description |
|---|---|
| `abilities-api-talk.html` | 60-minute version: self-contained HTML slideshow (32 slides, keyboard navigation, deep links) |
| `abilities-api-speaker-notes.md` | Speaker notes for all 32 slides of the 60-minute version |
| `abilities-api-talk-30min.html` | 30-minute version: same format, 27 slides |
| `abilities-api-speaker-notes-30min.md` | Speaker notes for all 27 slides of the 30-minute version |

### Running the slides

Open either deck in any browser: [60-minute version](https://webdados.github.io/The-WordPress-Abilities-API-talk/abilities-api-talk.html) or [30-minute version](https://webdados.github.io/The-WordPress-Abilities-API-talk/abilities-api-talk-30min.html). Navigate with:
- **Arrow keys** or **Space**: next/previous slide
- **Home / End**: first/last slide
- **URL hash**: link directly to a slide: `#slide-14`
- **Browser back/forward**: works as expected

---

## References

### WordPress
- [Abilities API documentation](https://developer.wordpress.org/apis/abilities-api/)
- [Abilities API in WordPress 6.9](https://make.wordpress.org/core/2025/11/10/abilities-api-in-wordpress-6-9/)
- [Client-side Abilities API in WordPress 7.0](https://make.wordpress.org/core/2026/03/24/client-side-abilities-api-in-wordpress-7-0/)
- [A unified public exposure flag for Abilities in WordPress 7.1](https://make.wordpress.org/core/2026/08/04/a-unified-public-exposure-flag-for-abilities-in-wordpress-7-1/)
- [Abilities API improvements in WordPress 7.1](https://make.wordpress.org/core/2026/07/31/abilities-api-improvements-in-wordpress-7-1/)
- [New execution lifecycle filters for the Abilities API in WordPress 7.1](https://make.wordpress.org/core/2026/07/29/new-execution-lifecycle-filters-for-the-abilities-api-in-wordpress-7-1/)
- [Filtering registered abilities with wp_get_abilities() in WordPress 7.1](https://make.wordpress.org/core/2026/08/05/filtering-registered-abilities-with-wp_get_abilities-in-wordpress-7-1/)
- [Merge Proposal: Expanding WordPress Core Abilities](https://make.wordpress.org/core/2026/07/02/merge-proposal-expanding-wordpress-core-abilities/)
- [Roadmap to 7.2](https://make.wordpress.org/core/2026/09/18/roadmap-to-7-2/)
- [AI plugin (includes the Abilities Explorer)](https://wordpress.org/plugins/ai/)
- [WordPress MCP Adapter intro](https://developer.wordpress.org/news/2026/02/from-abilities-to-ai-agents-introducing-the-wordpress-mcp-adapter/)
- [WordPress MCP Adapter (GitHub)](https://github.com/WordPress/mcp-adapter)
- [WP-CLI ability command docs](https://developer.wordpress.org/cli/commands/ability/)
- [WP-CLI ability command (GitHub)](https://github.com/wp-cli/ability-command)
- [Six Months of Core AI](https://make.wordpress.org/ai/2025/12/03/six-months-of-core-ai/)
- [AI as a WordPress Fundamental](https://make.wordpress.org/core/2025/12/04/ai-as-a-wordpress-fundamental/)

### WooCommerce
- [Canonical WooCommerce abilities: WC 10.9](https://developer.woocommerce.com/2026/05/12/mcp-abilities-api-10-9/)
- [MCP Integration architecture docs](https://developer.woocommerce.com/docs/features/mcp/)
- [woocommerce/woocommerce#69325: data missing from the domain abilities](https://github.com/woocommerce/woocommerce/issues/69325)

---

## Version history

**v3.0**, to be tagged and presented at WordPress Day for AI 2026, Faro, October 24, 2026
- New 30-minute version of the talk, alongside the 60-minute one
- Both versions re-checked against WordPress 7.1, WooCommerce 11.1 and MCP Adapter 0.6
- WordPress 7.1's unified `meta.public` flag: added to the timeline, used in the custom ability example, and explained on the core abilities slide, with the point that exposure is not authorisation
- Corrected what the three core abilities return, and that since 7.1 they are exposed to REST and MCP
- The MCP Adapter's default server exposes three tools (discover, inspect, execute) rather than one tool per ability; demo notes updated to match
- MCP Adapter install via the GitHub zip or a single WP-CLI command; Composer is no longer recommended
- WooCommerce abilities use the same capabilities as the WooCommerce REST API (a Shop Manager works), not a fixed `manage_woocommerce` check
- The WooCommerce MCP bridge is described as deprecated but still shipped
- Short note on what WooCommerce's domain abilities leave out: customer personal data on purpose, product categories for now
- The shipping workflow prompt adds an order note to each order
- Consumers table: MCP marked as a plugin, A2A, WebMCP and UTCP as "Exploring"
- The 60-minute title slide no longer names a specific event

**v2.0**, WordPress Faro Meetup, September 3, 2026 ([recording](https://wordpress.tv/2026/09/28/the-wp-abilities-api-and-how-to-interact-with-woocommerce-using-human-language/))
- Section 4 rebuilt around the **WordPress MCP Adapter**, replacing the deprecated WooCommerce-specific MCP beta (feature flag, `/wp-json/woocommerce/mcp`, API-key auth, local proxy)
- New setup: install the MCP Adapter plugin from GitHub, connect Claude Code directly over HTTP with `claude mcp add --transport http`, authenticated via WordPress Application Passwords
- Updated ability list to the 7 canonical `woocommerce/*` domain abilities shipped in WC 10.9 (previously 9, under the old REST-bridge beta)
- Added `meta.mcp.public` to the custom ability registration example, with an explanation of why it's required for MCP discovery
- Flagged `woocommerce_mcp_include_ability` as deprecated in favor of the shared adapter's meta-based discovery
- Added a note on why core's three built-in abilities (`core/get-site-info`, `core/get-user-info`, `core/get-environment-info`) aren't MCP-callable by default; a deliberate opt-in safety measure
- Reworked the WC 10.9 forward-looking note into a retrospective one, using the real deprecation as a concrete example of "register once, every surface picks it up"
- Cleaned up references: dropped links to the deprecated beta and its proxy, added the current MCP Adapter and architecture docs

**v1.1**, WordPress Lisboa Meetup, June 11, 2026
- Initial public version of the talk

---

## Speaker

**Marco Almeida**, Chief Executive Meerkat at [Webdados](https://webdados.pt) and [Naked Cat Plugins](https://nakedcatplugins.com).

WordPress plugin developer, WooCommerce integrations specialist, and occasional speaker at Lisbon WordPress Meetup and WordCamp events.

- WordPress.org: [@webdados](https://profiles.wordpress.org/webdados/) / [@nakedcatplugins](https://profiles.wordpress.org/nakedcatplugins/)
- Twitter/X: [@marcoalmeidapt](https://x.com/marcoalmeidapt) / [@webdados](https://x.com/webdados) / [@NakedCatPlugins](https://x.com/nakedcatplugins)
- GitHub: [webdados](https://github.com/webdados) / [Naked-Cat-Plugins](https://github.com/Naked-Cat-Plugins)
- LinkedIn: [@marcoandrealmeida](https://linkedin.com/in/marcoandrealmeida)
