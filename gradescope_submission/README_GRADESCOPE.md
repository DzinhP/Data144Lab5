# UC Berkeley DMA (Data 144) Lab 5 — Kaggle Titanic Competition

## Submission Package & Reproduction Guide

### 1. Files Included in This Package
* `titanic_submission.ipynb`: Clean, fully executed, reproducible Jupyter Notebook using only DMA Labs 1–4 techniques (no advanced ensembles or black-box pipelines).
* `train.csv`: Training dataset (891 rows).
* `test.csv`: Competition test dataset (418 rows).
* `submission.csv`: Final Lab 5b submission file (Model A: Ticket-group overrides, 418 rows, binary predictions, achieves 0.8530 5-fold cross-validation accuracy and 0.80239 on the Kaggle public leaderboard).
* `submission_combo_8024_backup.csv`: Historical benchmark backup verifying the exact 0.80239 predictions.
* `submission_boosting.csv`: Historical Lab 5a prediction file (initial boosting model checkpoint, scored 0.72455 on Kaggle).
* `submission_rule.csv`: Historical section 6 rule file (matched the 0.74850 baseline on Kaggle).
* `kaggle_screenshot_placeholder.txt`: Instructions for attaching your team Kaggle rank & accuracy screenshot.

---

### 2. How to Add Your Kaggle Screenshot and Rebuild the ZIP

1. Save your leaderboard screenshot (e.g., `kaggle_screenshot.png`) into the `gradescope_submission/` folder.
2. In your terminal, run this single command from your project directory to rebuild the Gradescope ZIP:

```bash
zip -j Data144Lab5_Gradescope_Submission.zip gradescope_submission/*
```

(The `-j` flag packages all files directly at the root of the ZIP so Gradescope can inspect them immediately).

---

### 3. Summary of Methodology & Iterations

1. **Baseline**: Majority-class gender rule achieves **0.7868** on local training data and **0.74850** on the Kaggle public leaderboard.
2. **Early Exploratory Models**:
   * Initial experiments explored tree ensembles (HistGradientBoosting, Random Forest).
   * Although cross-validation on the training set appeared competitive, the boosting model scored **0.72455** on the Kaggle public leaderboard, falling below the gender baseline.
   * *Diagnosis*: Overfitting on noisy borderline passengers (predicting 1st-class cabin adult men alive and solo 3rd-class women dead).
3. **Conservative Conditional Overrides**:
   * We anchored strictly on the gender rule, changing predictions only where empirical survival was deterministic:
     - 3rd-class women in families >= 5 predicted dead (only 3 of 27 survived, 11%).
     - Boys (`Master`) in families <= 4 predicted alive (all 22 survived, 100%).
     - Unmarried 3rd-class women from Southampton predicted dead (32.8% survival).
4. **Primary Model (Model A — Ticket-Group Overrides)**:
   * Evacuation on the Titanic was coordinated by traveling groups.
   * For female passengers and boys (`Master`), we check if others on the same `Ticket` in the labeled training data all survived (`rate == 1`) or all perished (`rate == 0`).
   * *Validation*: Evaluated using 5-fold Stratified Cross-Validation (`random_state=42`).
   * *Results*:
     - Mean 5-Fold Accuracy: **0.8530** (760/891 correct).
     - Beats gender baseline on all 5 folds.
     - Kaggle Public Leaderboard Score: **0.80239**.

---

### 4. Honest Disclosures on Validation & Competition Timing
* **Model Selection & Heuristic Thresholds**: Within the cross-validation loop, ticket-group survival rates are computed strictly from the training fold without reading validation fold labels (verified via label-inversion testing). However, the structural heuristic thresholds themselves (such as the family size cutoff of 5 and the Southampton Miss group) were initially identified through exploratory data analysis on the full training set.
* **Leaderboard Standing**: The public leaderboard score of 0.80239 is competitive and well above the baseline. However, placement in the top 70% of the class depends on the distribution of all student submissions and the final private 60% leaderboard evaluation.
* **Lab 5a Submission Timing**: `submission_boosting.csv` corresponds to the model committed on October 5, 2026 prior to the October 7 checkpoint. However, local Git commits record only local commit timestamps; exact submission timing and score must be verified from your Kaggle Submissions dashboard.
