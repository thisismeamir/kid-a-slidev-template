---
theme: ./
index: "FCC-EP"
kicker: "CERN — EP-FCC"
title: "Particle Flow Reconstruction at FCC-ee"
subtitle: "Cluster-level calibration and W mass sensitivity"
author: "A. Ebrahimi"
affiliation: "CERN"
date: "2026"
---

---
layout: section
index: "02"
kicker: "SECTION"
title: "PF Reconstruction Pipeline"
subtitle: "PandoraPFA within the FCC-ee software stack"
agenda:
  - Cluster formation
  - Energy calibration
  - W mass sensitivity
---

asd

---
layout: content
index: "03"
kicker: "RESULTS"
title: "Cluster Energy Calibration"
---

something else
::aside::
Calibration performed on Z→qq̄ samples, 91.2 GeV


---
layout: content-split
title: "PF Cluster Formation"
reverse: false
ratio: "30/70"
---

::left::
Topological clustering groups calorimeter hits using nearest-neighbour
distance in η–φ, seeded by local energy maxima above threshold.

::right::
```python
clusters = topo_cluster(
    hits, seed_threshold=4.0,
    neighbour_threshold=2.0,
)
```
 

---
layout: content-figure
title: "ECAL/HCAL Boundary Effects"
ratio: "100/0"
caption: "Energy leakage at calorimeter boundary, before calibration"
source: "Z→qq̄, √s = 91.2 GeV"
---

::figure::
![](https://commons.wikimedia.org/wiki/File:002_Jabiru_feeding_its_babies_in_their_nest_in_Encontro_das_%C3%81guas_State_Park_Photo_by_Giles_Laurent.jpg#/media/File:002_Jabiru_feeding_its_babies_in_their_nest_in_Encontro_das_%C3%81guas_State_Park_Photo_by_Giles_Laurent.jpg)



---
layout: content-statement
kicker: "RESULT"
support: "Something"
source: "FCC-ee CDR, Vol. 2"
---

Post-calibration jet energy resolution improves by **45%** at the ECAL/HCAL boundary.

---
layout: content-quote
kicker: "PERSPECTIVE"
ratio: "40/60"
attribution: "V. Domcke"
context: "CERN-TH, on cosmic string PTA constraints"
---

Cosmic strings remain one of the few probes that connect the GUT scale directly to nanohertz gravitational waves.
