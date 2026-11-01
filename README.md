# Webanto for Gemini CLI

Connect [Gemini CLI](https://geminicli.com/docs/get-started/installation/) to
[Webanto](https://webanto.com) to inspect your organization's connected store
catalog, review product-copy proposals, find imported Search Console
opportunities, and search organization knowledge.

## Install

```sh
gemini extensions install https://github.com/empline/webanto-gemini
```

Restart an existing Gemini CLI session after installation. The extension connects
over Streamable HTTP to `https://api.webanto.com/api/v1/mcp`, the same existing
Webanto MCP endpoint used by other clients. It installs no local server or extra
dependencies and contains no API key or client secret.

## Connect your organization

Every MCP call requires authentication. In an interactive Gemini CLI session:

```text
/mcp auth webanto
```

[Gemini CLI supports OAuth discovery and dynamic client registration](https://geminicli.com/docs/tools/mcp-server/#automatic-oauth-discovery).
Sign in to Webanto, choose an organization, and review the consent screen.
Consent requires the organization's `org:update` permission. Tool access also
depends on your current organization permissions and the granted scopes.
Authentication needs a local browser and loopback callback support. Run `/mcp`
to inspect the connection after authorization.

Disconnect under Webanto Settings → API keys → Connected apps. The server
rechecks permissions; the extension's tool list does not replace authorization.

## Try it

- "Use Webanto to list my organization's synced products and inspect one product."
- "Use Webanto to show product-copy proposals awaiting review."
- "Use Webanto to find opportunities in my organization's imported Search Console data."

## Available tools

| Tool | Purpose | OAuth scope |
| --- | --- | --- |
| `webanto_list_products` | List synced store products with filters and pagination. | `products:read` |
| `webanto_get_product` | Read one synced product by its Webanto id. | `products:read` |
| `webanto_list_copy_proposals` | Read product-copy proposals and review status. | `products:read` |
| `webanto_enrich_product` | Queue AI-generated product-copy proposals for review. | `products:enrich` |
| `webanto_get_job` | Inspect a job belonging to the calling credential. | `jobs:read` |
| `webanto_search_console_opportunities` | Read opportunities from imported Search Console data. | `seo:read` |
| `webanto_search_memory` | Retrieve organization knowledge passages. | `memory:read` |

Six tools are read-only. `webanto_enrich_product` starts a job, consumes AI
credits under the account's subscription and quota, and writes proposals for
review. It does not publish changes to a connected store. Reuse its
`idempotency_key` when retrying the same request. Knowledge search also consumes
one embedding credit per call despite being a read operation. Use metered tools
only when you intend to consume those credits.

The manifest explicitly allows these seven tools and requests only their five
scopes. New remote tools need a reviewed manifest update. No automatic approval,
local commands, hooks, or additional model instructions are configured.

Tool arguments are sent to Webanto over HTTPS. See the
[MCP guide](https://webanto.com/ai/mcp), [Privacy](https://webanto.com/privacy),
and [Terms](https://webanto.com/terms).

## Update or uninstall

```sh
gemini extensions update webanto
gemini extensions uninstall webanto
```

For a reproducible installation, append `--ref <full-commit-sha>` to the install
command. Review changes before updating an installed extension.

## Maintainers

The root `gemini-extension.json` is the complete integration. Its version is
independent of the remote MCP server version. Before releasing:

1. Compare the allowlist and scopes with Webanto's canonical tool registry and
   OAuth scope declarations; check the published MCP guide agrees.
2. Run Webanto's shared MCP conformance suite and MCP contract/privacy guard.
3. Verify anonymous initialization returns HTTP 401 with a Bearer challenge,
   and that Node can fetch the challenge's protected-resource metadata and
   authorization-server metadata. Check the resource, issuer, PKCE S256, and
   declared scopes agree with this extension.
4. Verify the live server card and official MCP registry listing match the
   server identity. Install from the public repository in a fresh
   `GEMINI_CLI_HOME`; authentication is required before Connected is expected.
5. Confirm the public root manifest matches the tested file and that GitHub's
   `gemini-cli-extension` topic is present.

These checks do not certify signed-in Gemini CLI access to organization data.
That separate end-to-end proof requires a consenting account and organization.
Do not start metered jobs or knowledge searches as unattended release probes.

Google's [gallery](https://geminicli.com/extensions/) discovers public tagged
repositories on a documented daily crawl and lists extensions that pass
validation. Publishing here does not establish inclusion or endorsement.
[Release requirements](https://geminicli.com/docs/extensions/releasing/#list-your-extension-in-the-gallery).
