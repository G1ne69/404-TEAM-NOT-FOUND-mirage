# MIRAGE
### AI Vision Failure Testing & Diagnosis Workbench

**Making computer vision failures visible, measurable, and reproducible.**

MIRAGE is an interactive computer-vision robustness testing workbench designed to investigate how object-detection models behave under controlled, real-world-inspired image distortions.

Modern AI vision systems can perform well on clean images yet behave differently when exposed to poor lighting, blur, shadows, reduced contrast, or partial obstruction. MIRAGE provides a systematic way to evaluate these changes by running a real object-detection model on original and perturbed images, comparing predictions against verified ground-truth annotations, and measuring changes in detection performance.

Rather than treating a successful prediction as sufficient evidence of reliability, MIRAGE encourages developers to ask a more important question:

> **When does an object detector stop detecting an object it could detect before, and what evidence can help us investigate that failure?**

MIRAGE turns this question into a reproducible experiment.

---

## Table of Contents

- [The Problem](#-the-problem)
- [Our Solution](#-our-solution)
- [Why MIRAGE Matters](#-why-mirage-matters)
- [Key Features](#-key-features)
- [How It Works](#-how-it-works)
- [Controlled Perturbation Suite](#-controlled-perturbation-suite)
- [Evaluation Methodology](#-evaluation-methodology)
- [Metrics and Failure Analysis](#-metrics-and-failure-analysis)
- [Scientific Integrity and Limitations](#-scientific-integrity-and-limitations)
- [Technology Stack](#-technology-stack)
- [System Architecture](#-system-architecture)
- [Installation and Quickstart](#-installation-and-quickstart)
- [Running Tests](#-running-tests)
- [Experiment Reports and Reproducibility](#-experiment-reports-and-reproducibility)
- [Real-World Applications](#-real-world-applications)
- [Potential Business Model](#-potential-business-model)
- [Current Scope and Future Roadmap](#-current-scope-and-future-roadmap)
- [Responsible Use](#-responsible-use)
- [Contributing](#-contributing)
- [License and Attribution](#-license-and-attribution)

---

## 🔍 The Problem

Object detection is increasingly used in manufacturing, robotics, agriculture, transportation, retail, and other environments where decisions depend on visual information.

However, images encountered outside controlled benchmarks may contain:

- Low illumination and underexposure.
- Excessive brightness and harsh lighting.
- Defocus and motion blur.
- Shadows and uneven illumination.
- Partial object occlusion.
- Reduced contrast caused by haze-like conditions.
- Strong contrast variations.

These conditions can change the appearance of objects and influence model predictions.

A detector may produce a lower-confidence prediction, localize an object differently, generate an unmatched detection, or fail to detect an annotated object altogether.

A model's performance on clean images alone does not establish how reliably it will perform under every environmental condition.

### The gap MIRAGE addresses

Developers can manually edit images and rerun inference, but this can become difficult to manage systematically across multiple images, distortion levels, model configurations, and evaluation settings.

MIRAGE aims to make this process more structured by combining controlled image perturbations, real model inference, ground-truth-based evaluation, failure analysis, and replayable experiment records in one visual workbench.

The objective is not to assume that a model will fail. It is to discover, measure, and document what actually happens.

---

## 💡 Our Solution

MIRAGE provides an interactive workflow for investigating object-detection robustness.

Users can load an available pretrained detector, evaluate labelled images, apply controlled image transformations, and compare the resulting predictions with a baseline.

The evaluation pipeline uses ground-truth annotations to distinguish between successful detections, missed objects, and unmatched predictions. Aggregate metrics summarize performance changes, while per-object results help identify specific detection regressions.

Each experiment can be recorded with its configuration and replayed to support reproducible investigation.

**Core principle:** Observe first, measure second, hypothesize third.

MIRAGE reports empirical evidence separately from possible explanations of model behavior.

---

## 🎯 Why MIRAGE Matters

MIRAGE focuses on a practical engineering challenge: understanding the conditions under which an object-detection model may become less reliable.

Its value comes from bringing several capabilities together:

- **Controlled experimentation:** Change selected image conditions while keeping the model and other evaluation settings consistent.
- **Quantitative evaluation:** Measure precision, recall, missed detections, false positives, and relevant confidence changes.
- **Failure traceability:** Identify which ground-truth objects were detected in the baseline but missed after a transformation.
- **Reproducibility:** Record experiment settings and deterministic perturbation seeds.
- **Evidence-based diagnosis:** Present measured behavior separately from hypotheses about why a failure occurred.
- **Accessible visualization:** Make model behavior easier to inspect without requiring users to interpret raw inference outputs alone.

The intended outcome is a more systematic model-testing workflow that can support development, research, debugging, and pre-deployment evaluation.

---

## ✨ Key Features

### 1. Real Object-Detection Inference

MIRAGE is designed to use actual pretrained object-detection models rather than simulated predictions.

Supported model choices in the planned setup include:

- YOLO11n
- YOLOv8n as a fallback option

Inference can run locally on a CPU, reducing the need for dedicated GPU infrastructure for small experiments.

Model availability, download success, and compatibility depend on the local environment and installed dependencies.

### 2. Labelled Benchmark Evaluation

MIRAGE supports small public labelled datasets, including COCO8 and COCO128, for evaluating predictions against annotated objects.

Ground-truth annotations provide the reference needed to calculate detection metrics instead of relying solely on model confidence.

### 3. Seven Controlled Image Perturbations

The perturbation suite covers selected variations in exposure, blur, shadows, occlusion, and contrast.

Each transformation can be configured and reproduced using its recorded parameters and, where applicable, a deterministic random seed.

### 4. Baseline-versus-Perturbed Comparison

Run the detector on an original image and a modified version using consistent inference settings.

Compare predicted labels, bounding boxes, confidence scores, and ground-truth-based evaluation outcomes.

### 5. Ground-Truth-Based Failure Analysis

MIRAGE uses class-aware, one-to-one matching between predictions and ground-truth boxes.

This supports identification of:

- Correctly detected objects.
- Missed ground-truth objects.
- Unmatched predictions.
- Objects lost relative to a successful baseline detection.
- Changes in detection performance across perturbation conditions.

### 6. Quantitative Robustness Metrics

Aggregate results help users examine whether selected distortions are associated with changes in precision, recall, or confidence.

Metrics are calculated from model outputs and ground-truth annotations using documented evaluation settings.

### 7. Reproducible Experiment Records

Experiment manifests store relevant configuration information, including model and dataset identifiers, perturbation settings, evaluation thresholds, software versions, and random seeds where applicable.

JSON manifests make experiment configurations inspectable and easier to exchange than opaque binary experiment records.

### 8. Reports and Replay

Saved experiment reports support later inspection and replay through the application's reporting workflow.

Reproducibility depends on retaining the relevant model weights, dataset version, software environment, inference settings, and perturbation implementation. A matching configuration does not by itself guarantee identical results across every software or hardware environment.

---

## ⚙️ How It Works

MIRAGE follows a structured evaluation pipeline.

1. **Load the model:** Initialize a supported pretrained object detector.
2. **Load labelled data:** Obtain images and their corresponding ground-truth annotations.
3. **Establish a baseline:** Run inference on the original images and evaluate the predictions.
4. **Apply a perturbation:** Modify selected image properties using a controlled transformation.
5. **Run inference again:** Process the perturbed images with the same model and inference settings.
6. **Match predictions:** Associate predicted bounding boxes with ground-truth boxes using class-aware IoU matching.
7. **Measure changes:** Calculate evaluation metrics and identify relevant detection regressions.
8. **Record the experiment:** Save the configuration and measured outcomes for inspection and replay.

### Conceptual workflow

```text
       Labelled Dataset
              |
              v
     Original Images
              |
              v
      Baseline Inference
              |
              +---------------------+
              |                     |
              v                     v
       Baseline Results       Controlled Image
                                    Perturbation
                                         |
                                         v
                                  Modified Image
                                         |
                                         v
                                 Perturbed Inference
                                         |
                    +--------------------+
                    |
                    v
           Ground-Truth Matching
                    |
                    v
           Metric Calculation
                    |
                    v
       Regression and Error Analysis
                    |
                    v
       JSON Report and Experiment Replay
```

This workflow is intended to isolate the effects of selected image changes while retaining a traceable record of the experiment.

---

## 🌦️ Controlled Perturbation Suite

MIRAGE includes seven planned perturbation categories designed to represent selected visual challenges.

| Perturbation | What it simulates | What can be investigated |
|---|---|---|
| Dark Exposure | Low-light or underexposed images | Whether detections change as image brightness decreases |
| Bright Exposure | Overexposure or harsh illumination | Whether excessive brightness affects detection performance |
| Gaussian Blur | Defocus or blur-like image degradation | Whether reduced image detail affects localization or detection |
| Synthetic Shadows | Uneven illumination from shadow regions | Whether shadow placement and strength affect predictions |
| Patch Occlusion | An object partially hidden by an obstruction | Whether partial visibility affects detection |
| Low Contrast | Haze-like reduction in image contrast | Whether weaker foreground-background separation affects detections |
| High Contrast | Increased contrast and compressed visual detail | Whether altered intensity ranges affect predictions |

The transformations are controlled approximations of selected visual conditions, not complete physical simulations of every environment.

For example, Gaussian blur does not reproduce every property of real camera motion, and synthetic shadows do not fully model the geometry and illumination of real-world scenes.

These distinctions matter when interpreting results.

---

## 📊 Evaluation Methodology

MIRAGE evaluates predictions against ground-truth bounding-box annotations.

### Intersection over Union (IoU)

Intersection over Union measures the overlap between a predicted bounding box and a ground-truth box.

\[
\operatorname{IoU}(B_p,B_g)
=
\frac{|B_p\cap B_g|}{|B_p\cup B_g|}
\]

Where:

- \(B_p\) represents a predicted bounding box.
- \(B_g\) represents a ground-truth bounding box.
- The intersection is the overlapping area.
- The union is the total area covered by both boxes.

The default matching threshold is IoU ≥ 0.50.

A prediction must satisfy the configured IoU threshold and class-matching rule to qualify as a valid match.

### One-to-One Matching

MIRAGE uses a documented one-to-one greedy matching procedure.

The objective is to ensure that:

- One prediction cannot count as a match for multiple ground-truth objects.
- One ground-truth object cannot be counted as detected by multiple predictions.
- Duplicate or unmatched predictions are not incorrectly counted as additional true positives.

The order and selection criteria of the matching algorithm must remain consistent between baseline and perturbed evaluations.

The default confidence cutoff determines which predictions are eligible for evaluation.

### Ground-Truth-Based Counts

| Outcome | Definition |
|---|---|
| True Positive (TP) | A prediction matched to a ground-truth object under the configured matching rules |
| False Negative (FN) | A ground-truth object that remains unmatched |
| False Positive (FP) | A prediction that remains unmatched |

These definitions make it possible to distinguish detection performance from changes in confidence alone.

---

## 📈 Metrics and Failure Analysis

### Precision

Precision measures the fraction of evaluated predictions that match ground-truth objects.

\[
\operatorname{Precision}
=
\frac{TP}{TP+FP}
\]

A decrease in precision can indicate that a larger proportion of predictions are unmatched under the selected evaluation settings.

### Recall

Recall measures the fraction of ground-truth objects successfully detected.

\[
\operatorname{Recall}
=
\frac{TP}{TP+FN}
\]

Recall is particularly useful for investigating missed detections.

### Recall Delta

MIRAGE compares baseline and perturbed recall:

\[
\Delta R=R_{\text{perturbed}}-R_{\text{baseline}}
\]

A negative value indicates that recall decreased under the selected perturbation.

A positive value indicates that recall increased under that condition.

The magnitude and interpretation of the change depend on the dataset, image subset, matching criteria, and inference settings.

### Detection Regressions

A detection regression occurs when a ground-truth object detected in the baseline evaluation becomes unmatched in the perturbed evaluation.

This analysis helps identify individual objects associated with a measured loss of detection.

The baseline detection must first satisfy the configured matching and confidence rules.

### Confidence Delta

For an object successfully detected in both evaluations, MIRAGE can compare the confidence scores associated with its matched detections:

\[
\Delta C=C_{\text{perturbed}}-C_{\text{baseline}}
\]

A negative value indicates lower confidence in the perturbed evaluation.

Confidence changes are useful diagnostic signals, but they are not equivalent to accuracy changes.

### Interpreting Results Correctly

Consider a hypothetical example:

- Baseline recall: 85%.
- Recall under a selected blur condition: 50%.
- Recall delta: −35 percentage points.

This would indicate a measured reduction in recall for that experiment, provided those figures were actually obtained from the evaluated images and annotations.

**The numbers above are illustrative, not claimed MIRAGE benchmark results.**

Meaningful comparisons require consistent evaluation settings and transparent reporting of the evaluated dataset and sample size.

---

## 🔬 Scientific Integrity and Limitations

MIRAGE is designed to distinguish measured observations from explanations that have not yet been verified.

### What MIRAGE Can Establish

When supported by correctly implemented and validated evaluation code, MIRAGE can measure:

- Changes in precision and recall across selected conditions.
- Changes in the number of matched and unmatched objects.
- Individual ground-truth objects lost relative to a baseline.
- Changes in confidence for objects detected in both evaluations.
- Differences between models under the same experimental conditions.
- The configurations associated with particular measured outcomes.

### What MIRAGE Cannot Establish on Its Own

MIRAGE does not automatically identify the true internal cause of a neural network failure.

For example, an observed detection regression does not by itself prove that the model suffers from a particular training-data bias, architecture limitation, or learned illumination sensitivity.

Those explanations require additional experiments and evidence.

Likewise, testing a small labelled dataset does not establish that the same behavior will occur across every deployment environment.

### Important Evaluation Considerations

- **Dataset representativeness:** Small public benchmarks are useful for controlled experiments but cannot represent every real-world environment.
- **Synthetic perturbations:** Controlled image transformations approximate selected visual conditions and may not reproduce their full physical complexity.
- **Threshold sensitivity:** Precision, recall, and regression counts can change when confidence or IoU thresholds change.
- **Matching limitations:** Greedy matching is a defined evaluation procedure, but it is not necessarily equivalent to every benchmark's matching or assignment protocol.
- **Statistical uncertainty:** Small samples can produce unstable estimates. Larger evaluations and uncertainty analysis are needed for stronger conclusions.
- **Determinism:** Replaying the same configuration requires consistent inputs, model weights, software behavior, and evaluation settings.

MIRAGE is a model-evaluation workbench, not a certification system or a guarantee of deployment safety.

---

## 🧰 Technology Stack

MIRAGE is built around a lightweight, locally executable computer-vision workflow.

| Technology | Purpose |
|---|---|
| Python | Evaluation logic and application implementation |
| Streamlit | Interactive workbench interface |
| Ultralytics YOLO | Pretrained object-detection inference |
| OpenCV | Image transformations and computer-vision utilities |
| NumPy | Numerical operations and image-array processing |
| JSON | Experiment configuration and report serialization |
| pytest | Unit and integration testing |

The intended setup uses CPU inference to keep small experiments accessible without dedicated GPU hardware.

Actual runtime depends on the model, image dimensions, number of images, inference settings, and available hardware.

---

## 🏗️ System Architecture

MIRAGE can be understood as five cooperating layers:

**1. Data layer**

Loads labelled images and ground-truth annotations from the selected dataset.

**2. Perturbation layer**

Applies controlled image transformations using recorded parameters and deterministic seeds where appropriate.

**3. Inference layer**

Runs the selected pretrained detector on baseline and perturbed images.

**4. Evaluation layer**

Matches predictions with ground-truth annotations and computes metrics, detection regressions, and confidence differences.

**5. Interface and reporting layer**

Displays experiment results and supports JSON report saving, inspection, and replay.

This separation makes the workflow easier to test, maintain, and extend as new models, datasets, or perturbations are introduced.

---

## 🚀 Installation and Quickstart

### Prerequisites

- macOS with a compatible Python installation.
- Python 3.10 or another Python version supported by the installed dependencies.
- Terminal using zsh or another compatible shell.
- Internet access for the initial download of model weights and datasets, unless all required assets are already cached.
- Sufficient memory and disk space for the selected model and dataset.

### 1. Clone the repository

Replace the placeholder URL with the actual GitHub repository URL.

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd mirage
```

### 2. Create a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Launch MIRAGE

```bash
streamlit run app.py
```

The application should be available at:

`http://localhost:8501`

### Optional: Use another port

If port 8501 is already in use:

```bash
streamlit run app.py --server.port 8502
```

The actual startup behavior depends on the installed dependencies, application configuration, and whether the required assets are available.

---

## 🧪 Running Tests

MIRAGE includes tests intended to validate important parts of the evaluation pipeline.

Run the test suite with:

```bash
source .venv/bin/activate
pytest tests/ -v
```

The intended coverage includes:

- IoU calculations.
- Ground-truth matching rules.
- Perturbation geometry.
- Deterministic transformation behavior.
- JSON experiment manifests.
- Selected integration behavior.

A test suite's existence does not establish that all tests pass. Run the commands in your local environment and review any failures before describing the implementation as fully validated.

---

## 🗂️ Experiment Reports and Reproducibility

MIRAGE stores experiment reports as JSON files in the `reports/` directory.

Depending on the implementation, a report can record:

- Model identifier and version.
- Dataset source and selected image identifiers.
- Software versions and execution device.
- Perturbation type, severity, and random seed.
- Confidence and IoU thresholds.
- Baseline and perturbed evaluation metrics.
- Per-image evaluation outcomes.
- Detection regressions and relevant confidence changes.

JSON makes the experiment configuration easy to inspect, compare, version, and exchange.

### Why Reproducibility Matters

A useful robustness experiment should be more than a screenshot of a model failing.

By recording the conditions associated with an outcome, MIRAGE aims to help users repeat the experiment, compare alternative models, and investigate whether a particular change consistently affects detection behavior.

Exact replay requires the corresponding assets and compatible software behavior. Deterministic perturbations alone do not guarantee identical inference across every runtime environment.

---

## 🏭 Real-World Applications

MIRAGE is intended to support investigation and testing across multiple computer-vision domains.

These are potential applications, not claims of existing commercial deployment.

### 1. Industrial Quality Inspection

Factory cameras may encounter shadows, reflections, changing illumination, and variations in product appearance.

MIRAGE could help engineers investigate whether a suitable object detector continues to identify relevant objects under selected visual conditions.

For defect detection specifically, the model and dataset must be appropriate for the defects being investigated.

### 2. Robotics and Warehouse Automation

Robots and automated handling systems depend on visual perception to identify packages, tools, containers, and obstacles.

MIRAGE could help developers explore how selected blur, occlusion, and exposure conditions affect object detection before broader system testing.

It does not replace physical robot safety validation.

### 3. Automotive Vision Research

Automotive perception systems may encounter changes in visibility, illumination, and image quality.

MIRAGE could serve as a preliminary image-level evaluation tool for researchers investigating detection behavior under selected perturbations.

It is not an automotive safety certification or validation system.

### 4. Agriculture and Field Robotics

Outdoor cameras experience variable sunlight, shadows, and changing visibility.

Researchers could use controlled perturbations to investigate the robustness of crop, fruit, or equipment detectors before evaluating them on representative field data.

### 5. AI Research and Model Development

Researchers can use the same evaluation workflow to compare supported object detectors under consistent conditions.

This can support experiments investigating model behavior, benchmark performance, and potential weaknesses.

### 6. Computer-Vision Education

Students can observe how predictions change when image conditions change, then compare their observations with quantitative evaluation metrics.

This makes robustness evaluation more accessible as a practical learning exercise.

---

## 💼 Potential Business Model

MIRAGE could evolve from a local developer workbench into a more comprehensive model-evaluation tool if user research demonstrates sufficient demand.

Potential customer segments include:

- AI startups building computer-vision products.
- Machine-learning engineers evaluating deployed models.
- Industrial machine-vision integrators.
- Robotics and automation developers.
- Research laboratories and universities.
- Teams that need repeatable testing before model deployment.

### Potential Product Tiers

| Tier | Possible capabilities |
|---|---|
| Community | Local experiments and basic comparisons |
| Developer | Batch evaluation, experiment history, and exportable reports |
| Team | Shared experiments, collaborative workflows, and integrations |
| Enterprise | Private deployment, tailored evaluation pipelines, and support |

These tiers represent possible future directions. They are not current subscription offerings or validated commercial commitments.

### Potential Value Proposition

The commercial opportunity is not simply applying image filters to pictures. It is helping engineering teams investigate model weaknesses more consistently and efficiently.

A sustainable business would need to demonstrate measurable customer value, such as reduced manual testing effort, improved test coverage, easier failure reproduction, or more efficient evaluation workflows.

Customer demand, willingness to pay, operating costs, and competitive positioning must be validated before making claims about profitability.

---

## 🛣️ Current Scope and Future Roadmap

MIRAGE prioritizes a focused, measurable evaluation workflow rather than attempting to solve every aspect of AI safety.

### Current Core Scope

The intended core includes:

- Real pretrained object detection.
- Labelled benchmark evaluation.
- Controlled image perturbations.
- Baseline-versus-perturbed inference.
- Ground-truth-based metrics.
- Detection regression analysis.
- JSON experiment reports and replay.

The status of each capability should be verified against the actual repository and test results.

### Potential Future Improvements

**Phase 1 — Evaluation reliability**

- Expand labelled evaluation datasets.
- Validate metric calculations against reference implementations.
- Add tests for edge cases in matching and regression analysis.
- Improve handling of empty predictions and empty ground-truth cases.

**Phase 2 — Comparative experimentation**

- Evaluate multiple models under identical conditions.
- Run batch experiments across perturbation strengths.
- Introduce severity-response curves.
- Add confidence-threshold sensitivity analysis.

**Phase 3 — Deeper diagnostic analysis**

- Group recurring failure patterns.
- Investigate interactions between multiple perturbations.
- Add uncertainty estimates and more detailed statistical reporting.
- Support additional benchmark protocols where appropriate.

**Phase 4 — Workflow integration**

- Export results in machine-readable formats.
- Integrate with model-development and evaluation pipelines.
- Support experiment history and collaborative analysis.
- Explore private deployment for teams with data restrictions.

These are roadmap possibilities, not promises that all features will be implemented.

---

## 🛡️ Responsible Use

MIRAGE is intended to support evaluation and investigation of computer-vision systems.

Results should be interpreted within the scope of the images, labels, models, and conditions tested.

A model that performs well under a limited set of perturbations is not necessarily reliable in every deployment environment. Conversely, a performance decrease under a synthetic transformation does not automatically establish that the model will fail in a particular real-world situation.

For safety-sensitive applications, MIRAGE should be used as one component of a broader validation process involving representative data, domain expertise, system-level testing, and appropriate safety procedures.

---

## 🤝 Contributing

Contributions that improve evaluation quality, reproducibility, usability, and transparency are especially valuable.

Potential areas include:

- Additional controlled perturbations.
- Improved matching and metric validation.
- New labelled benchmark integrations.
- Reproducibility improvements.
- Performance optimization.
- Better visualizations and experiment reports.
- Documentation and test coverage.

Before contributing a new metric or perturbation, document its definition, parameters, intended interpretation, and limitations.

Contributions should preserve the distinction between measured results and unverified explanations.

---

## 📄 License and Attribution

MIRAGE uses third-party software, pretrained models, and potentially public benchmark datasets. Each component has its own licensing and attribution requirements.

- **Software dependencies:** Review their respective licences.
- **Pretrained models:** Verify the applicable model and framework terms before redistribution or commercial use.
- **Datasets and annotations:** Preserve required attribution and comply with the relevant dataset licence.
- **MIRAGE source code:** Apply the licence explicitly selected for this repository.

COCO-related assets and Ultralytics model assets should be reviewed individually. Do not assume that the licence of a dataset automatically covers the model weights, dependencies, or the entire application.

---

## 🌐 Project Vision

MIRAGE begins with a focused question about object detection, but its broader motivation is applicable across computer-vision development:

**How can we understand the conditions under which an AI vision system becomes less reliable, and how can we make that evidence reproducible?**

By combining controlled experimentation, ground-truth-based measurement, visual comparison, and transparent reporting, MIRAGE aims to help developers move beyond isolated predictions toward a more systematic understanding of model behavior.

**MIRAGE — See the failure. Measure the change. Investigate the evidence.**
