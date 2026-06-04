# OCR Evaluation Report — PaddleOCR on P&ID Diagrams

> **Evaluation date:** 2026-02-24  
> **Sample:** 12 P&ID images (stratified by text density)  
> **Total predicted zones:** 2550  
> **Total ground-truth zones:** 2663

---

## 1. Methodology

### 1.1 Pipeline

The PaddleOCR pipeline runs two sequential models on rasterized SVG P&ID diagrams:

1. **Detection** — PP-OCRv5_server_det identifies bounding boxes around text zones.
2. **Recognition** — en_PP-OCRv5_mobile_rec transcribes the text within each detected box.

The 12 evaluation images were selected by stratified sampling across four text-density categories (low, medium, high, high-error), ensuring representativeness of the full 270-image dataset.

### 1.2 Ground Truth Construction

Ground truth was produced by expert manual review in Label Studio. For each predicted box, the annotator either:

- **Accepted** the box (possibly correcting the text transcript),
- **Resized** the box to better fit the text zone,
- **Deleted** the box (spurious detection), or
- **Drew a new box** for a text zone missed by the OCR scanner.

Label Studio exports each result with an `origin` field:

| `origin` value | Meaning |
|----------------|---------|
| `prediction` | Box geometry unchanged; text may have been corrected silently |
| `prediction-changed` | Box was moved or resized by the annotator |
| `manual` | New box drawn from scratch (missed by OCR — FN) |

> ⚠️ **Critical note:** The `origin` field tracks **box geometry** changes only, not text corrections. A box with `origin='prediction'` may still have a corrected transcript (e.g. `Ø` corrected to `0`). Text accuracy is always evaluated by direct comparison of `pred_text` vs `gt_text`.

### 1.3 Matching Strategy

Label Studio preserves box IDs from the prediction file into the annotation file. Matching is **ID-based** — no IoU threshold is needed for TP assignment:

- **TP**: Prediction ID present in the GT annotation (accepted/corrected by annotator)
- **FP**: Prediction ID absent from GT (deleted by annotator as spurious)
- **FN**: GT box with `origin='manual'` (added by annotator; missed by OCR)

IoU is computed *post-hoc* on TP pairs to measure bounding-box boundary quality, in Label Studio's percentage coordinate space (to avoid pixel-scale mismatches between the predictions file and the GT export).

### 1.4 Spatial Bleeding: A Detection-Level Error

In densely annotated P&ID diagrams, text zones are often in close proximity. PaddleOCR sometimes merges two or more adjacent text zones into a single oversized box — a phenomenon we call **spatial bleeding**.

When spatial bleeding occurs:

1. The annotator **shrinks** the oversized box to cover only the primary label → the prediction ID survives as a TP, but with corrected box geometry (`origin='prediction-changed'`) and a truncated transcript.
2. The annotator **draws new manual boxes** for each of the subsumed neighbouring zones.

The resulting manual boxes (FN candidates) are **not true misses**: the scanner did detect the region — it simply over-extended the box boundaries. We therefore reclassify them as **FN_subsumed** (distinct from **FN_true**, where the zone was genuinely overlooked).

*Subsumed FN detection:* for each TP classified as `spatial_bleeding` by the recognition step, we test whether the center of each `manual` GT box falls inside the **original** (pre-shrink) prediction box. Any match is a FN_subsumed.

---

## 2. Text Zone Detection

### 2.1 Overall Results

| Metric | Value |
|--------|-------|
| Precision | **0.995** (2537 TP / 2550 predicted) |
| Recall | **0.953** (2537 TP / 2663 GT zones) |
| F1-Score | **0.973** |
| FP (spurious detections) | 13 |
| FN total | 126 (= 37 true misses + 89 subsumed) |
| Spatial bleeding events | 86 |

**Interpretation:** PaddleOCR achieves excellent precision (99.5%): only 13 predicted zones were false positives across 12 images. Recall is lower (95.3%) due to 126 missed zones, but the picture is nuanced:

- **89 FN_subsumed** — inside the oversized box of a spatial bleeding event; the scanner detected the region but merged it incorrectly.
- **37 FN_true** — genuinely missed zones with no covering prediction.

The 86 spatial bleeding events represent the primary detection failure mode and account for the majority of apparent FN.

### 2.2 Per-Image Results

| Image | Category | TP | FP | FN_true | FN_sub | SB | Precision | Recall | F1 | IoU (med) |
|-------|----------|----|----|---------|--------|----|-----------|--------|----|-----------|
| 102 | medium | 89 | 0 | 0 | 3 | 3 | 1.000 | 0.967 | 0.983 | 1.000 |
| 132 | high | 250 | 0 | 0 | 4 | 4 | 1.000 | 0.984 | 0.992 | 1.000 |
| 168 | high | 185 | 0 | 1 | 2 | 2 | 1.000 | 0.984 | 0.992 | 1.000 |
| 198 | high | 352 | 0 | 0 | 22 | 19 | 1.000 | 0.941 | 0.970 | 1.000 |
| 21 | medium | 50 | 0 | 0 | 2 | 2 | 1.000 | 0.962 | 0.980 | 1.000 |
| 242 | high_error | 416 | 4 | 1 | 11 | 11 | 0.990 | 0.972 | 0.981 | 1.000 |
| 261 | low | 30 | 0 | 0 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 |
| 272 | high_error | 385 | 2 | 21 | 1 | 2 | 0.995 | 0.946 | 0.970 | 1.000 |
| 51 | high | 541 | 3 | 14 | 37 | 36 | 0.994 | 0.914 | 0.952 | 1.000 |
| 79 | low | 47 | 1 | 0 | 3 | 3 | 0.979 | 0.940 | 0.959 | 1.000 |
| 84 | medium | 75 | 3 | 0 | 0 | 0 | 0.962 | 1.000 | 0.980 | 1.000 |
| 94 | medium | 117 | 0 | 0 | 4 | 4 | 1.000 | 0.967 | 0.983 | 1.000 |

*SB = spatial bleeding TP events; FN_sub = FN subsumed by SB boxes.*

### 2.3 Results by Density Category

| Category | TP | FP | FN_true | FN_sub | SB events | Precision | Recall | F1 |
|----------|----|----|---------|--------|-----------|-----------|--------|----|
| low | 77 | 1 | 0 | 3 | 3 | 0.987 | 0.963 | 0.975 |
| medium | 331 | 3 | 0 | 9 | 9 | 0.991 | 0.974 | 0.982 |
| high | 1328 | 3 | 15 | 65 | 61 | 0.998 | 0.943 | 0.970 |
| high_error | 801 | 6 | 22 | 12 | 13 | 0.993 | 0.959 | 0.976 |

---

## 3. Bounding Box Quality (IoU on TP Pairs)

For each TP pair, IoU is computed between the **original** predicted box and the **corrected** GT box. IoU = 1.0 indicates the annotator accepted the box boundaries exactly.

| Statistic | Value |
|-----------|-------|
| Total TP pairs | 2537 |
| Mean IoU | 0.983 |
| Median IoU | 1.000 |
| Min IoU | 0.222 |
| IoU = 1.00 (unchanged) | 2413 (95.1%) |

IoU distribution:

| Range | Count | Share |
|-------|-------|-------|
| <0.50 | 30 | 1.2% |
| 0.50-0.75 | 60 | 2.4% |
| 0.75-0.90 | 25 | 1.0% |
| 0.90-<1.00 | 9 | 0.4% |
| =1.00 | 2413 | 95.1% |

**Interpretation:** 95.1% of predicted boxes were accepted without any geometric correction. Cases with IoU < 1.0 include the 86 spatial bleeding events (IoU typically 0.22–0.84) and a small number of minor boundary adjustments.

---

## 4. Text Recognition Quality

Recognition metrics use:

- **CER** (Character Error Rate): Levenshtein distance / number of GT characters. Primary metric for P&ID labels (short alphanumeric codes). A single wrong character gives WER = 1.0 for single-token labels, making WER less informative.
- **WER** (Word Error Rate): Levenshtein distance at word level.
- **Accuracy**: fraction of TP zones with CER = 0 (exact character match).

### 4.1 All TP Pairs

| Metric | Value |
|--------|-------|
| TP pairs evaluated | 2537 |
| Exact matches (CER=0) | 2122 (83.6%) |
| CER (aggregate) | 0.0544 |
| WER (aggregate) | 0.1611 |

### 4.2 Clean TP Pairs (spatial bleeding excluded)

The 86 spatial bleeding TP pairs are excluded here because their recognition errors are a direct consequence of the detection failure (merged zones). Excluding them gives a cleaner picture of the recognition model's standalone character transcription performance.

| Metric | Value |
|--------|-------|
| Clean TP pairs | 2451 (= 2537 total − 86 SB pairs) |
| Exact matches (CER=0) | 2122 (86.6%) |
| CER (aggregate) | 0.0301 |
| WER (aggregate) | 0.1345 |

### 4.3 Results by Density Category (clean TP)

| Category | Clean TP | Correct | Accuracy | CER | WER |
|----------|----------|---------|----------|-----|-----|
| low | 74 | 68 | 91.9% | 0.0085 | 0.0645 |
| medium | 322 | 290 | 90.1% | 0.0153 | 0.1080 |
| high | 1267 | 1071 | 84.5% | 0.0332 | 0.1546 |
| high_error | 788 | 693 | 87.9% | 0.0351 | 0.1218 |

---

## 5. P&ID-Specific Error Taxonomy

Of 2537 TP zones, **415** (16.4%) required at least one correction. Errors are classified into five mutually exclusive categories (checked in priority order):

| Error type | Count | % of errors | % of all TP | Notes |
|------------|-------|-------------|-------------|-------|
| `slash_o_confusion` | 247 | 59.5% | 9.7% | Ø (slashed O) read as `0` — dominant P&ID error |
| `spatial_bleeding` | 86 | 20.7% | 3.4% | Detection error: OCR merged adjacent zones *(see §2)* |
| `duplication` | 15 | 3.6% | 0.6% | Same label repeated ≥ 2 times in one box |
| `spurious_chars` | 8 | 1.9% | 0.3% | ≤ 2 stray punctuation chars at box boundary |
| `other` | 59 | 14.2% | 2.3% | Character substitution / insertion / deletion |
| correct (no error) | 2122 | — | 83.6% | — |

### 5.1 Slash-O Confusion (Ø ↔ 0)

In P&ID nomenclature, **Ø** (Unicode U+00D8) denotes a pipe diameter and is visually similar to the digit **0**. PaddleOCR systematically misreads Ø as 0, accounting for 247 corrections (59.5% of all errors, 9.7% of all TP zones).

This error is **systematic and domain-specific**: it does not reflect a general recognition weakness, but a vocabulary gap. The model was likely trained on general-purpose corpora where Ø is rare. A simple post-processing rule (dictionary-based `0` → `Ø` substitution in P&ID label contexts) could eliminate this class of error entirely.

Estimated clean accuracy after Ø/0 fix: **96.7%** (+10.1% from current 86.6%)

### 5.2 Spatial Bleeding (Detection Error)

86 TP pairs are classified as `spatial_bleeding`: the predicted transcript contains text from an adjacent zone that was merged into the OCR box. These pairs are **excluded from clean recognition metrics** (Section 4.2) because the error originates in the detection stage.

Each spatial bleeding event typically generates:
- 1 TP with corrected (shortened) box and truncated transcript
- 1–3 FN_subsumed (subsumed neighbouring zones, now manual GT boxes)

The 86 SB events generated a total of **89 subsumed FN**.

### 5.3 Other Errors

The remaining errors are minor:

- **Duplication** (15 cases): OCR reads the same label multiple times within one box, likely from symmetric layout or bounding box overlap.
- **Spurious characters** (8 cases): 1–2 stray punctuation characters at the boundary of the text zone.
- **Other** (59 cases): miscellaneous character-level substitutions (spacing, backslash vs. none, partial label).

### 5.4 Error Examples

**`slash_o_confusion`** (247 total, showing first 4):

| Prediction | Ground Truth |
|------------|--------------|
| `0200` | `Ø200` |
| `025` | `Ø25` |
| `050` | `Ø50` |
| `0100` | `Ø100` |

**`spatial_bleeding`** (86 total, showing first 4):

| Prediction | Ground Truth |
|------------|--------------|
| `10LBG10 TI` | `10LBG10` |
| `10LBG10 TIS` | `10LBG10` |
| `10LBG10 \PI` | `10LBG10` |
| `10LCW10 \PI` | `10LCW10` |

**`duplication`** (15 total, showing first 4):

| Prediction | Ground Truth |
|------------|--------------|
| `10QKC3010QKC30` | `10QKC30` |
| `10QKC3010QKC30` | `10QKC30` |
| `10SBS31,10SBS31` | `10SBS31` |
| `10SBS31,10SBS31` | `10SBS31` |

**`spurious_chars`** (8 total, showing first 4):

| Prediction | Ground Truth |
|------------|--------------|
| `-+14,00m` | `+14,00m` |
| `-+14,00m` | `+14,00m` |
| `10QKC30:` | `10QKC30` |
| `!10QKC30` | `10QKC30` |

**`other`** (59 total, showing first 4):

| Prediction | Ground Truth |
|------------|--------------|
| `10LBG /010 .B:1` | `10LBG / 010 .B:1` |
| `10LCM /010 .B:8` | `10LCM / 010 .B:8` |
| `10LBG /030 .E:10` | `10LBG / 030 .E:10` |
| `10LCA /050 .E:9` | `10LCA / 050 .E:9` |

---

## 6. Confidence Calibration

Does PaddleOCR's confidence score reliably predict recognition accuracy? The table below shows accuracy (CER = 0) and mean CER per confidence bucket, computed on **clean TP pairs** (spatial bleeding excluded).

| Confidence range | N | Accuracy (CER=0) | Mean CER |
|-----------------|---|-----------------|----------|
| 0.85-0.90 | 43 | 25.6% | 0.2193 |
| 0.90-0.95 | 111 | 59.5% | 0.1003 |
| 0.95-0.98 | 157 | 56.7% | 0.1231 |
| 0.98-1.00 | 2114 | 91.5% | 0.0293 |

**Interpretation:** PaddleOCR's confidence scores show meaningful calibration. Predictions with score ≥ 0.98 achieve substantially higher accuracy than those below 0.95. A confidence threshold of ~0.95 can be used to flag uncertain predictions for human review, while high-confidence predictions can be trusted for automated pipeline use.

---

## 7. Summary and Conclusions

### Key Findings

| Dimension | Result |
|-----------|--------|
| Detection Precision | **99.5%** — very few spurious boxes |
| Detection Recall | **95.3%** — some missed zones |
| FN_true (genuine misses) | **37** of 2663 GT zones |
| Spatial bleeding events | **86** merged-zone detections |
| Recognition accuracy (clean) | **86.6%** exact match |
| CER (clean) | **0.0301** |
| Dominant error | **slash_o_confusion** (247 cases, 60% of errors) |

### Implications for the Full Dataset

These results were obtained on a 12-image stratified sample. Key projections for the full 270-image dataset:

1. **Detection is reliable** (99.5% precision). The 13 FP on 12 images indicates a very low spurious-detection rate across the full corpus.

2. **Spatial bleeding is the primary detection failure mode.** The 86 events on 12 images scale to an estimated ~1935 events on the full dataset, concentrated in high-density images.

3. **The Ø/0 confusion is fully correctable.** A post-processing rule (context-aware `0` → `Ø` substitution) could improve clean-TP accuracy from 86.6% to an estimated **96.7%** (+10.1%).

4. **Confidence thresholding is meaningful.** Predictions below confidence 0.95 should be flagged for human review; above 0.98, the text can be trusted for automated pipeline use.

---

*Report generated automatically by `text_recognition/evaluate_ocr.py`.*