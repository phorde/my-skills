---
name: clinical-safety-guard
description: Clinical safety guardrails, health data privacy (LGPD/CFM/HIPAA), and medical reasoning integrity for healthcare software. Enforces strict clinical assertion stratification, zero fabrication of vitals/doses, multi-tenant facility isolation, and deterministic safety scores.
---

# Clinical Safety Guard

Enforce medical rigor, clinical assertion boundaries, sensitive health data privacy (LGPD/CFM/HIPAA), and patient safety in all health-tech applications.

---

## 1. Clinical Assertion Stratification

When processing clinical notes, anamnesis, physical examinations, or generating documentation:

| Assertion Level | Definition | Rules & Boundaries |
| :--- | :--- | :--- |
| **`EXPLICIT_FINDING`** | Facts explicitly dictated or recorded by the clinician. | Preserve verbatim fidelity. Do not alter nuances (e.g., preserve "há 1 dia" without silently converting to "24 horas"). |
| **`EXPANDED_NORMAL`** | Normal findings authorized by a recognized broad medical expression (e.g., *"abdome inocente"* authorizes general abdominal normalcy). | May expand to standard normal descriptions, but **MUST** be visually grouped or flagged for rapid clinician sign-off. |
| **`INFERRED_DIFFERENTIAL`** | Diagnostic hypotheses, risks, and clinical reasoning suggested by AI. | **MUST** be rendered in a separate clinical discussion container, never mixed into the factual legal medical record. |
| **`FORBIDDEN_FABRICATION`** | Any fact not explicitly stated or authorized. | **NEVER** fabricate vital signs, medication dosages, allergy status, surgical history, or physical exam maneuvers not performed (e.g., *"dor em QSD"* never authorizes inventing *"Murphy negativo"*). |

---

## 2. Protected Health Data & Privacy (LGPD / CFM / HIPAA)

### Strict Zero-Leak Policy
1. **Logs and Telemetry**:
   - Never log patient identifiable information (Name, CPF, National ID, Phone, Address, Medical Record Number) in application logs, Sentry, or third-party observability platforms.
   - Mask patient identifiers before sending text to external LLMs: use synthetic IDs (`pt_anon_<hash>`) or local tokenization.
2. **URLs & Browser State**:
   - Never pass patient IDs or clinical queries in URL search parameters or unencrypted localStorage.
3. **Multi-Tenant Facility Scoping**:
   - All patient queries and records must be strictly scoped by `facility_id` (hospital/clinic) and `patient_id`.
   - Never allow patient episodes, homonyms, or records from one healthcare facility to leak into another.

---

## 3. High-Risk Red-Flag Gates

When designing clinical workflows or documentation engines for urgent and emergency care, verify and alert on mandatory critical red flags:

- **Acute Back Pain**: Alert if red flags are missing (cauda equina syndrome: saddle anesthesia, acute urinary retention, fecal incontinence, progressive bilateral motor deficits).
- **Chest Pain**: Trigger mandatory differential evaluation (Acute Coronary Syndrome, Aortic Dissection, Pulmonary Embolism, Tension Pneumothorax, Boerhaave Syndrome).
- **Thunderclap Headache**: Enforce evaluation for Subarachnoid Hemorrhage.
- **Acute Abdomen**: Enforce pregnancy test (beta-hCG) check for women of childbearing age to rule out ectopic pregnancy.

---

## 4. Deterministic Clinical Calculators & Decision Logic

1. **Pure Functions Only**:
   - Clinical scores (e.g., Glasgow Coma Scale, Wells DVT/PE, CURB-65, NEWS2, qSOFA, HEART score) and dose calculators must be implemented as deterministic, pure functions with zero side effects.
2. **Exhaustive Unit Testing**:
   - Every clinical calculation must have 100% test branch coverage against published guideline tables, including boundary edge cases (e.g., age thresholds, eGFR cutoffs, weight extremes).
3. **Fail-Safe Defaults**:
   - In case of incomplete or ambiguous clinical inputs, calculators must return explicit validation warnings rather than assuming normal default values.
