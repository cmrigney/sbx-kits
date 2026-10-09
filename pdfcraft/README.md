# PdfCraft kit

An sbx mixin that installs [PdfCraft](https://github.com/storytold/pdfcraft) (reading,
organizing and protecting PDFs, in the style of Acrobat) in the sandbox and registers its
MCP server with the agent.

## Usage

```sh
sbx run --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=pdfcraft" claude .
```

The agent works on PDFs through PdfCraft's MCP server. To also see the PdfCraft window
on your desktop, create the sandbox with `--display` (an experimental feature; enable it once):

```sh
sbx settings set platform.allowExperimentalFeatures true
sbx settings set feature.sandbox-display true
sbx run --display --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=pdfcraft" claude .
```

PdfCraft's MCP server is **headless only**: it never drives the window. The window has its
own control channel, which the agent can use from the shell via `pdfcraft-cli ui --control …`
for inspecting, clicking and screenshots.

## What you get

- PdfCraft 0.4.0 from the upstream release (arm64 or x86_64, checksum-verified),
  in `/opt/pdfcraft`
- `pdfcraft`: opens the app on the sandbox display, with a control file at
  `~/.local/state/pdfcraft/control.json`
- `pdfcraft-cli`: the upstream CLI (`info`, `render`, `text`, `combine`, `split`, `run`, `mcp`, `ui`, …)
- `pdfcraft-mcp`: the MCP server registered as `pdfcraft` with `claude` or `codex`
- Software rendering through Mesa lavapipe (Vulkan) and llvmpipe. No GPU needed.

## MCP server options

| Variable | Default | Meaning |
|---|---|---|
| `PDFCRAFT_MCP_ROOT` | the workspace | Every file the server reads or writes must be inside this directory |

## Limitations

- The app's Open, Save and Import dialogs don't work inside the sandbox. Use the MCP tools
  or `pdfcraft-cli` with file paths instead.
- Only Wayland is supported. The launcher unsets `DISPLAY` because there is no X server,
  which means files can't be dragged and dropped onto the window.

## Logs

- `/tmp/pdfcraft-startup.log`: the app's own output
- `/tmp/pdfcraft-mcp-register.log`: MCP registration at startup
