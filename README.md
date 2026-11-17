<div align="center">

# DermaMNIST-Extension

Cartographic Generalization Exposes Non-Uniform Structure Loss in Downsampled Dermoscopy Benchmarks

Shrinking skin-cancer photos to 28×28 pixels erases most of what dermatologists diagnose by, and it erases more for some skin tones than others.

Experiment is complete · Co-Authored Paper Accepted @ NeurIPS 2026 Workshop: AI Data Readiness for Scientific Discovery (AIDaR)

</div>

Audited DermaMNIST, a widely used medical AI dataset that shrinks skin-cancer photos from 224×224 to 28×28 pixels. Proved that 4 of the 5 patterns dermatologists diagnose by are destroyed at that size, and that the damage falls unevenly across skin tones. Cut that gap by 52%. Built an 18-layer image format that keeps the patterns, borrowing the rules mapmakers use to shrink maps without losing what matters.

## What gets lost

At 28×28, each pixel stands in for an 8×8 block of the original photo. Any pattern finer than about 16 original pixels can't survive, whatever resizing method is used.

| Pattern dermatologists look for | Survives at 28×28? |
|---|---|
| pigment network | no |
| dots and globules | no |
| streaks | no |
| vessels | no |
| blue-white veil | **yes** |

- **Most of the diagnostic detail is gone before any model is trained.** This is simple math on the image sizes, and it could have been printed with the original dataset.
- **The loss is uneven across skin tones.** The standard resize keeps only about a third of the pattern detail, and how much it keeps varies by skin tone (a gap of 0.094). Our format keeps about 80% and cuts that gap to 0.045, a 52% reduction.

## What the new format buys, and what it costs

Our 18-layer format stores the original color image plus separate layers that record where each pattern appears, along with a per-image "confidence map" of where the photo is clear enough to trust.

| | Standard 28×28 | Our 18-layer 28×28 | Full-size 224×224 |
|---|---|---|---|
| Overall accuracy (AUC) | 0.864 | 0.796 | **0.875** |
| Recall on rare cancer types | 0.505 | 0.512 | **0.537** |
| Fairness gap across skin tones (lower is better) | 0.269 | 0.209 | **0.185** |

- **Better on rare cases and fairness, worse on overall accuracy.** We report that trade-off up front.
- **The model actually uses the pattern layers.** Scrambling them drops accuracy sharply.
- **The layers point at the same spots experts mark.** We checked against dermatologist annotations from ISIC 2018.

## What didn't work

- A rule from mapmaking that predicts how many patterns to keep at each size did not hold up.
- The confidence map's ability to flag bad predictions flipped between two runs of the same code, so we don't claim it.
- On a second, larger run, the rare-case advantage shrank to noise, and a simple image-sharpening baseline came close to full-size accuracy.

**What held across both runs:** the lost-patterns audit, the skin-tone gap, and the evidence that the model uses the layers.

*Limitations: one dataset and two runs. Skin tone is estimated from the photos, and the test set is mostly lighter skin. Two of the five patterns have no expert annotations to check against.*

## What every shrunken medical dataset should include

1. **A description of how it was shrunk**, with tests showing what each step keeps
2. **A scale bar**, so users know which details can survive before training anything
3. **A per-image confidence map** of where the photo can be trusted
4. **A calibration report** showing whether the model's confidence matches how often it's right

## Stack

Python · CNNs (ResNet-18, compact CNN) · MedMNIST v2 / HAM10000 · ISIC 2018

## Citation

```bibtex
@inproceedings{huang2026cartographic,
  author    = {Christopher Yu-Hao Huang and Ryan Jaemin Ahn and Wenhao Lu},
  title     = {Cartographic Generalization Exposes Non-Uniform Structure Loss
               in Downsampled Dermoscopy Benchmarks},
  booktitle = {NeurIPS 2026 Workshop on AI Data Readiness for Scientific Discovery (AIDaR)},
  year      = {2026}
}
```

Data: DermaMNIST (MedMNIST v2), derived from HAM10000. Expert annotations from ISIC 2018 Task 2.
