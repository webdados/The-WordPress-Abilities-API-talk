# The WordPress Abilities API
## And how to interact with WooCommerce using human language

**Talk by [Marco Almeida](https://webdados.pt) ([@marcoalmeidapt](https://x.com/marcoalmeidapt))**  
- WordPress Lisboa Meetup · June 11, 2026 (see version 1.1)
- WordPress Faro Meetup · September 3, 2026 (see version 2.0)

---

## About this talk

The WordPress Abilities API, introduced in WordPress 6.9, gives WordPress a central registry of named, self-describing units of functionality. Register what your plugin can do — once — and every surface that understands abilities can discover and use it: PHP, REST API, WP-CLI, MCP (AI agents), the Command Palette, and more.

This talk covers what the Abilities API is, why it's a meaningful step forward from hooks, how to use it, and how WooCommerce 10.9 exposes canonical domain abilities through the shared **WordPress MCP Adapter** — letting you interact with your store through natural language using Claude Code.

The live demo uses [DPD Portugal for WooCommerce](https://nakedcatplugins.com/shop/woocommerce-plugins/dpd-portugal-for-woocommerce/) to show a complete real-world workflow: creating shipping labels, generating end-of-day reports, sending customer SMS notifications, and completing orders — all from a single prompt.

---

## Talk structure

1. **Introduction** — What is the Abilities API? Timeline. Consumers.
2. **Technical** — Why better than hooks? Validation chain. Annotations.
3. **How to use** — Functions, WP-CLI, core abilities.
4. **Abilities API & the MCP Adapter** — Install the adapter, connect Claude Code, live demo.
5. **Creating your own abilities** — Register, define schemas, expose on MCP.
6. **Live demo** — DPD Portugal for WooCommerce full workflow.
7. **What this changes for you** — The bigger picture.

---

## Files

| File | Description |
|---|---|
| `abilities-api-talk.html` | Self-contained HTML slideshow (31 slides, keyboard navigation, deep links) |
| `abilities-api-speaker-notes.md` | Speaker notes for all 31 slides |

### Running the slides

[Open `abilities-api-talk.html` in any browser](https://webdados.github.io/The-WordPress-Abilities-API-talk/abilities-api-talk.html). Navigate with:
- **Arrow keys** or **Space** — next/previous slide
- **Home / End** — first/last slide
- **URL hash** — link directly to a slide: `#slide-14`
- **Browser back/forward** — works as expected

---

## References

### WordPress
- [Abilities API documentation](https://developer.wordpress.org/apis/abilities-api/)
- [Abilities API in WordPress 6.9](https://make.wordpress.org/core/2025/11/10/abilities-api-in-wordpress-6-9/)
- [Client-side Abilities API in WordPress 7.0](https://make.wordpress.org/core/2026/03/24/client-side-abilities-api-in-wordpress-7-0/)
- [WordPress MCP Adapter intro](https://developer.wordpress.org/news/2026/02/from-abilities-to-ai-agents-introducing-the-wordpress-mcp-adapter/)
- [WordPress MCP Adapter (GitHub)](https://github.com/WordPress/mcp-adapter)
- [WP-CLI ability command](https://github.com/wp-cli/ability-command)
- [Six Months of Core AI](https://make.wordpress.org/ai/2025/12/03/six-months-of-core-ai/)
- [AI as a WordPress Fundamental](https://make.wordpress.org/core/2025/12/04/ai-as-a-wordpress-fundamental/)

### WooCommerce
- [Canonical WooCommerce abilities — WC 10.9](https://developer.woocommerce.com/2026/05/12/mcp-abilities-api-10-9/)
- [MCP Integration architecture docs](https://developer.woocommerce.com/docs/features/mcp/)

---

## Version history

**v2.0** — WordPress Faro Meetup, September 3, 2026
- Section 4 rebuilt around the **WordPress MCP Adapter**, replacing the deprecated WooCommerce-specific MCP beta (feature flag, `/wp-json/woocommerce/mcp`, API-key auth, local proxy)
- New setup: install the MCP Adapter plugin from GitHub, connect Claude Code directly over HTTP with `claude mcp add --transport http`, authenticated via WordPress Application Passwords
- Updated ability list to the 7 canonical `woocommerce/*` domain abilities shipped in WC 10.9 (previously 9, under the old REST-bridge beta)
- Added `meta.mcp.public` to the custom ability registration example, with an explanation of why it's required for MCP discovery
- Flagged `woocommerce_mcp_include_ability` as deprecated in favor of the shared adapter's meta-based discovery
- Added a note on why core's three built-in abilities (`core/get-site-info`, `core/get-user-info`, `core/get-environment-info`) aren't MCP-callable by default — a deliberate opt-in safety measure
- Reworked the WC 10.9 forward-looking note into a retrospective one, using the real deprecation as a concrete example of "register once, every surface picks it up"
- Cleaned up references: dropped links to the deprecated beta and its proxy, added the current MCP Adapter and architecture docs

**v1.1** — WordPress Lisboa Meetup, June 11, 2026
- Initial public version of the talk

---

## Speaker

**Marco Almeida** — Chief Executive Meerkat at [Webdados](https://webdados.pt) and [Naked Cat Plugins](https://nakedcatplugins.com).

WordPress plugin developer, WooCommerce integrations specialist, and occasional speaker at Lisbon WordPress Meetup and WordCamp events.

- WordPress.org: [@webdados](https://profiles.wordpress.org/webdados/) / [@nakedcatplugins](https://profiles.wordpress.org/nakedcatplugins/)
- Twitter/X: [@marcoalmeidapt](https://x.com/marcoalmeidapt) / [@webdados](https://x.com/webdados) / [@NakedCatPlugins](https://x.com/nakedcatplugins)
- GitHub: [webdados](https://github.com/webdados) / [Naked-Cat-Plugins](https://github.com/Naked-Cat-Plugins)
- LinkedIn: [@marcoandrealmeida](https://linkedin.com/in/marcoandrealmeida)
