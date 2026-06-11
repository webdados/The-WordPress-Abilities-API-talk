# The WordPress Abilities API
## And how to interact with WooCommerce using human language

**Talk by [Marco Almeida](https://webdados.pt) ([@marcoalmeidapt](https://x.com/marcoalmeidapt))**  
WordPress Lisboa Meetup · June 11, 2026

---

## About this talk

The WordPress Abilities API, introduced in WordPress 6.9, gives WordPress a central registry of named, self-describing units of functionality. Register what your plugin can do — once — and every surface that understands abilities can discover and use it: PHP, REST API, WP-CLI, MCP (AI agents), the Command Palette, and more.

This talk covers what the Abilities API is, why it's a meaningful step forward from hooks, how to use it, and how WooCommerce 10.8 exposes its own abilities via MCP — letting you interact with your store through natural language using Claude Code.

The live demo uses [DPD Portugal for WooCommerce](https://nakedcatplugins.com/shop/woocommerce-plugins/dpd-portugal-for-woocommerce/) to show a complete real-world workflow: creating shipping labels, generating end-of-day reports, sending customer SMS notifications, and completing orders — all from a single prompt.

---

## Talk structure

1. **Introduction** — What is the Abilities API? Timeline. Consumers.
2. **Technical** — Why better than hooks? Validation chain. Annotations.
3. **How to use** — Functions, WP-CLI, core abilities.
4. **WooCommerce MCP** — Enable MCP, connect Claude Code, live demo.
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

Open `abilities-api-talk.html` in any browser. Navigate with:
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
- [WP-CLI ability command](https://github.com/wp-cli/ability-command)
- [Six Months of Core AI](https://make.wordpress.org/ai/2025/12/03/six-months-of-core-ai/)
- [AI as a WordPress Fundamental](https://make.wordpress.org/core/2025/12/04/ai-as-a-wordpress-fundamental/)

### WooCommerce
- [WooCommerce MCP intro](https://woocommerce.com/posts/woocommerce-mcp/)
- [WooCommerce MCP developer docs](https://developer.woocommerce.com/docs/features/mcp/)
- [Canonical WooCommerce abilities — WC 10.9](https://developer.woocommerce.com/2026/05/12/mcp-abilities-api-10-9/)
- [WooCommerce MCP call for testing](https://developer.woocommerce.com/2025/11/03/call-for-testing-woocommerce-mcp-beta/)

### Proxy & tooling
- [@automattic/mcp-wordpress-remote](https://github.com/Automattic/mcp-wordpress-remote)
- [WordPress MCP Adapter](https://github.com/WordPress/mcp-adapter)

---

## Speaker

**Marco Almeida** — Chief Executive Meerkat at [Webdados](https://webdados.pt) and [Naked Cat Plugins](https://nakedcatplugins.com).

WordPress plugin developer, WooCommerce integrations specialist, and occasional speaker at Lisbon WordPress Meetup and WordCamp events.

- WordPress.org: [@webdados](https://profiles.wordpress.org/webdados/) / [@nakedcatplugins](https://profiles.wordpress.org/nakedcatplugins/)
- Twitter/X: [@marcoalmeidapt](https://x.com/marcoalmeidapt) / [@webdados](https://x.com/webdados) / [@NakedCatPlugins](https://x.com/nakedcatplugins)
- GitHub: [webdados](https://github.com/webdados) / [Naked-Cat-Plugins](https://github.com/Naked-Cat-Plugins)
- LinkedIn: [@marcoandrealmeida](https://linkedin.com/in/marcoandrealmeida)
