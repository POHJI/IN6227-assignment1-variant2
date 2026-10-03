---
name: tabular-classification-report
description: Run an end-to-end tabular classification study from a local dataset, generating the needed analysis code and an IN6227 Variant 2 PDF report. Use for supervised classification, not regression or clustering.
---

# Tabular classification report

This file is the complete portable skill. A user may give an agent **only this `SKILL.md`** and local dataset paths. Do not assume that any Python script, configuration file, font file, earlier output, or example dataset accompanies it. Create the code needed for the current dataset in a local working directory, execute it, inspect its outputs, and repair it until the workflow succeeds. Keep data and outputs local unless the user explicitly authorizes sharing.

## 1. Confirm the run inputs

Ask together for any missing information and confirm information already supplied in the conversation:

- Local labeled training file path; optional test file path; target column; what one row represents; whether test labels exist. Accept a local CSV or TSV. If the target or sample unit is ambiguous, inspect the schema and ask rather than guessing.
- Full name, matric number, and GitHub repository URL for the report. Do not infer these from a username, filename, previous run, or example. If a repository is pending, mark the PDF as a draft and use a visible placeholder.
- Actual LLM model name/version and interface used for this run. Read reliable session metadata if available and show it to the user for confirmation; otherwise ask the user to verify it in the interface. Never substitute a guessed model name.
- Any constraints on dependencies, output language, or data handling. The default is local processing only.

Do not place personal details in this shareable skill. Write confirmed run-specific values into the local working directory. A change of dataset or user requires fresh confirmation.

## 2. Inspect before choosing models

Read **training data only** for initial decisions. Record row/column counts, class counts, missing targets and features, inferred and semantic feature types, category cardinalities, duplicate rows, numeric ranges and possible outliers, and any likely identifiers, target proxies, or leakage. Check whether rows share an entity, have time order, or may overlap across files. Missing labels are excluded from fitting and scoring; retain their rows only for prediction where appropriate. An IQR flag alone is not proof that a value is erroneous.

Choose feature exclusions and conversions with a recorded reason. Do not remove a field merely because its name resembles an ID, and do not treat numeric codes as continuous without considering their meaning. If semantic validity or the split design depends on domain knowledge, ask the user and state the limitation until it is resolved. Verify test columns against the chosen training features without using test labels to design preprocessing.

## 3. Generate and execute the analysis code

Write a **new, readable Python program for this run** in the local working directory. Generate any needed dependency list or local configuration there as part of the run; neither is an input to this skill. Use available installed packages, or set up a local environment and install suitable versions if needed. The program must accept the confirmed file paths, target, output directory, and decisions as parameters or a run-specific config rather than embedding one person's dataset or identity in reusable logic. Save the generated program so the run can be reproduced.

Implement and check these invariants:

1. Preserve source row order or an explicit row ID in predictions. Reject duplicate column names, missing required features, a target with fewer than two observed classes, and incompatible train/test schemas. Explain unsupported file encodings or feature types rather than silently coercing them.
2. Use a split appropriate to the sample unit: stratified random splitting only when rows are independent; group or time-aware splitting when needed. If class counts are too small for a reliable split, use a defensible alternative and disclose the limits. Set and record a random seed where randomness is used.
3. Keep final evaluation labels outside model and preprocessing selection. With a labeled external test set, choose models and settings using training-only validation, refit on training, then score the external labels once. With an unlabeled external test set, reserve an internal labeled holdout before tuning, score that holdout, refit the selected settings on all labeled training rows, then predict every external row. With no external test, use the internal holdout for final evaluation. Never claim metrics on unlabeled rows.
4. Put imputation, encoding, optional scaling, and classification inside fitted pipelines. Fit learned preprocessing separately within each training fold or tuning split. Handle unseen categories and nonfinite numeric values. Select a positive class for binary precision/recall/F1; use macro metrics for multiclass. Do not choose thresholds or features from final evaluation labels.
5. Compare **two justified classifiers**. Select them after profiling, based on data size, feature types, class balance, sparsity, and plausible relationships. A regularized linear classifier can be a baseline; a tree ensemble can capture interactions; distance-based methods require meaningful distances and manageable size. These are choices, not mandatory models. Define a small, feasible tuning space and state fitting/stopping criteria. Select settings and the preferred model by a stated validation metric, normally macro F1 when classes are imbalanced. Break ties by a documented rule. Do not infer that a small single-split difference proves general superiority.
6. Save a data profile, run record, predictions for both models, and evaluation metrics in machine-readable local files. Record class order, data counts, split method and sizes, features/exclusions, preprocessing, candidate and selected parameters, selection metric, confusion matrices, software versions, seed, and output paths. Include a majority-class reference where meaningful. For binary classification, report positive-class precision, recall, and F1; also report accuracy, macro F1, and balanced accuracy.

Run the generated code. If it fails, fix the code or documented decisions and rerun. Check row counts, prediction counts, class order, confusion-matrix totals, metric calculations, and any split leakage before writing conclusions. State a real limitation if the available data cannot support a credible evaluation; do not fabricate a successful run.

## 4. Generate and inspect the PDF

Generate the report from the saved run record, not manually transcribed numbers. The report body is at most **two pages** and covers data exploration/cleaning, feature decisions, training and stopping criteria, evaluation/comparison, and discussion. Explain the reasons for choices and relevant uncertainty. Include the full name, matric number, `IN6227-Assignment-1`, `Variant-2`, confirmed LLM model/version and interface, and repository URL. Use a visible draft placeholder only for details still pending.

Use Times New Roman: 24 pt bold title, 11 pt author/metadata, and 10 pt headings/body. Use A4 paper, approximately 0.75-inch left/right margins, a two-column body, and matching header/footer. A wide metrics table may span both columns for legibility. Do not shrink the specified font sizes to force a page count. If Times New Roman is unavailable, ask for a local font source or disclose the substitution; do not silently claim compliance. Render every PDF page and inspect for clipping, overlap, incorrect breaks, legibility, and page count. Correct layout problems and regenerate.

Add a separate **Reflection** page after the two-page body; it is outside the two-page limit and uses the same visual style. Reflection must cover actual human oversight, a challenged skill decision, and a concrete manual trustworthiness check. Ask the user for their real actions and judgment, or draft only from actions evidenced in the current run and have the user verify them. Never invent oversight or verification. Keep the Reflection text separate until confirmed, then combine it with the report into one submission PDF.

## 5. Hand off the local outputs

Give the user the final PDF and the generated code, plus the profile, run record, and predictions needed to inspect or reproduce it. State where each local file is saved and what, if anything, remains for the user to confirm. Do not upload to GitHub, NTULearn, or any other service without the user's explicit authorization. If this single-file skill is shared, the run-specific code and outputs are created anew by the receiving agent; they are **not dependencies bundled with the skill**.
