# Entrega v2.5.2 — rostro_animado (tracking MediaPipe)

## OPCIÓN A (recomendada): reemplazo total
Sustituye TU carpeta `rostro_animado/` COMPLETA por la del tar adjunto
(`../rostro_animado_v2.5.2.tar.gz`, sha256: 1ccce045d6234e299fb272ca589422d63e2b56246b94e11b476f7aad05f1b15d).
Así eliminas de un golpe cualquier archivo huérfano de builds antiguas (p. ej. el app.js?v=25a que muestra tu consola y que NO existe en esta rama).

## OPCIÓN B: solo los archivos modificados/añadidos (overlay sobre tu repo)
Copia estos 14 archivos respetando las rutas:

Modificados:
- call.html                        (cache-busters ?v=252)
- src/tracking/MpLoader.js         (fetch+validación estricta, VENDOR_BASE getter baseURI, reintento con URL única)
- src/tracking/Backends.js         (Worker con ?v=único-por-carga, onWarning cableado)
- src/tracking/TrackingBridge.js   (F1: epoch sin `|| stopped`; F2: absUrl bundleUrl/wasmPaths/modelUrl; F4: onWarning)
- src/tracking/track.worker.js     (fetch no-store + validación HTTP/HTML/firma + reintento cache-buster)

Nuevos (imprescindibles — son la causa raíz del error actual):
- assets/vendor/mediapipe/vision_bundle.mjs          (~136 KB)
- assets/vendor/mediapipe/face_landmarker.task       (~3.6 MB)
- assets/vendor/mediapipe/wasm/vision_wasm_internal.js
- assets/vendor/mediapipe/wasm/vision_wasm_internal.wasm
- assets/vendor/mediapipe/wasm/vision_wasm_nosimd_internal.js
- assets/vendor/mediapipe/wasm/vision_wasm_nosimd_internal.wasm
- tools/fetch-mediapipe.sh                           (regenerador idempotente de los binarios)
- tools/selftest.html                                (autochequeo en el navegador)

index.html y site/* están SIN cambios en esta rama (no es necesario tocarlos).

## Instalación y verificación
1. Copia los archivos (Opción A o B).
2. Sirve la carpeta desde su raíz: `python3 -m http.server 8080 --directory <ruta>/rostro_animado`
3. Abre `http://localhost:8080/tools/selftest.html` → debe decir VEREDICTO: TODO OK.
4. Recarga dura (Ctrl+Shift+R) de call.html/index.html.
5. Si GitHub Pages está implicado: commitea también `assets/vendor/` (viaja en el repo a propósito, ~22 MB).

Nota: si tras esto persiste `app.js?v=25a:292`, ese app.js NO es de esta rama
(en esta rama app.js tiene 885 líneas y ninguna función failTracking/bindTrackingToMain).
