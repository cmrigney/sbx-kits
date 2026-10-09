# DesignCraft kit

An sbx mixin that installs [DesignCraft](https://github.com/storytold/designcraft) (page
layout and publishing, in the style of InDesign) in the sandbox and registers its MCP
server with the agent.

## Usage

Headless: no display needed. The agent gets every document tool and renders to check its work:

```sh
sbx run --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=designcraft" claude .
```

With the app window on your desktop, so you can watch the agent work and take over:

```sh
sbx settings set platform.allowExperimentalFeatures true   # once
sbx settings set feature.sandbox-display true              # once
sbx run --display --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=designcraft" claude .
```

With `--display`, DesignCraft starts on the workspace directory with its control channel on
`127.0.0.1:7979`, and the MCP server drives that window. This also turns on the
window-only tools (screenshots, clicks, menus, dialogs).

## What you get

- DesignCraft 0.4.0 from the upstream release (arm64 or x86_64, checksum-verified),
  in `/opt/designcraft`
- `designcraft`: opens the app on the sandbox display with the control channel turned on
- `designcraft-cli`: the upstream CLI (headless rendering, scripting, `mcp`)
- `designcraft-mcp`: the MCP server registered as `designcraft` with `claude` or `codex`
- Software rendering through Mesa lavapipe (Vulkan) and llvmpipe. No GPU needed.

## How the MCP mode is chosen

The agent CLI launches `designcraft-mcp` when it starts. If the sandbox has a display, the script
waits up to 20 s for the app's control port, then connects to it. Otherwise, or if the app
never comes up, it runs headless. You can override this with environment variables:

| Variable | Default | Meaning |
|---|---|---|
| `DESIGNCRAFT_MCP_MODE` | `auto` | `app` waits for the app even without a display; `headless` never connects |
| `DESIGNCRAFT_MCP_WAIT` | `20` | Seconds to wait for the app |
| `DESIGNCRAFT_CONTROL_PORT` | `7979` | Control port used by both the app and the MCP server |

The default port is unique across these kits, so you can stack several in one sandbox.

If you close the window, relaunch it from the sandbox shell with `designcraft &`. The MCP server
reconnects on its next call.

## Limitations

- The app's Open, Save and Import dialogs don't work inside the sandbox. Use the MCP tools
  or `designcraft-cli` with file paths instead.
- Only Wayland is supported. The launcher unsets `DISPLAY` because there is no X server,
  which means files can't be dragged and dropped onto the window.

## Logs

- `/tmp/designcraft-startup.log`: the app's own output
- `/tmp/designcraft-mcp-register.log`: MCP registration at startup
