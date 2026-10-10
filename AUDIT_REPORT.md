# DrishtiType — Application Audit Report

**Audit date:** 2026-10-10  
**Repository:** https://github.com/avadhgupta64-png/drishtitype  
**Production app:** https://drishtitype-lime.vercel.app/  
**Scope:** Static source review, Vercel deployment metadata, and browser smoke testing.  
**Important limitation:** This is an engineering audit, not clinical validation or a guarantee of assistive-communication safety.

## Executive summary

- The production deployment reports **READY** on Vercel and is tied to commit `cb965f59194cf1e7ec30f587488a4fbb9ba6ed0d` (`Fix number keyboard and add punctuation mode`).
- A no-camera browser smoke test of the production URL found the app loaded and the core message composition and demo-mode UI were available. No visible initial-load error was observed.
- Static review identified a crash risk: malformed or incompatible calibration data in browser storage was parsed during script startup without validation. The audit branch now validates saved calibration shape and numeric values, rejects incompatible data, and handles browser storage read/write/clear failures without taking down the app.
- The audit fix is deployed as a Vercel **preview** and reports `READY`. TinyFish could not access the preview URL during its follow-up run (it returned `DEPLOYMENT_NOT_FOUND`), so that run did not verify the fix in a browser. The change is **not merged to production**.
- Webcam tracking, real calibration, blink selection, actual speech output, and long-session reliability were not validated by this audit.

## What was checked

### Production browser smoke test (TinyFish; camera permission not granted)

| Area | Result | Evidence / limitation |
|---|---|---|
| Initial page load | Pass | App rendered; no visible initial-load error |
| Message input | Pass | Text entry worked |
| Word suggestions | Pass | Clicking “Hello” inserted `Hello ` |
| Letter keyboard | Pass | A–Z keys visible |
| Demo mouse mode | Pass | Status/debug text changed and pointer followed mouse |
| Space / Backspace / Undo / Clear | UI pass | Controls visible and interactive; not all state combinations exhaustively verified |
| Speak message | UI pass only | Button present; actual audible speech and browser voice support not verified |
| Start / Stop tracking | UI pass only | Controls present; camera permission intentionally not granted |
| Number/punctuation keyboard | Pass | Follow-up production regression test confirmed letter → number → punctuation → letter and verified digit/punctuation insertion |
| Privacy info | Pass | Follow-up production regression test opened the privacy popup and dismissed it with Escape |
| Camera, calibration, blink-to-select | Not tested | No webcam permission or real gaze measurements collected |
| Mobile layout / screen-reader behavior | Not fully tested | No comprehensive device/accessibility matrix run |

The automated runs did not provide browser DevTools console output, so “no visible error” must not be interpreted as “no console errors”. Follow-up regression testing confirmed number/punctuation layout transitions, typed letter/digit/punctuation input, Undo, Clear, and the privacy popup on production.

### Static source review

- **Saved calibration parsing — issue found and fix prepared.** `JSON.parse(localStorage.getItem(...))` previously ran unguarded at startup. Malformed JSON could stop the module before the interface initialized. The audit branch validates the expected MLP model structure, array dimensions, and finite numeric values, discards invalid/incompatible models, and safely handles storage access failures.
- **Storage persistence — hardened.** Saving or clearing calibration now fails gracefully if browser storage is unavailable or throws (for example, restricted storage modes). A calibration can still be used in the current page session if it cannot be saved.
- **No repository license file found.** README explicitly states that no license has been assigned. Add a suitable license before accepting external reuse or contributions.
- **No automated test suite/build script found in the reviewed root files.** The app is a single HTML file with a browser-imported MediaPipe module. Add repeatable unit/smoke tests and a documented release checklist.
- **Privacy claims need care.** The app's own gaze processing is intended to run in-browser, but it loads MediaPipe code and a model from third-party CDN/Google-hosted URLs. The privacy notice should distinguish local camera-frame processing from the external requests needed to download code/model assets.
- **Assistive-use safeguards remain incomplete/unverified.** There is no independently verified gaze-error or false-selection rate in this audit. Do not use this prototype as the sole or emergency communication channel. Provide an alternative input method and test with consenting users before making performance claims.
- **Runtime robustness still needs real-device tests.** Calibration cancellation/stop behavior, face-loss recovery, storage persistence, blink false positives, and extended-session drift should be tested deliberately. This audit did not establish whether these cases reproduce.

## Fix prepared

**Branch:** `audit/fix-corrupted-calibration-model`  
**Latest commit:** `507d24e85d85981140673dd55eb5dc0f219cc3fc`  
**Preview deployment:** `https://drishtitype-qkjzyoqmu-avadhs-projects-2db9a2a3.vercel.app/` (Vercel metadata reports READY; automated browser follow-up returned 404/DEPLOYMENT_NOT_FOUND, so browser validation is blocked)  
**Production status:** Unchanged; fix has not been merged.

The change is intentionally limited to safe loading and persistence of saved calibration data. It does not claim to improve gaze accuracy.

## Recommended next steps

1. Confirm the audit preview is reachable to the browser test environment, then verify malformed-storage recovery and keyboard-mode transitions on the preview.
2. Run syntax/runtime checks and automated regression tests for letter, number, punctuation, undo, clear, suggestions, speech, camera startup failure, stop-during-startup, calibration cancellation, and face-loss recovery.
3. Test real webcam calibration and held-out targets on multiple devices, recording calibration success, target error, false selections, missed selections, and session drift.
4. Add an appropriate repository license and automated tests.
5. Review third-party asset requests and revise privacy wording to be precise.
6. Merge the fix only after the preview checks pass.

## Audit conclusion

**Production: smoke-tested with limitations. Fix: prepared, preview deployment marked READY, but browser validation blocked.** This is not a clean bill of health for gaze accuracy or real-world assistive use. The calibration-storage crash risk is addressed on the audit branch, pending accessible preview verification and review before production merge.
