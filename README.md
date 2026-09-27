# Postraid MCP

**From product brief to a reviewed social-content library.**

Postraid helps founders, creators and growth marketers turn product context into social content. Start with a website or product brief, explore reactions, memes and carousels, then keep the ideas that fit the brand. The application combines a content library, creative review and publishing calendar so approved work has a place to go next.

[Website](https://www.postraid.com) · [MCP repository](https://github.com/postraid/mcp-server) · [Agent skill](https://github.com/postraid/agent-skill) · [npm package](https://www.npmjs.com/package/postraid-mcp)

## What this connector does

This package connects a local stdio MCP client to the hosted [Postraid MCP server](https://mcp.postraid.com/mcp). Hosted tools run on Cloudflare; the local package bridges the connection and opens browser-based OAuth. You do not need to deploy a Worker or paste a product password into your assistant.

| Tool | What it does |
| --- | --- |
| `get_profile` | Read the signed-in account context. |
| `list_brands` | Find brands in the user’s own workspace. |
| `list_posts` | Browse saved content pieces for an owned brand. |
| `get_post` | Read an existing owned content piece. |
| `create_post_draft` | Save supplied copy as an editable carousel with placeholder backgrounds and an editor link. |

The MCP reads the user’s own workspace, not team workspaces. Its posts are saved content pieces, not a delivery ledger proving that a social post went live. Draft creation does not generate backgrounds, render media, schedule, publish or spend credits. Replace placeholder backgrounds and review the result in the product. Reach, sales and viral performance are not guaranteed.

## Example workflow

1. Choose the owned brand and inspect existing content to avoid repeating the same idea.
2. Prepare a title, one to ten slides and hashtags that match the supplied product brief.
3. Save the requested carousel draft, return its editor link, and review copy and backgrounds in Postraid before scheduling.

### Things to ask your assistant

> List my brands, then show the saved content for the brand I select.

> Review this saved carousel and suggest a clearer opening based on my product brief.

> Save these five slides as an editable carousel draft. Return its editor link without generating media or publishing.

## Connect a remote MCP client

1. Open the client’s custom MCP or connector settings.
2. Enter `https://mcp.postraid.com/mcp` as the remote server URL.
3. Complete Postraid sign-in in your browser and review the permissions on the consent screen.
4. Return to the client and load the available tools.

Use a client that supports Streamable HTTP MCP and OAuth. Custom-connector availability depends on the client and your account. A public repository or npm release does not mean the integration has been approved for a client’s marketplace.

## Claude Desktop and other stdio clients

Requires **Node.js 22 or newer** and an existing Postraid account. Add this entry to your client’s MCP configuration:

```json
{
  "mcpServers": {
    "postraid": {
      "command": "npx",
      "args": [
        "-y",
        "postraid-mcp"
      ]
    }
  }
}
```

You can also run `npx -y postraid-mcp` from a terminal to start the bridge. It speaks MCP over stdio; it is not an interactive chat interface. For desktop clients that support MCPB extensions, download the `.mcpb` file from [Postraid releases](https://github.com/postraid/mcp-server/releases).

## Permissions and account access

Requested scopes: `profile:read content:read drafts:write`. Older profile-only connections need to reconnect and explicitly approve the additional permissions before content tools are available.

Only approve a connection you intended to start. If sign-in opens a new tab, finish it, return to the consent screen and refresh. [Manage or revoke connected apps](https://www.postraid.com/oauth/mcp/connections).

The package uses pinned [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) for OAuth, PKCE and local token storage. Tokens on your computer are credentials. The connector uses the fixed endpoint above and rejects command-line endpoint overrides. Product passwords and underlying provider credentials are not requested by this package.

### Retrying draft creation

Use a fresh UUID `requestId` for each new draft. Reuse that same ID only when retrying the identical request after an uncertain response. A changed brief or slide deck is a new request. Return the saved draft ID and review link; do not describe a saved draft as rendered or published content.

## Troubleshooting

- **No tools or insufficient permissions:** reconnect through browser consent and check the selected account.
- **An empty list:** confirm that the account owns the expected items. Empty results are different from a failed request.
- **A link asks you to sign in:** open it with the owning product account; a private product link is not a public share link.
- **The browser blocks authorization:** inspect the browser’s displayed error and restart an expired request from the client. Never send cookies or tokens in an issue.

## Add the companion skill

The [Postraid agent skill](https://github.com/postraid/agent-skill) explains how to select the right records, interpret results and respect the workflow’s limits:

```sh
npx skills add postraid/agent-skill
```

## Learn more about Postraid

- [Social-content workspace](https://www.postraid.com/)
- [From brief to content library](https://www.postraid.com/#how-it-works)
- [Reaction clip library](https://www.postraid.com/#reaction-library)
- [AI meme generator](https://www.postraid.com/tools/ai-meme-generator)
- [TikTok scheduling workflow](https://www.postraid.com/tools/tiktok-scheduler)
- [Content tools](https://www.postraid.com/tools)
- [Product and content articles](https://www.postraid.com/blog)

## Development and support

```sh
npm ci
npm test
npm run bundle
```

[Report a connector issue](https://github.com/postraid/mcp-server/issues) with your client, Node.js version and a redacted error. Keep `package.json`, `manifest.json`, `server/config.json`, `server.json` and the lockfile version aligned for releases. GitHub Actions publishes versioned npm packages and MCPB assets. See [LICENSE](https://github.com/postraid/mcp-server/blob/main/LICENSE) for the MIT license.
