# instagram-data-mcp (local stdio package)

<!-- mcp-name: io.github.FastSocialCo/instagram-data-mcp -->

A small local MCP server for public Instagram data, published on PyPI as `instagram-data-mcp`.
It gives Claude, Cursor and any other MCP client 28 read-only tools: profiles, posts, reels,
stories, highlights, comments, likers, followers, hashtags, places, audio, search
and a daily ranking of the 2,000 most-followed accounts.

Most people don't need this package. The same tools are available from the remote server at
`https://data.fastsocial.co/mcp`, with sign-in in the browser and nothing to install. Setup for
that is in [FastSocialCo/instagram-data-mcp](https://github.com/FastSocialCo/instagram-data-mcp).
Use this package if your client only runs local (stdio) servers.

## Run it

Get a key at https://fastsocial.co/instagram-api?ref=pypi (there is a free plan), then:

```json
{
  "mcpServers": {
    "instagram-data": {
      "command": "uvx",
      "args": ["instagram-data-mcp"],
      "env": { "IG_DATA_API_KEY": "your_key" }
    }
  }
}
```

No `uvx`? `pip install instagram-data-mcp` and use `"command": "instagram-data-mcp"` instead.

## What it does and what it sends

The whole server is [`server.py`](src/instagram_data_mcp/server.py), about 150 lines. It reads the
bundled [`openapi.json`](src/instagram_data_mcp/openapi.json) (the public spec of the Instagram
Data API) and turns each `GET` endpoint into one tool.

When a tool is called, it makes one HTTPS `GET` request to `https://data.fastsocial.co/v1/<endpoint>`
with the tool's arguments as query parameters, your key in the `X-API-Key` header and
`instagram-data-mcp` as the user agent. It returns the JSON response as is. That is the only
network request it makes.

- Every tool reads. Each one is marked `readOnlyHint: true`. None can log in to Instagram, post,
  follow, like or message.
- It writes no files and stores nothing. Your key stays in your client's config and is only ever
  sent in that header.
- No install scripts, no telemetry.

Privacy policy: https://fastsocial.co/privacy · Terms: https://fastsocial.co/terms

## Settings

| Env var | Default | |
|---|---|---|
| `IG_DATA_API_KEY` | none | Required |
| `IG_DATA_API_BASE` | `https://data.fastsocial.co` | |
| `IG_DATA_API_TIMEOUT` | `60` | Seconds |

## Tools

The tool list and what each one costs in credits are in the
[plugin README](https://github.com/FastSocialCo/instagram-data-mcp#the-27-tools) and the
[API docs](https://fastsocial.co/instagram-api/docs?ref=pypi).

## Security

Report a vulnerability to contact@fastsocial.co. See
[SECURITY.md](https://github.com/FastSocialCo/instagram-data-mcp/blob/main/SECURITY.md).

## License

MIT
