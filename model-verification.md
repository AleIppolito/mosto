# ferment-calculator — Model Verification & Test Data

Reference values, external calculators, and worked test cases for checking the
nutrient / bentonite / SO2 model in `index.html`.

Last updated: 2026-09-21

---

## 1. Cross-check tools

### Wine (vino rosso / bianco)

| Tool | Link | Notes |
|---|---|---|
| **FermCalc YAN** | https://fermcalc.com/yan/ | Best ground truth. Implements Scott Labs Fermentation Handbook formula, shows all steps, staged additions, editable nutrient %N |
| Scott Labs Nutrition Planner | https://scottlab.com/fermentation-nutrition-planning | Vendor tool, Brix + strain N-need → YAN table |
| WineBusiness YAN | https://www.winebusiness.com/calculator/winemaking/calc/2114/ | Quick YAN addition converter |
| VinoEnology | https://vinoenology.com/calculators/fermentation/ | YAN + nutrient calculator |
| Lamothe-Abiet | https://lamothe-abiet.com/en/decision-making-tools/your-nutritional-needs/ | YAN need by ABV + yeast |
| AZ3 Oeno | https://en.az3oeno.com/tools/calculation/calculation-yeast-assimilable-nitrogen | YAN + activator doses |
| DG Winemaking worksheet | https://www.dgwinemaking.com/portfolio/yan-calculation-worksheet/ | Practice problems **with answers** |
| Nanaimo Wine Makers (Excel) | https://nanaimowinemakers.com/yan-calculator/ | .xlsx, Scott Labs 2021 recommendations |

### Cider (sidro)

| Tool | Link |
|---|---|
| Scott Labs Cider Nutrition Planner | https://scottlab.com/cider-fermentation-nutrition-planning |
| Same, as PDF | https://scottlab.com/content/files/images/articles/cider%20fermentation%20nutrition%20planner.pdf |
| Scott Labs Cider Nutrition Guide | https://scottlab.com/complete-guide-to-cider-fermentation-nutrition |
| Iowa State cider nutrient strategies | https://www.extension.iastate.edu/wine/cider-nutrient-strategies |

### SO2 / bentonite / yeast

| Topic | Link |
|---|---|
| Molecular SO2 / pH (OSU) | https://extension.okstate.edu/fact-sheets/understanding-free-sulfur-dioxide-fso2-in-wine.html |
| Sulfite calculator (WineMakerMag) | https://winemakermag.com/resource-guide/sulfite-calculator |
| SO2 vs pH (WineBusiness) | https://www.winebusiness.com/calculator/winemaking/calc/21/ |
| Vinmetrica sulfite | https://vinmetrica.com/sulfite-adjustments-what-to-do-with-my-so2-results/ |
| Scott Labs — Fermenting on Bentonite | https://scottlab.com/fermenting-on-bentonite |
| Fermentaid-O TDS (Lallemand) | https://products.lallemandwine.com/storage/files/nutrients-and-protectors/25-technical-datasheet-us-1755868000.pdf |
| Fermentaid-K TDS (Lallemand) | https://products.lallemandwine.com/storage/files/nutrients-and-protectors/26-technical-datasheet-us-1716369678.pdf |
| Scott Labs Winemaking Handbook 2025-2026 | https://scottlab.com/content/files/documents/handbooks/2025-2026%20scott%20labs%20winemaking%20handbook.pdf |

---

## 2. Reference constants

### Nutrient nitrogen content (%N → mg N/g)

Source: FermCalc / Scott Labs Handbook.

| Nutrient | %N | mg N/g | App value | Verdict |
|---|---|---|---|---|
| DAP | 21 | 210 | 210 | ✅ exact (also ~212 via molar mass) |
| Fermaid-K | 10 | 100 | 100 | ✅ confirmed |
| Fermaid-O | 4.3 | 43 | 40 | 🟡 ~7% low (Lallemand TDS says 40) |
| Ciderferm (BSG) | 12 | 120 | — | not in app |

### YAN target — the two models

**Scott Labs / FermCalc formula (Brix-based, uses yeast N-need multiplier):**

```
nt = y × 10 × Brix × SG / 0.9982
y = 0.90 (medium N-need yeast), 1.25 (high N-need)
```

| Brix | SG | medium (y=0.90) | high (y=1.25) | Bisson & Butzke |
|---|---|---|---|---|
| 20 | 1.0830 | 195 | 271 | — |
| 21 | 1.0874 | 206 | 286 | 200 |
| 22 | 1.0919 | 217 | 301 | — |
| 23 | 1.0965 | 227 | 316 | 250 |
| 25 | 1.1056 | 249 | 346 | 300 |
| 27 | 1.1149 | 271 | 377 | 350 |

**App formula (Brix-based, FermCalc / Scott Labs model — same as above):**

```
target = yanTargetFromBrix(brix, og, strain.nFactor)
       = strain.nFactor × 10 × Brix × SG / 0.9982
abv = (OG − 1) × 131.25        (display only)
```

`strain.nFactor` doubles as the yeast N-need multiplier `y` (0.75 low, 0.9
medium, 1.25 high). No clamp. Sources per strain in §4.

### 2a. Yeast nitrogen-need multipliers (per strain, from manufacture TDS)

| Strain | N-need | nFactor | Source |
|---|---|---|---|
| EC-1118 | Low | 0.75 | Lallemand TDS, FermCalc |
| RC212 | Medium | 0.9 | Lallemand TDS («Medium relative nitrogen demand»), FermCalc |
| D254 | Medium | 0.9 | FermCalc (ICV D254) |
| QA23 | Low | 0.75 | Lallemand TDS («Very low»), FermCalc |
| VL3 | **High** | **1.25** | **Laffort TDS (Zymaflore VL3, «High nitrogen requirements»)** — brand is Laffort, not Lalvin |
| D47 | Low | 0.75 | FermCalc (ICV D47), Lallemand quick-ref |
| 71B | Low | 0.75 | Lallemand TDS |
| K1-V1116 | Low | 0.75 | Lallemand TDS, Scott Labs product page («LOW») |
| M05 | Medium | 0.9 | Mangrove Jack's (low-to-medium → 0.9) |
| Zymoferm Bayanus | Medium* | 0.9 | no published data — estimate |

Legend: `*` = estimate, no authoritative TDS found.

### Yeast rate / rehydration

| Item | Scott Labs | App |
|---|---|---|
| Inoculum | 25 g/hL (30 g/hL if >25 Brix) | 20–35 g/hL (0.20–0.35 g/L) |
| Rehydration nutrient (Go-Ferm) | 30 g/hL, 1 yeast : 1.25 Go-Ferm | not implemented |
| Rehydration water | Go-Ferm in 20× its weight, 43 °C | 10× yeast grams, 35–40 °C |

---

## 3. Test cases

At **100 L** a Fermaid-K dose in grams equals the deficit in ppm (because factor
= 100 and 100 L = 1 hL) — handy sanity check.

### Test A — Vino rosso, 22 Brix, 100 L, medium yeast (y=0.9), must YAN 120

- OG ≈ 1.0919 · ABV ≈ 12.07%
- Scott/FermCalc target ≈ **216.6 ppm** → deficit **96.6 ppm**
- NOTE: default strain EC-1118 is now Low (0.75) → target 180.5 ppm, deficit 60.5 ppm. This test pins the medium-y multiplier, not the default strain.

| Nutrient | Expected g | Notes |
|---|---|---|
| Fermaid-K | **96.5 g** | deficit ppm, 100 L |
| DAP | **46.0 g** | ÷210 |
| Fermaid-O (app, eff 3, ÷120) | **80.4 g** | matches FermCalc to ~7% |
| Fermaid-O (FermCalc, eff 3, ÷129) | **74.9 g** | ground truth (43 mg/g) |

### Test A2 — Vino bianco, 20 Brix, 50 L, medium yeast (y=0.9), must YAN 150

- OG ≈ 1.0830 · ABV ≈ 10.89%
- FermCalc target ≈ **195.3 ppm** → deficit **45.4 ppm**

| Nutrient | Expected g |
|---|---|
| Fermaid-K | **22.7 g** |
| DAP | **10.8 g** |
| Fermaid-O (app, eff 3) | **18.9 g** |
| Fermaid-O (FermCalc eff 3) | **17.6 g** |

### Test B — Sidro, 16 Brix, 20 L, must YAN 40

- OG ≈ 1.0654 · ABV ≈ 8.59%
- Scott cider example: 16 Brix → **144 ppm** target (FermCalc medium 153.7, app 153.6)
- Deficit 153.6 − 40 = **113.6 ppm**

| Nutrient | App | FermCalc eff 3 |
|---|---|---|
| Fermaid-O | 18.9 g | **17.6 g** |
| Fermaid-K | 22.7 g | 22.7 g (no efficiency) |

### Arithmetic check (model-independent)

Pick any deficit `d` (ppm) and volume `V` (L):

```
grams = d × V / (factor × efficiency)   factor: DAP 210, Fermaid-K 100, Fermaid-O 40
                                        efficiency: 3 for Fermaid-O (wine/cider), else 1
```

The division matches FermCalc exactly when the same deficit is supplied. The only
residual difference is Fermaid-O `%N` (app 40 vs FermCalc 43).

---

## 4. Model status

Both gaps flagged in the 2026-09-17 review are fixed. Remaining items are minor.

### ✅ Wine/cider + Fermaid-O: organic-efficiency credit — FIXED

App divides Fermaid-O mass by `ORGANIC_N_EFFICIENCY = 3` on all non-TOSNA paths.
Test A dropped from 241.5 g → **80.4 g** vs FermCalc's 74.9 g. Mead unchanged
(the TOSNA path already accounts for it).

### ✅ Wine YAN target — FIXED

Replaced the ABV ladder with the FermCalc formula
`y × 10 × Brix × SG / 0.9982`, using `strain.nFactor` as `y`. Test A target
150 → **216.5 ppm**, matching FermCalc's 216.6. Applies to vino, sidro, and
idromele (non-TOSNA).

### 🟢 Fermaid-O %N

App 40 (Lallemand TDS) vs FermCalc 43. ~7% low → app doses ~7% higher. Kept the
manufacturer figure deliberately.

### 🟢 No minimum YAN floor

The old ladder clamped to [150, 300]. The FermCalc model is unclamped, so
low-Brix musts (e.g. 14 Brix → ~134 ppm) target below 150. Deliberate — matches
the reference tool.

### 🟢 Yeast rate

App 20–35 g/hL brackets Scott's 25–30 g/hL. Fine.

---

## 5. App model reference (current `index.html`)

| Quantity | Formula / constant |
|---|---|
| ABV potential | `(OG − 1) × 131.25` |
| Yeast rate (g/L) | <1.080→0.20, <1.100→0.25, <1.120→0.30, ≥1.120→0.35 |
| Rehydration water | 10 × yeast grams |
| YAN target | `strain.nFactor × 10 × Brix × SG / 0.9982` (`yanTargetFromBrix()`) |
| Yeast nFactor | per-strain (see §2a): 0.75 low / 0.9 medium / 1.25 high. No default 0.9 blanket — EC-1118, QA23, D47, K1-V1116, 71B = 0.75; RC212, D254, M05 = 0.9; VL3 = 1.25 (Laffort); Zymoferm = 0.9 (estimate) |
| Default must YAN | rosso **120**, bianco 150, idromele 5, sidro 40 — see §2b (real must survey data; all user-overridable) |
| NUTRIENT_FACTORS | DAP 210, Fermaid-K 100, Fermaid-O 40 — each overridable in UI (mg N/g) |
| ORGANIC_N_EFFICIENCY | 3 (Fermaid-O, non-mead only) — overridable in UI |
| Nutrient (vino/sidro/idromele non-TOSNA) | `deficit × V / (factor × organicEff)` |
| Nutrient (idromele + Fermaid-O) | TOSNA: `(Brix × 10 × nFactor / 50) × (L / 3.78541)` |
| Bentonite (g/L) | rosso 0.4, bianco 0.75, idromele 0.75, sidro 0.4 |
| SO2 free target | `molecular × (1 + 10^(pH − 1.81))` |
| Molecular SO2 | rosso 0.5 ppm, bianco/idromele/sidro 0.8 ppm |
| KMBS | `ppm × L / 576` |

---

## 6. Open items

1. ~~Source real nitrogen-need data for wine strains~~ — DONE 2026-09-21 (see §2a). Only
   Zymoferm Bayanus remains an estimate (no public TDS).
2. ~~Make `%N` and the efficiency factor editable in the UI~~ — DONE 2026-09-21 (per-product
   mg N/g + organic-efficiency override). Overrides are not persisted across reloads.
3. Go-Ferm rehydration nutrient (would raise efficiency 3 → 4) — deferred (user doesn't use Go-Ferm).
