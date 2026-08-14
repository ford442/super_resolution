# unlimited:waifu2x (nunif web)

WebGPU & WebAssembly (SIMD/Threaded) Super-Resolution Web Application based on [nunif/waifu2x](https://github.com/nagadomi/nunif).

## Features & Resources Included

- **Web Frontend**:
  - `index.html`: Main UI with interactive options for models, denoising levels, scaling (1x, 2x, 4x), tiling, TTA ensemble, and alpha channel handling.
  - `style.css`: Stylesheet.
  - `simd.1ijs` / `script.js`: Client-side inference orchestration with WebGPU / WebNN / WASM SIMD fallback, tiling, seam blending, and image preprocessing/postprocessing.
  - `vendor/`: Local offline copies of jQuery, jQuery Cookie, and WebDNN.

- **ONNX Runtime Web (`ort/`)**:
  - `ortall.1ijs` / `ort.all.min.js`: ONNX Runtime Web v1.21.0.
  - `ort-wasm-simd-threaded.wasm` & `ort-wasm-simd-threaded.jsep.wasm`: Multi-threaded SIMD & WebGPU JSEP WASM runtimes.

- **ONNX Models (`models/`)**:
  - `models/swin_unet/art/` (15 models: scale2x, scale4x, scale1x, noise0..3, noise0..3_scale2x, noise0..3_scale4x)
  - `models/swin_unet/art_scan/` (15 models)
  - `models/swin_unet/photo/` (15 models)
  - `models/cunet/art/` (10 models: scale2x, scale1x, noise0..3, noise0..3_scale2x)
  - `models/utils/` (7 helper models: seam blending filter, TTA split/merge, alpha border padding, antialias, replication/reflection pad)

## Running Locally

Because WebGPU and SharedArrayBuffer (for multi-threaded WASM) require specific HTTP headers (`Cross-Origin-Opener-Policy: same-origin` and `Cross-Origin-Embedder-Policy: require-corp`), you can serve the directory using a simple local server.

### Option 1: Python HTTP Server (Basic)
```bash
python3 -m http.server 8080
```
Then open `http://localhost:8080` in your browser.

### Option 2: Python HTTP Server with COOP/COEP (Recommended for Multi-threading & WebGPU)
```bash
python3 -c "
import http.server, socketserver

class Handler(http.server.SimpleHTTPRequestHandler):
    def end_headers(self):
        self.send_header('Cross-Origin-Opener-Policy', 'same-origin')
        self.send_header('Cross-Origin-Embedder-Policy', 'require-corp')
        super().end_headers()

with socketserver.TCPServer(('', 8080), Handler) as httpd:
    print('Serving at http://localhost:8080')
    httpd.serve_forever()
"
```
