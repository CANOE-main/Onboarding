# CANOE / TEMOA — Onboarding Activities

This repo (or folder) contains the onboarding workflow and activities for getting productive with **CANOE/TEMOA**, the **CANOE app + dataset**, and the **representative periods** workflow.

## What you’ll do
1. Download the dataset + application and clone the representative periods repo.
2. Generate a starter model for New Brunswick + Nova Scotia using the app (Current Measures, low resolution).
3. Run representative periods (4 representative periods) to prepare outputs for follow-on activities.
4. Complete a set of modeling activities:
   - Add industry (IND) to the model
   - Add technology groups + renewable policy constraints
   - Add Direct Air Capture (DAC) + emissions limits
   - Validate/fix efficiency assignment logic (OutputFlowIn)

---

## Prerequisites

### 1) CANOE/TEMOA repo
You must have the CANOE/TEMOA codebase available and checked out to the correct branch:

- Repo: `CANOE-main/temoa`
- Branch: `1.0.0-dev-operator`

Example:
```bash
git clone <CANOE_TEMOA_REPO_URL>
cd temoa
git checkout 1.0.0-dev-operator
```

### 2) Dataset + Application
The dataset + application are stored on OneDrive and require access.

Download the correct application build for your OS (Windows vs macOS).

> Tip: For a public GitHub README, avoid including personal email addresses. Instead, use a GitHub Issue/Discussion or an internal onboarding doc for access requests.

### 3) Representative periods code
Representative periods repo: `CANOE-main/representative_periods`

```bash
git clone <REPRESENTATIVE_PERIODS_REPO_URL>
```

---

## Setup workflow

### Step A — Generate a starter model using the CANOE app
1. Open the application.
2. Ensure **Scenario = “Current Measures”**.
3. Select **Low resolution** for:
   - New Brunswick
   - Nova Scotia
4. Export / name the database something meaningful (example: `CANOE_ONB.sqlite`).

✅ Output: a SQLite model database (e.g., `CANOE_ONB.sqlite`)

### Step B — Run representative periods (4 representative periods)
1. Copy/move your generated model database into the `representative_periods` workflow directory (or point the workflow to it).
2. Configure the workflow to use **4 representative periods**.
3. Run the representative periods code and generate outputs for the next activities.

✅ Output: representative-period outputs for downstream activities

---

## Activities

## 1) Add industry (IND) into the model
**Goal:** Implement an annual industry technology so that both New Brunswick and Nova Scotia can meet industrial demand.

### Tasks
- Integrate industry into **Nova Scotia** and **New Brunswick**.
- Ensure the implementation follows the breakdown shown in the onboarding schematics/figure.
- Validate everything is correct (and the model runs).

### Hints
- Assume **efficiency = 1** (simplifies accounting).
- `Limittechinputsplitannual` is a **fraction** (10% = `0.1`) and the sum cannot exceed 1.
- For similar technologies, tags like `unlim_cap` and `annual` should be set to `1`.

---

## 2) Add technology groups and renewable policy constraints
**Goal:** Create a technology group for renewables and enforce a renewable share constraint for each period in NB + NS.

### Tasks
- Create an appropriate technology group for renewable electricity.
- Create constraints for the renewable electricity scenario.
- Confirm the model runs successfully afterward.

### Hints
- Use the `RPSrequirement` table.
- Choose appropriate renewable technologies (example used previously: hydro, wind, solar).
- `RPSrequirement` is a **percentage/fraction** value.

---

## 3) Add Direct Air Capture (DAC) + emissions limits
**Scenario:** Both NB and NS set limits on emission activity: **5,000 kTonnes** for each province by **2050**, achievable via DAC.

### Tasks
- Create the emissions constraint for the target.
- Implement the DAC technology with correct information.
- Test feasibility and confirm the model runs.

### Hints
- Emissions constraint goes into `LimitEmission`.
- Create a **waste commodity** for captured CO₂.
- If infeasible, check `EmissionActivity` sign: DAC should be **negative** emissions.

---

## 4) Complete efficiency assignment (OutputFlowIn validation)
**Goal:** Validate/fix how mass/energy balances are translated into the `OutputFlowIn` table across test systems of varying complexity.

### Tasks
- Review the balance spreadsheet (expected input→output relationships and values).
- Run the provided test systems that generate DB outputs.
- Compare `OutputFlowIn` to the spreadsheet case-by-case.
- Diagnose issues (missing rows, wrong pairings, double-counting, scaling errors).
- Update the implementation, then re-run all cases to confirm nothing regresses.

### Hints
- Start with the simplest 1-input/1-output case, then move to multi-input/multi-output cases.
- Note: “for import and FINAL should be 1”.

---

## Quick SQL checks (optional)
If you want to sanity-check tables directly:

```sql
-- Confirm renewables policy rows
SELECT * FROM RPSrequirement;

-- Confirm emissions constraint rows
SELECT * FROM LimitEmission;

-- Inspect OutputFlowIn results
SELECT * FROM OutputFlowIn LIMIT 200;
```

---

## Troubleshooting
- **Model infeasible after policy/emissions changes**
  - Re-check that policy constraints are fractions (e.g., 40% = 0.4), not “40”.
  - For DAC, ensure emissions are negative and the captured-CO₂ waste commodity exists.
- **Industry doesn’t meet demand**
  - Ensure the IND technology is present for both regions and all required tables are filled consistently.
  - Re-check `Limittechinputsplitannual` totals (must not exceed 1).

---

