# ALB Flow and EAT Operations Guide
## Will be available in 0.2.11 or 0.2.12

This guide explains how **EAT** works in ALB from a controller point of view: what you see in the UI, what the buttons do, and how collaboration works between an **FMR (digital coordinator)** and **peers**.

Current baseline: **EAT:AR** (arrival‑rate based). A future “EAT to landing slot” mode (EAT:Landing / EAT:Threshold) will be documented later.

---

## Abbreviations

- **EAT** — Estimated Approach Time (ALB’s scheduling time reference)
- **FMR** — Flow Management Responsible (coordinating authority for the plan)
- **TXE** — Transmit EAT requests (FMR toggle: request peers publish holding-list EAT)
- **HLW** — Hold‑List Write accept (peer opt‑in: allow ALB to publish holding-list EAT for aircraft you control)
- **HLS** — Holding‑List Sync / override ingestion (treat holding-list EAT as a constraint in the plan)
- **EATLOOP** — (will be renamed) Drift correction / cascade correction of downstream timing
- **SWAP** — Swap two aircraft in the sequence
- **PLR** — Planned Landing Rate (used by EAT:Landing later)

---

## 1) EAT modes: EAT:AR vs EAT:Landing (future)

### EAT:AR (current baseline)
EAT:AR maintains spacing using **arrival‑rate assumptions** per stream/via‑fix. It is robust and simple, but can become crude when:
- traffic distribution across STARs changes over time
- heavy vectoring makes route‑based prediction unreliable
- winds/groundspeed changes distort timing

### EAT:Landing / EAT:Threshold (future)
EAT:Landing aims to schedule aircraft to **landing slots** (FAF/threshold) in order to achieve a target **PLR** at the airport (not runway). In that mode:
- the schedule is driven by *slot availability*, not by stream arrival rates
- spacing may not be uniform across streams
- manual constraints and SWAP remain the primary controller tools

---

## 2) What EAT means in ALB (today)

ALB maintains two “views” of EAT:

- **Auto EAT (ALB plan)**  
  The time ALB currently predicts/targets for an aircraft (today: EAT:AR). This drives sequencing and spacing.

- **Holding‑list EAT (controller-visible)**  
  The EAT shown in the TopSky holding list / holding UI. Controllers can set this manually and monitor it.

ALB can optionally help publish holding‑list EAT values, but **only if the responsible controller opts in** (HLW).

---

## 3) Roles and authority

### FMR (digital coordinator)
When FMR is active, the FMR’s ALB instance is the **authority for the master plan** (order and target spacing). FMR can request that peers publish holding‑list EAT values for aircraft they control.

### Peer / responsible controller
The responsible controller always retains authority:
- you can set a manual holding‑list EAT at any time;
- you can reorder aircraft using SWAP;
- you can opt in/out of ALB publishing holding‑list EAT on your behalf.

---

## 4) Coordination when nobody manually claims FMR (Auto‑FMR)

If no controller explicitly claims FMR, ALB can run **Auto‑FMR**:

- Multiple ALB clients may be online for the same destination.
- One client is **auto‑selected as FMR** using a negotiation method (based on factors like visibility and number of aircraft viewed).
- If a controller **manually claims FMR**, that manual claim overrides the auto selection.
- The current FMR status is visible via ALB’s peer/legend displays.

Operational implication:
- Auto‑FMR provides “a coordinator by default” so the system can keep a coherent plan even without a dedicated human FMR.

---

## 5) Buttons and indicators

### TXE (FMR)
**TXE** controls whether the FMR will request holding‑list EAT publication.

- **TXE ON**: FMR issues publication requests (rate‑limited and change‑thresholded).
- **TXE OFF**: FMR does not request holding‑list publication.

Use TXE when you want ALB to actively drive holding‑list values to keep everyone aligned.

---

### HLW (peer opt‑in)
**HLW** is your opt‑in switch that allows ALB to publish holding‑list EAT values *for aircraft you control* when requested by FMR.

- **HLW ON**: ALB may apply coordinator requests for aircraft you are responsible for.
- **HLW OFF**: ALB will not change holding‑list EAT for your aircraft.

**Attention highlight:**  
If an FMR has TXE enabled and your HLW is OFF, HLW is highlighted (light yellow background) to prompt you that the coordinator is requesting opt‑in.

---

### HLS (holding list sync / overrides)
**HLS** controls whether ALB treats a holding‑list EAT as an **override input**.

- **HLS ON**: a manually set holding‑list EAT is treated as a constraint and influences planning.
- **HLS OFF**: holding‑list EAT is displayed/monitored, but does not override the plan.

This is most important at the FMR, so the master plan honors real controller constraints.

---

### EATLOOP
**EATLOOP** is ALB’s “drift correction” mechanism: it keeps the plan coherent over long sessions by re‑aligning downstream timing when reality diverges from the plan.

Operationally, EATLOOP is intended to be safe and generally enabled.

---

### SWAP
Swapping two aircraft in a specific STAR flow is done in the TimelineView by double-right clicking the aircraft you want to move forward in the flow.
SWAP is the “cheap tool” to fix ordering:

- Use SWAP when the schedule is correct in principle but two aircraft need to trade places.
- A SWAP changes both the local and global sequence so everyone stays aligned.

---

### Peer dropdown table
The peers dropdown shows, per connected peer, their current state:
- HLW on/off
- HLS on/off
- (optionally) TXE / other preference flags

Use this to see who has opted in to coordinator publishing.

---

### Manual marker in the timeline: `m`
A small 1‑character indicator field is available:

- `m` = holding‑list EAT is **manual/controller-set**
- empty = holding‑list EAT is **automatic / ALB‑applied**

**Where it appears:** the `m` is shown in ALB’s **timeline view**, using the **gain/lose (gain/loose) column/field** (not in the TopSky holding list).

---

## 6) Normal workflows

### 6.1 FMR workflow (digital coordinator)
1) Claim/act as FMR (or rely on Auto‑FMR if nobody claims).
2) Enable **TXE** if you want ALB to request holding‑list publication.
3) Monitor the peers dropdown:
   - peers with HLW OFF may not apply requests.
4) Use **SWAP** to correct ordering when needed.
5) If controllers set manual holding‑list EAT values, keep **HLS ON** so the plan respects those constraints.

---

### 6.2 Peer workflow (responsible controller)
1) Decide if you want to opt in to coordinator requests:
   - enable **HLW** if you accept ALB publishing holding‑list EAT for your aircraft.
2) If the plan needs correction:
   - set a manual holding‑list EAT (it will show `m` in the timeline)
   - use **SWAP** to adjust ordering
3) If you want your manual entry to influence planning:
   - enable **HLS** (especially useful if you are coordinating a stream locally)

---

## 7) Common situations and expected behavior

### “I set a manual holding‑list EAT”
- The timeline shows `m` next to that aircraft (in the gain/lose field).
- If HLS is ON (at the FMR), the manual EAT acts as a constraint and will influence spacing behind it.

### “FMR is requesting but my HLW is OFF”
- HLW button is highlighted.
- You will not see holding‑list EAT values being changed automatically for your aircraft until you enable HLW.

### “FMR resigns or disappears”
- Coordinator requests stop.
- Controllers continue with local planning and manual holding‑list control.

---

## 8) What changes with EAT:Landing later
EAT:AR keeps approximate spacing using arrival‑rate assumptions. EAT:Landing will instead target landing slots to meet PLR at the airport level. Manual overrides and SWAP remain central tools in both modes.

