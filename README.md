# DrishtiType 👁️⌨️

**Eye-controlled text entry in the browser — an accessibility-focused prototype.**

DrishtiType explores how webcam-based eye tracking can help a person move a pointer, select on-screen keys, and compose text without relying on a conventional mouse or keyboard. It uses browser-based face/iris landmarks and a personalised calibration workflow to estimate gaze direction.

> **Status: experimental prototype.** DrishtiType has not been clinically validated and must not be relied on as a sole or emergency communication method. Tracking quality can vary substantially with camera placement, lighting, glasses, head movement, device performance, and individual eye movement.

## ✨ What it explores

- **Webcam-based eye tracking** using MediaPipe Tasks Vision face landmarks and eye-related features.
- **Personalised calibration** using known on-screen targets to pair observed eye features with target screen coordinates.
- **Browser-side gaze estimation** intended to adapt to the current user's calibration samples.
- **On-screen text entry** with gaze-based targeting and blink-based selection.
- **Word suggestions and speech output** to explore a more accessible communication workflow.
- **Local browser processing** for the calibration and gaze-estimation workflow; this prototype does not require a custom model-training server.

Feature availability and performance depend on the current source and browser environment. Please verify behavior on your own device before relying on any feature.

## 🧠 How the gaze pipeline works

1. **Camera input** — the browser requests webcam access after the user starts tracking.
2. **Landmark extraction** — MediaPipe estimates facial and eye landmarks from video frames.
3. **Calibration** — the user looks at known screen targets. The app collects eye-feature samples associated with each target's screen coordinates.
4. **Personalised model fitting** — the current implementation includes a small multilayer perceptron (MLP) intended to learn the mapping from eye features to screen coordinates from the calibration samples.
5. **Live inference** — the fitted model estimates a screen location from incoming eye features; pointer filtering and target-selection logic help make interaction usable.
6. **Text output** — the user can select on-screen keys and use the app's text/speech features.

**Important:** a neural network in the code is not proof of accurate gaze tracking. The implementation has not been validated on a representative real-world user dataset, and synthetic tests do not establish real-world accuracy. Calibration quality and real-camera evaluation remain essential.

## 🚀 Run locally

This is a client-side HTML prototype. No build step is required.

1. Download or clone this repository.
2. Open `index.html` in a modern browser, or serve the folder over localhost.
3. Allow camera access when prompted.
4. Position the camera at a stable, comfortable angle with even lighting.
5. Complete the calibration carefully and test the pointer on multiple known screen targets.

For a local HTTP server with Python installed:

```bash
python -m http.server 8000
```

Then open <http://localhost:8000>.

Camera permissions and browser security rules may differ between `localhost` and deployed HTTPS sites. Use HTTPS for deployment.

## 🧪 Testing and validation

Before describing the app as accurate or using it for essential communication, evaluate it systematically:

- Test centre, edges, and corners after calibration.
- Use separate validation targets that were not used for training.
- Record gaze-point error in pixels or degrees of visual angle, plus target-selection success rate.
- Measure calibration time, missed selections, unintended selections, and performance over time.
- Repeat across lighting conditions, camera distances, glasses, users, and devices—with informed consent.
- Compare results against a baseline and report sample size and limitations.

Do not claim clinical effectiveness from a successful syntax check or synthetic-data test. This repository currently does not provide independently verified real-world accuracy metrics.

## 🔐 Privacy

The intended design performs eye-feature extraction, calibration, and inference in the browser. Review the source and any third-party library behavior before deployment. Camera access requires user permission. Do not collect, store, or publish face/eye data without clear notice and informed consent.

## ♿ Accessibility and safety

DrishtiType is an experimental accessibility project, not a medical device and not a substitute for a professionally assessed assistive communication system. Provide an alternative input method wherever possible. Do not rely on it for emergency calls or other time-critical communication until it has been independently tested for that purpose.

## 🛠️ Technology

- HTML, CSS, and JavaScript
- MediaPipe Tasks Vision / Face Landmarker
- Browser camera APIs
- A small calibration-trained MLP gaze estimator
- Browser speech synthesis (where supported)

## 🗺️ Roadmap

- [ ] Expand calibration sampling and improve outlier rejection.
- [ ] Add a dedicated validation mode with held-out targets and error reporting.
- [ ] Track confidence and show when gaze estimates are unreliable.
- [ ] Add guided recalibration and drift detection.
- [ ] Test across devices, lighting conditions, and a diverse set of consenting users.
- [ ] Publish reproducible evaluation results before making accuracy claims.
- [ ] Improve keyboard navigation, reduced-motion support, and alternative input paths.

## 🤝 Contributing

Issues and thoughtful contributions are welcome. When reporting a tracking problem, include your browser, device/camera setup, lighting conditions, calibration procedure, and reproducible observations. Avoid posting personal face or eye recordings publicly.

For code changes, please explain the problem being addressed and how you tested it. Accuracy-related changes should include quantitative evaluation rather than subjective claims alone.

## 📄 License

No license has been assigned yet. Unless a license is added, the default copyright rules apply and reuse is not automatically permitted. Add a license file before inviting external reuse or contributions under specific terms.

## 👤 Maintainer

Created by **Avadh Gupta**.

---

*Built to explore a simple idea: make digital communication more accessible, one interaction at a time.*
