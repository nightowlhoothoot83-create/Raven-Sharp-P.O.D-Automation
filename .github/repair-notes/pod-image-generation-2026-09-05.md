# POD image generation repair — 2026-09-05

Confirmed issues to repair and verify on this branch:

1. `generate_images()` ignores the submitted `payload.aspect_ratio` when no style profile is selected and forces `square`.
2. `/api/image-gen/batches` returns full stored base64 image payloads for up to 20 batches while the frontend polls every 3 seconds during processing. This is unnecessarily large and can make generation/history appear stalled, especially on mobile.
3. Generated image display depends on the provider URL even though the backend already stores image bytes. The final repair must provide a stable authenticated image route or stable storage URL instead of relying only on the provider URL.
4. A style profile reference image currently uses `referenceImages`; current Runware FLUX reference workflows must be checked against the supported `ipAdapters`/`guideImage` or `seedImage` contract before changing this path.
5. Do not change the default generation model blindly: `runware:101@1` is a valid FLUX.1 Dev model.
6. The direct-main commit `d55e5ef558da3fb1048df69f014e4479829ccfc9` was detected during this audit and already deployed by Railway without the required PR/approval gate. Treat it as unverified until end-to-end generation is tested.

Acceptance criteria:
- a plain prompt with no style profile returns and displays at least one generated image;
- square, portrait, landscape and wide selections reach the backend with their requested dimensions;
- generation progress polling does not transfer embedded base64 payloads repeatedly;
- generated images remain viewable/downloadable without depending on an expiring provider URL;
- provider errors are surfaced clearly in History;
- retry preserves prompt controls without double charging for already successful images;
- GitHub tests pass;
- Railway branch/preview or equivalent backend verification passes;
- real prompt-to-image test passes before user approval, merge and deployment.
