# EffectCraft kit

An sbx mixin that installs [EffectCraft](https://github.com/storytold/effectcraft) (motion
graphics and visual effects, in the style of After Effects) in the sandbox and registers
its MCP server with the agent.

## Usage

Headless: no display needed. The agent gets every document tool and renders to check its work:

```sh
sbx run --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=effectcraft" claude .
```

With the app window on your desktop, so you can watch the agent work and take over:

```sh
sbx settings set platform.allowExperimentalFeatures true   # once
sbx settings set feature.sandbox-display true              # once
sbx run --display --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=effectcraft" claude .
```

With `--display`, EffectCraft starts on the workspace directory with its control channel on
`127.0.0.1:9877`, and the MCP server drives that window. This also turns on the
window-only tools (screenshots, clicks, menus, dialogs).

## What you get

- EffectCraft 0.6.0 from the upstream release (arm64 or x86_64, checksum-verified),
  in `/opt/effectcraft`
- `effectcraft`: opens the app on the sandbox display with the control channel turned on
- `effectcraft-cli`: the upstream CLI (headless rendering, scripting, `mcp`)
- `effectcraft-mcp`: the MCP server registered as `effectcraft` with `claude` or `codex`
- Software rendering through Mesa lavapipe (Vulkan) and llvmpipe. No GPU needed.

## How the MCP mode is chosen

The agent CLI launches `effectcraft-mcp` when it starts. If the sandbox has a display, the script
waits up to 20 s for the app's control port, then connects to it. Otherwise, or if the app
never comes up, it runs headless. You can override this with environment variables:

| Variable | Default | Meaning |
|---|---|---|
| `EFFECTCRAFT_MCP_MODE` | `auto` | `app` waits for the app even without a display; `headless` never connects |
| `EFFECTCRAFT_MCP_WAIT` | `20` | Seconds to wait for the app |
| `EFFECTCRAFT_CONTROL_PORT` | `9877` | Control port used by both the app and the MCP server |

The default port is unique across these kits, so you can stack several in one sandbox.

If you close the window, relaunch it from the sandbox shell with `effectcraft &`. The MCP server
reconnects on its next call.

## Limitations

- The app's Open, Save and Import dialogs don't work inside the sandbox. Use the MCP tools
  or `effectcraft-cli` with file paths instead.
- Only Wayland is supported. The launcher unsets `DISPLAY` because there is no X server,
  which means files can't be dragged and dropped onto the window.
- The sandbox has no sound device, so playback is silent. Exporting audio to files works.

## Logs

- `/tmp/effectcraft-startup.log`: the app's own output
- `/tmp/effectcraft-mcp-register.log`: MCP registration at startup
