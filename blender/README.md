# Blender kit

An sbx mixin that installs Blender in the sandbox and runs it on the host
display, with [Blender Lab's MCP server](https://www.blender.org/lab/mcp-server/)
wired up so the agent can drive it.

## Requirements

Blender needs a real display surface, which is an experimental sbx feature.
Enable it once:

```sh
sbx settings set platform.allowExperimentalFeatures true
sbx settings set feature.sandbox-display true
```

## Usage

The sandbox must be created with `--display`:

```sh
sbx run --display --kit "git+https://github.com/cmrigney/sbx-kits.git#dir=blender" claude .
```

Blender starts automatically on the workspace directory and its window appears
on your desktop. If you launch `blender` without `--display`, the wrapper tells
you so and exits.

## What you get

- Blender (software OpenGL via llvmpipe — no GPU needed)
- The Blender MCP server, registered with `claude` or `codex` in the sandbox
- Notes for the agent on FBX export, Unity axis/scale conventions, and known
  MCP gotchas (see `agentInstructions` in `spec.yaml`)

## Logs

- `/tmp/blender-startup.log` — Blender's own output
- `/tmp/blender-mcp-register.log` — MCP registration
