# wasmcart Web Runner

A small, static browser player for [wasmcart](https://github.com/wasmcart/wasmcart) cartridges. Load a `.wasc` cartridge or compatible `.wasm` file and run it directly in the browser—no build step or local installation required.

## Features

- Drag and drop or browse for local `.wasc` and `.wasm` files
- Load a cartridge from a URL
- 2D framebuffer and WebGL2 rendering
- Stereo audio with mute control
- Keyboard and up to four gamepads
- Pause, reset, fullscreen, and optional performance statistics
- Cartridge details including resolution, graphics backend, save size, and thread support

## Run locally

Because the runner uses JavaScript modules, serve the repository over HTTP rather than opening `index.html` directly:

```sh
git clone <repository-url>
cd wasmcart-runner
python3 -m http.server 8000
```

Then open <http://localhost:8000> in a modern browser and drop a cartridge onto the player.

The application imports `wasmcart/web` and `fflate` from [esm.sh](https://esm.sh), so an internet connection is required unless those dependencies are hosted locally.

## Load a remote cartridge

Pass the cartridge URL using the `wasc` query parameter:

```text
http://localhost:8000/?wasc=https%3A%2F%2Fexample.com%2Fgame.wasc
```

Add `fullscreen` to start in the fullscreen-style layout:

```text
http://localhost:8000/?wasc=https%3A%2F%2Fexample.com%2Fgame.wasc&fullscreen
```

The cartridge server must permit cross-origin browser requests (CORS). URL-encode the value of `wasc` when constructing links.

## Controls

The runner forwards standard keyboard input to the cartridge. Its Atari-style shortcuts are:

| Action | Keyboard |
| --- | --- |
| Move | Arrow keys or `W` / `A` / `S` / `D` |
| Fire / trigger | `Alt` |
| Select | `F3` |
| Start | `F4` |
| Toggle fullscreen | `F` or double-click the viewport |
| Leave fullscreen | `Escape` |

Standard browser gamepads are detected automatically. Face buttons, shoulder buttons, triggers, analog sticks, D-pad, Start, Select, L3, and R3 are forwarded to the cartridge.

## Browser requirements

Use a current browser with support for:

- WebAssembly and ES modules/import maps
- Canvas 2D (and WebGL2 for cartridges that use it)
- Web Audio
- The Gamepad API for controller input

Some browsers require an initial click or key press before audio can begin. WASI-threaded cartridges may additionally require cross-origin isolation headers from the web server.

## How it works

All application code lives in [`index.html`](./index.html). The page loads the wasmcart browser host from a CDN, reads the cartridge into memory, and advances it once per animation frame. Frames are rendered to either a 2D canvas or WebGL2 canvas, while generated audio is buffered through the Web Audio API.

## License

[MIT](./LICENSE)
