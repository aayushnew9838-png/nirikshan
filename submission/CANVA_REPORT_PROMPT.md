# PROMPT: BUILD THE THERMOS SIH 2026 TECHNICAL REPORT (CANVA)

> Copy everything between the two `═══` rules into Canva (or your AI writing tool of choice).
> Grounded in the actual repository — every number below exists in a file, or is explicitly marked TBD.

```
═══════════════════════════════════════════════════════════════════
PROMPT: BUILD THE THERMOS SIH 2026 TECHNICAL REPORT (CANVA)
═══════════════════════════════════════════════════════════════════

You are acting as a senior remote-sensing/ML technical writer AND a
Canva art director. Produce a complete, submission-ready 10,000–11,000
word technical system report for Smart India Hackathon (SIH 2026),
designed natively in Canva.

The report must read as: TECHNICAL SYSTEM PAPER + IMPLEMENTATION
REPORT + DEPLOYMENT PROPOSAL. It must NOT read like a college
assignment or a slide deck expanded into prose.

───────────────────────────────────────────────────────────────────
A. NON-NEGOTIABLE PROJECT FACTS (use verbatim, never contradict)
───────────────────────────────────────────────────────────────────
Title:     THERMOS — Thermal Event Recognition and Monitoring
           Operational System
Subtitle:  An AI-Driven Geospatial Intelligence Framework for
           Detection, Classification and Risk Prioritization of
           Industrial Fires and Persistent Thermal Sources
PS ID:     SIH26162
PS Title:  AI-Based Detection and Classification of Industrial Fires
           and Persistent Thermal Sources Using NASA FIRMS, OSM &
           Satellite Data
Category:  Software | Theme: Disaster Management
Institution: Netaji Subhas University of Technology (NSUT), New Delhi
Team:      Team THERMOS (6 members)
Live demo: https://sih-2026-nu-ten.vercel.app/

Stack (as actually implemented):
- Frontend: React 19.2.8, Vite 8.2.2, TailwindCSS v4, MapLibre GL
  6.6.0, Three.js 0.185.1, Zustand 5, Recharts 3, Framer Motion 13
- Backend: Python 3.10, FastAPI 0.141, SQLAlchemy 2 + GeoAlchemy2,
  Alembic, PostGIS 3.4 on PostgreSQL 16, Redis 7, APScheduler
- ML: XGBoost 3.4.1 XGBClassifier, Scikit-learn, SHAP 0.52.0
  (TreeExplainer), Joblib
- AI: Google Gemini (gemini-flash-lite-latest) + deterministic
  rule-engine fallback; separate RAG copilot with offline failsafe
- Deployment: Vercel (frontend), Render (backend), Docker Compose
  (PostGIS + backend + Redis; MongoDB + API + nginx)

Model (from ml/models/model_metadata.json — quote exactly):
  n_estimators=200, max_depth=6, learning_rate=0.1, subsample=0.8,
  colsample_bytree=0.8, random_state=42
  train_samples=28800, test_samples=7200,
  test_accuracy=0.9940277777777777

Dataset: THERMOS_ML_Core_15_Columns.csv — 36,000 rows × 15 columns
(14 features + label), 0 missing, 0 duplicates, exactly 6,000 rows
(16.67%) per class.

Six-class taxonomy:
1. Industrial Fire  2. Industrial Thermal Source  3. Gas Flare
4. Agricultural Burning  5. Wildfire  6. Mining Activity

Fourteen features (exact names, use this order):
brightness_k, frp_mw, firms_confidence_pct, daynight,
observation_count_7d, persistence_hours_7d, frp_trend_pct,
industrial_proximity_km, refinery_proximity_km, mine_proximity_km,
forest_proximity_km, cropland_proximity_km, population_5km,
land_cover

Feature importance (gain) — real values from model_metadata.json:
cropland_proximity_km 0.16999 | mine_proximity_km 0.15621 |
forest_proximity_km 0.14391 | observation_count_7d 0.12391 |
industrial_proximity_km 0.10610 | population_5km 0.09489 |
refinery_proximity_km 0.07933 | land_cover 0.05220 |
persistence_hours_7d 0.03862 | frp_trend_pct 0.02662 |
brightness_k 0.00250 | frp_mw 0.00246 | daynight 0.00197 |
firms_confidence_pct 0.00129

Per-class test results (real, from executed
ml/notebooks/train_xgboost.ipynb, support 1200 per class):
Agricultural Burning  P1.00 R1.00 F1 1.00
Gas Flare             P1.00 R1.00 F1 1.00
Industrial Fire       P0.97 R0.99 F1 0.98
Industrial Thermal Source P0.99 R0.97 F1 0.98
Mining Activity       P1.00 R1.00 F1 1.00
Wildfire              P1.00 R1.00 F1 1.00
macro avg             P0.99 R0.99 F1 0.99 | accuracy 0.99

Risk engine (risk_service.py, version tag "risk-v1"):
  thermal     = clip01(0.55*min(frp_mw/80,1) +
                       0.45*min(max(brightness_k-300,0)/80,1))
  thermal_adj = clip01(thermal * (0.85 + 0.3*firms_confidence_pct/100))
  persistence = clip01(0.5*min(observation_count_7d/15,1) +
                       0.5*min(persistence_hours_7d/72,1))
  population  = clip01(min(population_5km/30000,1)**0.7)
  infra       = clip01(1 - min(min(industrial_proximity_km,
                       refinery_proximity_km)/30,1))
  trend       = clip01((max(frp_trend_pct,-50)+50)/150)
  risk_score  = 0.30*severity + 0.25*persistence + 0.20*exposure
              + 0.15*infrastructure + 0.10*trend   (×100, clip 0–100)
Thresholds (backend config): CRITICAL ≥85, HIGH ≥65, MODERATE ≥40,
LOW <40. Note transparently in the report that the README/dashboard
use 80/60/35 and that the backend values are authoritative.
Every risk response carries the disclaimer: "Operational priority
score for triage only — not a probability of disaster, explosion, or
certified hazard."

Database: 8 tables — thermal_events, thermal_observations,
predictions, risk_assessments, reviews, investigation_sessions,
users, incident_logs. GiST indexes on geom geography(Point,4326)
for thermal_events and thermal_observations.

───────────────────────────────────────────────────────────────────
B. HONESTY RULES — THE REPORT'S CREDIBILITY DEPENDS ON THESE
───────────────────────────────────────────────────────────────────
1. Report 99.40% ONLY with this mandatory qualifier, placed in the
   same paragraph, every single time it appears:
   "measured on a synthetic/development dataset (36,000 rows, six
   exactly-balanced classes); it demonstrates class separability of
   the feature design, not field performance against verified
   ground-truth incidents."
   Quote ml/reports/data_audit.md: "This dataset appears to be
   synthetic/development data. Metrics from this data should NOT be
   claimed as real-world NASA/NTRO performance."
   Also quote Backend/README.md: "Model accuracy (~0.99 test)
   reflects synthetic separability, not field performance."
2. Do NOT claim WorldPop is integrated. State that population
   exposure currently uses a labelled demo-fallback heuristic over
   15 hardcoded Indian anchor cities, returned with
   population_source="demo-fallback" and is_self_reported_synthetic;
   real WorldPop raster ingestion is Phase 2.
3. Name the land-cover source correctly: ESA WorldCover 10 m
   (WORLDCOVER_2021_MAP via WMS GetFeatureInfo), integrated through
   a module named copernicus_service.py. Do not claim Copernicus
   Sentinel Hub / Sentinel-2 is called — those config keys are
   deprecated and unused.
4. Do not claim DBSCAN. The README mentions it, but the implemented
   method is haversine event association at
   EVENT_CLUSTER_RADIUS_KM=2.0 over EVENT_CLUSTER_TIME_HOURS=72.0.
   Describe the real method; list DBSCAN/HDBSCAN as future work.
5. Latency and uptime numbers: no benchmark files exist in the
   repo, and SystemHealth.jsx values (42 ms / 99.98% etc.) are a
   hardcoded static array. Present ALL performance numbers as
   "TBD — instrumented, to be measured" tables, EXCEPT
   ml_inference latency which is genuinely exposed live via
   GET /api/system/status and GET /api/system/pipeline. If a number
   is not measured, leave the cell blank with a footnote.
6. State clearly which mode was demonstrated. ENABLE_LIVE_FIRMS
   defaults to false; the frontend falls back through
   VITE_THERMOS_API_URL → https://thermos-backend-gz3d.onrender.com
   → 42-event procedural mockData.js. Report must say "live-capable
   with demo fallback" and must label screenshots data_mode =
   demo or live.
7. Disclose that the repo contains two parallel FastAPI backends
   (PostGIS-backed Backend/app and MongoDB-backed ml/api) and that
   the canonical production path is the PostGIS one. Present this
   as "dual-persistence reference implementation + migration in
   progress," not as an accident.
8. Banned words: revolutionary, game-changing, 100% accurate,
   completely automated, zero false positives, real-time (unless
   precisely defined and measured), predicts disasters, guarantees
   early warning, NASA cannot detect fires, state-of-the-art.
   Preferred: near-real-time, automated, evidence-grounded,
   risk-aware, context-aware, explainable, confidence-aware,
   human-in-the-loop, decision-support, modular, provenance-aware.

───────────────────────────────────────────────────────────────────
C. THE SENTENCE TO BUILD EVERYTHING AROUND
───────────────────────────────────────────────────────────────────
"THERMOS does not replace satellite detection; it makes satellite
detection interpretable, contextual and actionable."

And this boxed three-layer concept must appear early (page 3–4):
  LAYER 1 — CLASSIFICATION   What is it?     → XGBoost, 6 classes
  LAYER 2 — RISK             How much attention? → weighted engine
  LAYER 3 — INVESTIGATION    Why does the system think so?
                                            → rules-first + Gemini
Rationale line: ML predicts. The risk engine prioritizes. The LLM
explains and investigates — it never performs the detection.

───────────────────────────────────────────────────────────────────
D. CANVA DOCUMENT SETUP
───────────────────────────────────────────────────────────────────
Format: A4 portrait Canva Doc / "Report" design, exported as PDF.
Length: 24–28 pages, 10,000–11,000 words of body prose
        (exclude figure captions, tables, references).
Colour system (thermal palette):
  Ink       #0F172A   body text, dark section dividers
  Ember     #F97316   primary accent, risk-critical
  Amber     #F59E0B   secondary accent, highlights
  Slate     #64748B   captions, secondary labels
  Paper     #F8FAFC   background
  Alert Red #DC2626   CRITICAL tier only
  Cool Teal #0D9488   LOW tier, positive/verified states
Typography: Headings "Sora" or "Playfair Display" (bold);
  Body "Inter" 11–12 pt / 1.5 line spacing; Code and feature names
  in "JetBrains Mono" or "IBM Plex Mono".
Page furniture: running header "THERMOS · SIH26162 · NSUT New Delhi";
  footer with page number and section name. Every figure gets
  "Figure N —" caption in Slate 9 pt; every table gets
  "Table N —" title above and a source/footnote line below.
Section divider pages: full-bleed Ink background, large section
number in Ember, section title in white.
Screenshots: always inside a browser-chrome frame mockup with a
label chip showing the route (/dashboard, /investigator, etc.) and
a data_mode chip (LIVE or DEMO).
Use a consistent 12-column grid, generous white space, and max 2
accent colours per page. Do not use cartoon clipart, generic
"AI robot" imagery, or stock photos of firefighters. Prefer
satellite/thermal imagery, maps, diagrams and data visualisation.
Charts must be rebuilt natively in Canva from the numbers given —
never paste an unreadable chart.

───────────────────────────────────────────────────────────────────
E. SECTION PLAN (section | target words | required content)
───────────────────────────────────────────────────────────────────
0. COVER PAGE — THERMOS title, subtitle, PS ID SIH26162, category,
   theme, institution, team, live demo URL, 2026 badge.
   Beside it: a single "at a glance" panel — 6 classes · 14
   features · 5 risk factors · 8 tables · 40+ endpoints.

1. EXECUTIVE SUMMARY — 400 words
   Detection ≠ understanding. NASA FIRMS gives near-real-time
   VIIRS/MODIS thermal anomalies, but a hotspot may be a vegetation
   fire, agricultural burn, industrial activity, gas flare or other
   source, and cloud cover can obscure detections. THERMOS adds an
   intelligence layer: ingestion → QC → event association →
   geospatial/temporal enrichment → 14-feature vector → XGBoost
   attribution → SHAP evidence → transparent 0–100 risk →
   evidence-grounded AI investigator. End with the "detection-to-
   intelligence pipeline" framing and the decision-support (not
   replacement) disclaimer.

2. PROBLEM STATEMENT & MOTIVATION — 700
   2.1 The observation-to-understanding gap. Core ladder graphic:
     RAW DETECTION → What is it? → How long has it persisted?
     → What surrounds it? → Who could be exposed?
     → How should it be prioritised?
   2.2 Alert fatigue in industrial corridors (Jamnagar, Jharia,
   Delhi, Odisha anchors — the same 15 anchors the demo uses).
   2.3 Delayed escalation without cross-referencing plant
   boundaries, land use and settlement exposure.
   Include the NASA limitations honestly: VIIRS ≈375 m at nadir,
   MODIS ≈1 km; FIRMS products are active-fire/thermal-anomaly
   information rather than exact ground truth.

3. OBJECTIVES — 400
   Primary objective + 7 measurable secondary objectives:
   O1 automated sensor-agnostic ingestion (FIRMS area API,
   MAP_KEY, batching/caching); O2 geospatial enrichment (OSM
   infrastructure, land cover, population); O3 spatio-temporal
   reasoning (count, persistence, trend, association); O4 event
   attribution into the 6-class taxonomy; O5 explainability via
   feature attribution; O6 transparent risk prioritisation;
   O7 investigation with timelines and grounded NL querying.

4. EXISTING SYSTEMS & RESEARCH GAP — 900
   4.1 NASA FIRMS (VIIRS SNPP/NOAA-20, MODIS; area API, WMS, WFS;
   cloud and resolution limits).
   4.2 OSM/Overpass, ESA WorldCover, WorldPop — what each provides
   independently.
   4.3 Other approaches: threshold/hotspot alerting, single-sensor
   dashboards, academic fire detection with optical imagery.
   4.4 THE GAP: these are detection, mapping, land-cover and
   population layers that operate separately. Nobody fuses them
   into an event-centric, explainable, risk-ranked industrial
   attribution layer. Position FIRMS as the detection layer and
   THERMOS as the interpretation + prioritisation layer. Never say
   FIRMS is inadequate.

5. PROPOSED SYSTEM ARCHITECTURE — 800
   Reproduce the pipeline as the report's master figure:
   NASA FIRMS (VIIRS/MODIS) → Ingestion & Validation/QC →
   Event Association Engine → [Temporal History ‖ Spatial Context]
   → Multi-source Fusion → 14-feature vector → XGBoost
   (prediction + probabilities) → SHAP Explainer → Risk Engine →
   Analyst Dashboard → AI Investigator.
   Then describe each box as a named module: firms_service,
   firms_ingestion worker, geo_service (association),
   temporal_service, enrichment_service/geo_enrichment,
   osm_service, copernicus_service, risk_service,
   explainability_service, investigator_service, analytics_service,
   audit_service, state_machine. Include the security layer
   (JWT + RBAC roles PUBLIC/ANALYST/AUTHORITY/RESPONDER/ADMIN,
   rate limiting, API-key in production).

6. DATA ENGINEERING & INGESTION — 750
   FIRMS URL construction and the real field set (latitude,
   longitude, acquisition date/time, brightness, FRP, confidence,
   satellite, instrument, day/night, scan/track). VIIRS confidence
   l/n/h mapped to a 30/60/90 proxy and explicitly never presented
   as a percentage. Five stages: ingestion → validation (bounds,
   timestamps, duplicates, FRP sanity) → normalisation → PostGIS
   spatial indexing (GiST) → event association (2 km / 72 h
   haversine). Explain why a satellite row is not an event.

7. FEATURE ENGINEERING — 700
   Full 14-feature table grouped as Thermal (brightness_k, frp_mw,
   firms_confidence_pct), Temporal (observation_count_7d,
   persistence_hours_7d, frp_trend_pct), Infrastructure proximity
   (industrial, refinery, mine), Environmental (forest, cropland,
   land_cover), Human exposure (population_5km), plus daynight.
   Include the FEATURE_RANGES table. Mandatory methodological
   sentence: "Latitude and longitude are used for spatial
   enrichment and event association rather than being supplied to
   the classifier as predictive variables, to prevent location
   leakage."

8. MACHINE LEARNING METHODOLOGY — 1,100 (most technical chapter)
   8.1 Formulation: multiclass f(X) → (ŷ, P(y|X)).
   8.2 Data: 36,000×15, stratified 28,800/7,200 split, exactly
   balanced classes — and flag that perfect balance is itself a
   synthetic-data indicator.
   8.3 Model selection protocol: candidates compared under one
   split and one feature protocol — Dummy, Logistic Regression,
   Random Forest, Extra Trees, HistGradientBoosting, XGBoost.
   State honestly that only the XGBoost artefact is persisted and
   that the comparison harness exists in ml/train.py but its
   output file is pending.
   8.4 Evaluation: macro-F1 primary (imbalance-robust), plus
   accuracy, precision/recall, weighted F1, balanced accuracy,
   confusion matrix, 3-fold StratifiedKFold.
   8.5 Leakage prevention — the strongest section. Report the real
   findings from ml/reports/leakage_check.md: mine_proximity_km
   class range [0.1, 3.5] vs others [5.0, 119.98];
   forest_proximity_km [0.01, 4.0] vs [5.01, 149.97];
   cropland_proximity_km [0.05, 3.0] vs [3.01, 149.98].
   Mutual information: industrial_proximity 0.8908, refinery 0.8686,
   mine 0.7990, land_cover 0.7846, cropland 0.7696, forest 0.7213,
   observation_count 0.6011, population 0.5981, persistence 0.5524,
   frp_trend 0.3169, daynight 0.1334, brightness 0.1100, frp_mw
   0.0885, confidence 0.0025.
   Quote the report's own verdict: "CRITICAL: Target Leakage
   Detected … these features MUST NOT be used as-is in a production
   model."
   State the mitigation: group-aware event-level splitting,
   proximity features as context rather than labels, and the
   mandatory ablation below.
   8.6 THE ABLATION STUDY (centre-piece experiment):
     Model A — thermal only (brightness_k, frp_mw,
               firms_confidence_pct, daynight)
     Model B — thermal + temporal (+ observation_count_7d,
               persistence_hours_7d, frp_trend_pct)
     Model C — thermal + temporal + geospatial (all 14)
   Present as: "Context materially improves attribution — this is
   the thesis of THERMOS, tested rather than asserted." Mark the
   results cells TBD and run ml/train.py to fill ablation.json
   before submission.

9. EXPLAINABLE AI — 500
   shap.TreeExplainer over the XGBClassifier; slice the 3-D SHAP
   output to the predicted class; top 5 positive and top 3 negative
   contributions; human_readable_summary string. Show a worked
   example card: predicted INDUSTRIAL FIRE, with contributions for
   frp_mw, persistence_hours_7d, industrial_proximity_km,
   population_5km, land_cover. Disclose the
   gain-weighted-fallback path (feature importance × 1.6 with
   per-class hints) used when SHAP is unavailable, so the report is
   provenance-honest. Mandatory wording: "SHAP explains the
   contribution of input features to the model's prediction; it
   does not establish causal relationships."

10. RISK INTELLIGENCE ENGINE — 650
   Write out the full formula from block A with each component
   derivation. Risk ≠ classification: a gas flare is not
   automatically more dangerous than a wildfire. Table of the five
   weights (30/25/20/15/10). Threshold table (85/65/40 backend
   authoritative; note the 80/60/35 dashboard variant). Disclose
   the category calibration overlay applied after scoring
   (Industrial Fire +35 floor 72 cap 98; Wildfire +25/62/95;
   Gas Flare +20/55/85; Mining/ITS +15/48/80) and label it as a
   configurable policy layer. Mandatory sentence: "The weighting
   policy is explicitly configurable and is not presented as a
   learned probability of harm."

11. AI INVESTIGATOR — 550
   Architecture: rule engine first (keyword intents — why/
   classification, exposure, similar/history/trend, high-risk
   explanation, comparison, recommended action, grounded default),
   each returning answer + evidence array; then optional Gemini
   elaboration (temperature 0.2, max_tokens 500, 15 s timeout,
   candidate fallback chain). Show a Q→retrieval→answer flow for
   "Why is this event high risk?".
   Disclose honestly that the tool_* helpers (get_event_details,
   get_event_history, get_nearby_infrastructure, get_similar_events)
   are server-side functions and are NOT yet exposed to the LLM via
   function-calling; only find_similar is wired externally as
   GET /api/events/{id}/similar. Frame true tool-calling as Phase 7.
   Similarity = Euclidean over
   [frp/100, brightness/400, obs/15, persistence/72,
   industrial_prox/30, population/30000] with −0.3 same-class
   bonus, top-5 of 20.
   Guardrails: never invent facilities, satellite imagery,
   government actions, casualties or causes; otherwise respond
   "Insufficient evidence to determine this confidently." Sessions
   are persisted to investigation_sessions with provider recorded.

12. GEOSPATIAL INTELLIGENCE — 550
   Spatial joins (point → nearest facility), buffer analysis,
   Overpass query design (single combined query for
   landuse=industrial, man_made=works, power=plant,
   industrial=refinery, landuse=quarry, man_made=mine,
   landuse=forest, landuse=farmland within 10 km; 2.5 s timeout;
   1 h cache; User-Agent THERMOS-ThermalIntel/1.0; graceful
   source_status="unavailable"), known-industrial registry check at
   15 km, ESA WorldCover WMS GetFeatureInfo 11×11 pixel probe with
   11-class palette and NDVI representatives, PostGIS GiST
   indexing, 2 km/72 h event association.

13. SYSTEM IMPLEMENTATION — 700
   Two backend apps, described as canonical (PostGIS) + reference
   (MongoDB 2dsphere). Full endpoint table (see G below). The 8
   database tables with their key columns. Workers
   (firms_ingestion, event_processing). Caching and rate limits
   (predict 120/60 s, investigator 60/60 s, internal ingest
   5/60 s + Admin role). State machine statuses: active →
   acknowledged → investigating → reviewed → dismissed → resolved.

14. OPERATIONAL INTELLIGENCE CONSOLE (Dashboard/UX) — 450
   Three levels: L1 Situational Awareness (Map route — MapLibre
   heat layer, cluster markers, industrial boundary polygons,
   time slider 24H/7D/30D); L2 Event Triage (/priority risk-ranked
   queue with filters); L3 Investigation (/investigator tabs —
   Spectral & Thermal Dynamics, AI Diagnostic Decision Chain,
   Meteorological Dispersion Simulation, 5-Factor & SHAP
   Attribution). Plus /analytics, /intelligence, /system, and the
   landing 3D Three.js globe. Mention the dual-mode
   live/demo resilience and the visible data_mode badge.

15. FEASIBILITY & SCALABILITY — 600
   API-first, modular, containerised. Horizontal scaling by region
   (worker A/B/C), enrichment caching, stateless inference, GiST
   indexes, APScheduler 15-minute FIRMS poll, Docker Compose
   topology, Vercel + Render split. Include the honest constraint
   that global VIIRS queries can yield tens of thousands of records
   per day, motivating regional bbox querying, batching and caching.

16. LIMITATIONS — 500 (write this with pride; it raises the score)
   L1 Spatial resolution: VIIRS ≈375 m, MODIS ≈1 km → facility-level
   attribution uncertain in dense industrial zones.
   L2 Cloud cover obscures detections.
   L3 Ground truth: reliable labels for all six categories are hard
   to obtain; current training data is synthetic.
   L4 Persistent industrial sources: a refinery may produce heat
   continuously without being an emergency.
   L5 False confidence: high model confidence ≠ ground truth.
   L6 Population layer is a demo heuristic, not WorldPop.
   L7 Risk thresholds and the category calibration overlay are
   policy choices, not measured probabilities.
   Then the mitigation matrix: multi-sensor observations + temporal
   history + contextual enrichment + confidence reporting + human
   review (reviews table with reviewer_note and
   predicted_class/reviewed_class) + pending ground-truth campaign.

17. SECURITY, RELIABILITY & DATA PROVENANCE — 350
   JWT + five-role RBAC, X-API-Key with constant-time compare in
   production, TrustedHost, CORS restricted to the deployed origin,
   rate limits, security headers, secret redaction in logs, audit
   via incident_logs, model_version + feature_schema_version v1 +
   risk_engine_version risk-v1 recorded per prediction, immutable
   raw_payload JSON on every thermal_observation.
   Headline sentence: "Every generated intelligence record remains
   traceable to its originating observation and enrichment sources."

18. TESTING & VALIDATION — 700
   Real current state: 7 test files, 29 test functions
   (test_e2e, test_firms_security, test_osm_copernicus_gemini,
   test_rbac_and_audit, test_state_machine, test_units — covering
   confidence mapping, haversine, association thresholds, feature
   validation, risk levels, FIRMS URL building, investigator rule
   answers). pytest.ini present; no CI workflow yet (state as
   future work).
   Sub-sections: unit / integration / ML (CV, confusion matrix,
   per-class F1, leakage checks) / geospatial (known coordinates
   → expected nearest facility) / failure injection (missing FIRMS
   record, malformed coordinate, unavailable land cover, API
   timeout, LLM failure → deterministic fallback) / performance
   (TBD table: ingestion latency, enrichment time, ml_inference
   from /api/system/status, API response time — leave blank until
   measured).

19. RESULTS — 600, numbers only where they exist
   Table 1 — Model comparison (fill only what is persisted;
   mark other candidate rows "pending run").
   Table 2 — Per-class precision/recall/F1/support (use the real
   notebook numbers from block A).
   Table 3 — Confusion matrix (rebuild as a Canva heat grid from
   notebook predictions; if not reproducible, mark pending).
   Table 4 — Feature importance (gain) bar chart with the 14 real
   values.
   Table 5 — Leakage/MI findings.
   Table 6 — Ablation A/B/C (TBD until ml/train.py executed).
   Each table: one sentence of interpretation, no adjectives.

20. DEMONSTRATION SCENARIO — 450
   Walk one event end-to-end, e.g. IND-2048 in an industrial zone:
   FIRMS detection → 72 h history → nearest OSM facility (refinery)
   → ESA WorldCover class + demo population exposure → 14-feature
   vector → XGBoost class + probabilities → SHAP contributions →
   5-factor risk decomposition → risk tier → AI investigator
   answer with evidence list. Use real screenshots in browser
   frames. Label clearly if the capture is demo mode.

21. IMPACT — 400
   Claim only: faster screening of large detection volumes,
   better separation of industrial vs non-industrial activity,
   identification of persistent thermal sources, prioritisation of
   analyst attention, consolidated geospatial intelligence,
   traceable event assessments.
   Closing line: "THERMOS is positioned as a decision-support
   system, not as a replacement for ground verification or
   emergency command."

22. FUTURE ROADMAP — 400
   Phase 1 (current): FIRMS ingestion + OSM/WorldCover enrichment +
   XGBoost + SHAP + risk engine + dashboard + rule/Gemini
   investigator.
   Phase 2: real WorldPop raster ingestion + group-aware event-level
   CV + persisted ablation/benchmark harness + CI.
   Phase 3: Computer Vision — Sentinel-2 (10 m) optical smoke
   plume verification, ViT.
   Phase 4: Vision-Language Models — image + thermal + structured
   context reasoning.
   Phase 5: Graph-based event association — industrial sites,
   observations and infrastructure as a graph.
   Phase 6: Graph Neural Networks for large-scale spatial-event
   relationships.
   Phase 7: Agentic investigation — true LLM function-calling over
   get_event_history / get_nearby_infrastructure / get_similar_events.
   Phase 8: Multi-sensor fusion (additional feeds, higher
   resolution), CAP/NDRF/SDMA integration, Gaussian plume
   dispersion, IoT/MQTT fence-line sensors, drone dispatch.
   This is where CV, VLM, GNN, LLM and Agentic AI live — clearly
   as roadmap, never as shipped capability.

23. CONCLUSION — 300
   Circle back: detection does not imply understanding. THERMOS
   converts isolated satellite detections into structured thermal
   events by combining temporal behaviour, geospatial
   infrastructure, environmental context and exposure. ML provides
   attribution, XAI provides evidence, the risk engine provides
   prioritisation, the investigator provides grounded narrative.
   Final line: "THERMOS transforms the workflow from observing heat
   to understanding events."

24. REFERENCES — numbered, authoritative sources first:
   NASA FIRMS API and product documentation; FIRMS data quality /
   cloud & detection caveats; VIIRS 375 m and MODIS 1 km product
   specs; ESA WorldCover 10 m product documentation; WorldPop
   dataset documentation; OpenStreetMap & Overpass API docs;
   Lundberg SHAP / arXiv 1705.07874; Chen & Guestrin XGBoost
   arXiv 1603.02754; FastAPI; PostgreSQL/PostGIS; MapLibre GL;
   React; the SIH 2026 problem statement SIH26162.

25. APPENDICES — A: full endpoint table; B: database schema;
   C: environment variables (.env.example); D: feature ranges;
   E: test inventory.

───────────────────────────────────────────────────────────────────
F. FIGURES TO BUILD IN CANVA (14–16, one per concept)
───────────────────────────────────────────────────────────────────
Fig 1  Problem landscape: thermal anomaly → attribution gap ladder
Fig 2  THERMOS system architecture (master pipeline diagram)
Fig 3  FIRMS ingestion → validation → normalisation → indexing flow
Fig 4  Spatio-temporal event association (2 km / 72 h, before/after
       clustering of raw rows into one event)
Fig 5  The 14-feature vector, drawn as five grouped blocks
Fig 6  Training & validation workflow (36,000 → 28,800/7,200
       stratified → 3-fold CV → metrics)
Fig 7  Confusion matrix heat grid (real notebook numbers)
Fig 8  Mutual-information / leakage bar chart (14 real MI values)
Fig 9  Ablation: Model A vs B vs C macro-F1 (TBD markers until run)
Fig 10 Feature importance (gain) horizontal bar — 14 real values
Fig 11 SHAP-style contribution waterfall for one example event
Fig 12 Risk-score decomposition — five weighted segments summing
       to 0–100 with threshold bands
Fig 13 AI Investigator evidence flow (question → rule engine →
       evidence → optional Gemini → cited answer)
Fig 14 BEFORE vs AFTER THERMOS (two-column vertical flow) —
       BEFORE: hotspot → manual interpretation → separate sources →
       investigation; AFTER: detection → contextual event →
       classification + explanation → risk prioritisation →
       investigation
Fig 15 Operational console: three levels (awareness / triage /
       investigation) with real screenshots in browser frames
Fig 16 Deployment topology (Vercel, Render, Docker Compose:
       PostGIS, Redis, backend; demo fallback path)

───────────────────────────────────────────────────────────────────
G. TABLES TO BUILD (all with source footnote lines)
───────────────────────────────────────────────────────────────────
T1  Data sources & integration status — NASA FIRMS (live-capable,
    demo default), OSM/Overpass (real HTTP), ESA WorldCover (real
    WMS), WorldPop (demo heuristic — state this), MongoDB
    facilities registry (20 entries).
T2  FIRMS fields consumed + validation rule per field.
T3  14-feature design table: name | group | unit/range | meaning.
T4  Six-class taxonomy: label | operational definition | example.
T5  Model hyperparameters.
T6  Per-class evaluation results (real).
T7  Leakage findings & MI values (real).
T8  Ablation A/B/C (TBD).
T9  Risk factors: factor | component formula | weight.
T10 Risk tier thresholds + category calibration overlay.
T11 API endpoints (canonical PostGIS app, group by resource):
    GET /api/health, GET /api/fires,
    GET /api/firms/tile/{layer}/{z}/{x}/{y}.png, GET /api/events,
    GET /api/events/{id}, POST /api/events/process,
    PATCH /api/events/{id}/status, GET /api/events/{id}/timeline,
    POST /api/predict, GET /api/analytics/summary,
    GET /api/analytics/timeseries, GET /api/analytics/classifications,
    GET /api/analytics/risk-distribution, GET /api/analytics/regions,
    GET /api/alerts, POST /api/alerts/{id}/dispatch,
    POST /api/investigator/ask, POST /api/events/{id}/ask,
    GET /api/investigator/sessions, POST /api/reviews,
    GET /api/reviews, GET /api/system/status, GET /api/system/pipeline,
    POST /api/demo/scenario/{name}, GET /api/events/{id}/similar,
    POST /api/internal/ingest/firms, GET /api/ai/status,
    POST /api/ai/analyze, POST /api/ai/chat.
T12 Database schema (8 tables, key columns, indexes).
T13 Test inventory (7 files / 29 tests, what each covers).
T14 Performance targets vs measured — leave Measured blank/TBD.
T15 Limitation → mitigation matrix.
T16 Technology stack with exact pinned versions.

───────────────────────────────────────────────────────────────────
H. ATS / KEYWORD STRATEGY — thread these as plain text
───────────────────────────────────────────────────────────────────
Artificial Intelligence (AI); Machine Learning (ML); Geospatial AI;
Remote Sensing; Satellite Thermal Anomaly Detection; NASA FIRMS;
VIIRS; MODIS; Multimodal Data Fusion; Spatio-Temporal Analysis;
Geospatial Intelligence; GIS; Machine Learning Classification;
XGBoost; Explainable AI (XAI); SHAP; Risk Intelligence; Predictive
Analytics; Anomaly Detection; PostGIS; FastAPI; React; OpenStreetMap;
Overpass API; ESA WorldCover; WorldPop; Large Language Model (LLM);
Retrieval-Augmented Generation (RAG); Evidence-Grounded AI;
Human-in-the-Loop; Decision Support System; Data Provenance;
Near-Real-Time; Event-Centric Representation; Detection-to-
Intelligence Pipeline.
They must appear naturally in the abstract, methodology,
architecture, implementation, validation and conclusion — never
confined to a "technology stack" page.

Winning-level phrases to sprinkle: "detection-to-intelligence
pipeline", "event-centric geospatial representation", "multi-source
contextual enrichment", "spatio-temporal event reasoning",
"context-aware thermal attribution", "explainable prediction rather
than opaque classification", "risk-aware analyst triage",
"evidence-grounded generative investigation", "sensor-agnostic
ingestion architecture", "provenance-aware intelligence pipeline",
"operational intelligence layer over satellite detection".

───────────────────────────────────────────────────────────────────
I. DELIVERABLE ORDER
───────────────────────────────────────────────────────────────────
1. Cover + at-a-glance panel
2. Table of contents with section numbers and page ranges
3. All 25 sections, fully written to the word budgets above
4. 14–16 figures with captions
5. 16 tables with footnotes
6. References, appendices
7. A one-page "Data & Metric Provenance" box near the front
   listing which numbers are measured, which are synthetic-data
   derived, and which are TBD — this page is the report's
   credibility anchor.

Before writing prose, first produce: the architecture diagram, the
feature table, the risk formula, the DB schema, the endpoint list
and the experiment plan. Lock those. Then write the 10,000 words.
Never let the text describe a system that differs from those
artefacts.
═══════════════════════════════════════════════════════════════════
```

---

## Word budget check

| # | Section | Words |
|---|---|---|
| 1 | Executive Summary | 400 |
| 2 | Problem & Motivation | 700 |
| 3 | Objectives | 400 |
| 4 | Existing Systems & Research Gap | 900 |
| 5 | Proposed Architecture | 800 |
| 6 | Data Engineering & Ingestion | 750 |
| 7 | Feature Engineering | 700 |
| 8 | ML Methodology | 1,100 |
| 9 | Explainable AI | 500 |
| 10 | Risk Engine | 650 |
| 11 | AI Investigator | 550 |
| 12 | Geospatial Intelligence | 550 |
| 13 | Implementation | 700 |
| 14 | Dashboard / UX | 450 |
| 15 | Feasibility & Scalability | 600 |
| 16 | Limitations | 500 |
| 17 | Security & Provenance | 350 |
| 18 | Testing & Validation | 700 |
| 19 | Results | 600 |
| 20 | Demo Scenario | 450 |
| 21 | Impact | 400 |
| 22 | Future Roadmap | 400 |
| 23 | Conclusion | 300 |
| | **Total** | **13,050** |

Trim to 10,000–11,000 by cutting Section 4 (900 → 700), Section 8 (1,100 → 950) and Section 18 (700 → 550). Figures, tables and references are excluded.

## Before you write — two blockers

1. **Ablation results do not exist.** `backend-ml/ml/train.py` writes `ml/reports/ablation.json`, but that file is not in the repo. Section 8.6 and Table 8 are empty until you run it.
2. **Screenshots need a `data_mode` label.** `ENABLE_LIVE_FIRMS` defaults to `false`, so a capture will likely be demo mode. Label it honestly rather than implying live NASA data.
