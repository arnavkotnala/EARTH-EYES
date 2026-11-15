# Earth Intelligence Platform — Explained in Simple Words

This guide explains **what this application does**, **where every single dataset comes from**, **every API it talks to**, and **why each one is used** — all in plain, everyday language without heavy jargon.

---

## 1. What Is This Application? (The 30-Second Summary)

Think of this app as **an interactive map of Earth combined with real satellite sensors, scientific databases, and an AI assistant** that helps people understand and protect nature.

When you open the app, you get **three connected tools** that you can switch between at the top of the screen:

1. **🌲 Forest Eyes** — Measures how many trees a forest has lost over the last 24 years, how much carbon pollution that caused, and **which exact tree species can actually survive and grow there** if you want to replant it.
2. **🌊 River Eyes** — Maps nearly **1 million rivers worldwide**, checks live flood risk, soil, and earthquake history, measures **how muddy or algae-filled a river or lake is from space**, and finds the best places along rivers to generate **clean hydropower electricity** for nearby cities.
3. **⚠️ Hazard Lens** — Lets you click **any spot on Earth**, takes a real satellite photo of that spot alongside live rain, slope, flood, soil, and forest data, and asks a vision-capable AI (**Gemma 4**) to check for **natural disaster risks** like floods, landslides, riverbank erosion, or glacier hazards.

> **The Golden Rule of How This App Works:**
> The AI **never guesses or invents numbers**. Scientific datasets and satellites measure all the real numbers first (rainfall, elevation, river flow, soil pH, forest loss, water colour). The AI is only used to **look at real satellite photos** and **explain those real numbers in simple human words** — and if the AI is ever offline or busy, every tool has a built-in math/rule backup so the app still works!

---

## 2. What Does Each Part of the App Actually Do?

### Part A: Forest Eyes (`frontend/forest-eyes/`)

1. **Deforestation & Carbon Loss Calculator**
   - You type the name of any place (or click anywhere on the map) and pick a circle radius from **1 km to 50 km**.
   - The app calculates:
     - **How much forest existed in the year 2000** (baseline forest area).
     - **How many hectares of trees were lost year-by-year from 2001 to 2024**.
     - **How fast the forest is shrinking** per year (and labels the danger as *Low*, *Mid*, *High*, or *Very High*).
     - **How many tonnes of carbon dioxide (CO₂)** were released into the air when those trees were lost.
     - **How many new trees need to be planted** to absorb that CO₂ back over 40 years (realistically assuming only 60% of young saplings survive to adulthood), or how many trees are needed to replant the cleared land at 400 trees per hectare.

2. **2D Map, 3D Spinning Globe & Deforestation Time-Lapse**
   - You can view forest loss in **4 visual styles**:
     - **Dot form**: Individual pink/red pixels showing exact 30-metre patches where trees disappeared.
     - **Colour form**: Yellow-to-orange-to-red glow showing light vs. heavy clearing.
     - **Heatmap**: Turns healthy land green, water blue, and deforestation hotspots bright orange/red with a sensitivity slider.
     - **Year Replay (2001–2024)**: Plays an animated year-by-year movie showing exactly *when* each patch of forest was cut or burned.
   - If you zoom all the way out on the flat 2D map, the map smoothly **bends and wraps itself into a 3D spinning Earth globe** (and unwraps back into a flat map when you zoom in!).

3. **"Which Trees Can Grow Here?" (Smart Tree Planting Planner)**
   - Planting the wrong tree in the wrong soil or climate kills the sapling (or harms local wildlife if the tree is invasive).
   - When you click **"Which trees can grow here?"**, the app checks **92+ real, non-invasive tree species** against **5 real environmental conditions** at your exact spot:
     1. **Water**: Long-term yearly rainfall and how many months are wet vs. dry.
     2. **Soil**: Soil acidity/alkalinity (pH) and texture (sandy, loamy, or heavy clay).
     3. **Sunlight**: Average hours of daily sunshine.
     4. **Air Quality**: Whether ozone, sulfur dioxide, or nitrogen oxide pollution exceeds safe limits for sensitive trees.
     5. **Climate & Altitude**: Average temperature, coldest winter frost nights, length of the warm growing season, and mountain elevation.
   - It badges each tree as **Native** (naturally belongs in that region), **Introduced**, or **Not recorded**, writes a **3-year planting and care guide** (best monsoon months to plant, pit size, watering, frost/browse protection), and lets you **download a complete PDF planting guide**.

4. **Live Climate Gauge & Treeko AI Mascot**
   - Displays current local temperature, humidity, wind, rain, and a local heat/drought stress score.
   - Includes **Treeko**, a friendly tree mascot chatbot whose facial expression changes based on how severe deforestation is at your chosen spot. You can ask Treeko about soil, native trees, or where to plant, and Treeko can drop GPS planting pins directly on your map (while politely blocking off-topic requests like writing homework code).

---

### Part B: River Eyes (`frontend/river-eyes/`)

*(Note: When you switch between Forest Eyes and River Eyes using the top toggle bar, an animated canvas wave with swimming fish — or rising pine trees with flying birds — transitions the screen!)*

1. **Global River Explorer (~1 Million Rivers)**
   - Draws **998,251 river segments** across the entire planet, split into switchable layers (*Major*, *Large*, *Medium*, and *Small rivers & streams*), plus 12 quick-jump famous river basins (Ganga, Yamuna, Brahmaputra, Indus, Yangtze, Mekong, Amazon, Mississippi, Nile, Congo, Danube, Rhine).
   - **Smart River Snapping**: Global river databases are slightly coarse (~450 metres), so when you zoom in close on a satellite map, lines normally cut across river bends. River Eyes automatically detects real water from satellite history and **snaps the blue river line right onto the real water channel**.
   - **Hover & Pick Mode**: Move your mouse over any river and its entire main path from mountain source to ocean lights up in yellow.

2. **Site Conditions (River Flow, Soil & Earthquakes)**
   - Click anywhere on the map to snap to the nearest river and see:
     - **River Flow**: Average historical water volume (`m³/s`), **today's live flow** compared to normal for this time of year, and the **14-day flood peak forecast** compared to a typical 2-year flood.
     - **Topsoil (0–30 cm deep)**: Soil texture (e.g., sandy loam, clay), pH, percentage of clay/silt/sand/stones, density, and organic carbon.
     - **Earthquake History**: Count and list of the strongest **Magnitude 5.0+ earthquakes** within 150 km since 1970.

3. **Satellite Water Quality (`Sentinel-2`)**
   - Click a wide river, reservoir, or lake and press **"Analyse water quality here"**.
   - The app searches the last **120 days of European Sentinel-2 satellite passes**, picks the clearest cloud-free days, isolates only true open-water pixels (ignoring clouds, shadows, and riverbanks), and calculates two scientific water-colour measurements:
     - **Turbidity (FNU)** — *How muddy, silty, or cloudy the water is* (clear water is under 10 FNU; very muddy floodwater is hundreds of FNU).
     - **Chlorophyll Index (NDCI)** — *How green/algae-rich the water is*, flagging potential algal blooms from nutrient runoff.
   - It shows the **trend across the last 3 clear satellite dates**, displays the **real true-colour satellite photo**, and asks **Gemma 4 AI** to visually inspect the photo (spotting sediment plumes, sandbars, or green coves). It also honestly lists what satellites *cannot* measure (like *E. coli* bacteria, dissolved oxygen, heavy metals, or pH), reminding users that lab water samples are still needed for drinking safety.

4. **Hydropower / Small Dam Siting Engine (3 Modes)**
   - Instead of giant mega-dams that flood whole towns and forests, River Eyes screens for **run-of-river hydropower schemes**: a modest **15-metre weir** across a narrow valley gorge that diverts part of the river water along a ~6 km pipe/tunnel to a powerhouse further downstream, generating electricity from the natural downhill drop (`Power = efficiency × water density × gravity × flow × height drop`).
   - **Mode 1 — City Power Planner**: Type a city name (e.g., *Dehradun*), pick how much electricity it needs (**50 to 3,000 MW**), and pick a search radius (**50 to 400 km**). The server scans every major river in range, checks river steepness and valley narrowness using 3D terrain tiles, checks topsoil stones/clay and nearby M5+ earthquakes, scores each candidate out of 100 across 5 criteria (*Power*, *Narrow gorge*, *Near city*, *Soil/foundation*, *Few earthquakes*), and picks a non-overlapping set of sites to power the city. You can even click **"Gemma 4 site review"** on any proposed dam card to run a satellite hazard check right on that spot!
   - **Mode 2 — River Dam Atlas**: Picks one entire river (from the dropdown or clicked on the map), traces it from mountain source to mouth, slices it into 30 km sections, and maps **every feasible hydropower site along the whole river**.
   - **Mode 3 — Single-River Stretch Screening API**: Scans ~120 km of river (60 km upstream and 60 km downstream of a point) at 1 km intervals.

5. **Aqua AI Assistant**
   - A slide-out chat drawer powered by **Gemma 4** that answers questions about hydropower math, river water quality standards (like India's CPCB classes), flood risk, seismic safety, and soil foundations for the currently selected river.

---

### Part C: Hazard Lens (`frontend/hazard/`)

- **What it does**: Click **any point on Earth** (or pick built-in presets like *Joshimath*, *Manaus*, *Uttarkashi*, or switch between **River Hazards** and **Forest Hazards** mode).
- **Behind the scenes in one click**:
  1. Grabs a **~6.5 km wide real satellite photo** of that exact spot.
  2. Measures **ground elevation and slope steepness** across a 3×3 km grid around the click.
  3. Pulls **actual rainfall from the past 7 days** plus **forecast rainfall for the next 3 days**.
  4. Traces the **nearest river up to 300 km downstream** and checks its **14-day flood forecast**.
  5. Checks the **topsoil makeup** (clay, sand, silt) and (in Forest mode) **recent natural-forest loss from 2021 to 2024**.
  6. Feeds **both the satellite photograph AND all those real numbers** to **Gemma 4** at the same time.
- **What you get**: An overall danger level (*Low*, *Moderate*, *High*, or *Very High*), specific hazard cards (e.g., *Flood*, *Landslide / Slope Failure*, *Debris Flow*, *Riverbank Erosion*, *Deforestation*, *Glacial Hazard*), **separate bullet points for what the AI saw in the photo vs. what the numbers proved**, practical safety next steps, and a **one-click PDF report download**.

---

## 3. Where Are the Datasets Taken From? (Complete Source & Organisation Links)

### Master Table of All Datasets & Source Organisations

Before diving into the details, here is a quick overview of **every dataset used by the platform** (both pre-saved offline files and live-queried scientific datasets) with direct links to the **organisations** we took them from:

| Dataset | Stored Locally or Live? | Source Organisation(s) | Official Dataset / Portal Link |
| :--- | :---: | :--- | :--- |
| **1. HydroRIVERS v1.0** (Global River Network) | Local (`hydrorivers.bin`, `hydrorivers_geom.bin`) | [WWF](https://www.worldwildlife.org/) & [McGill University](https://www.mcgill.ca/) ([HydroSHEDS](https://www.hydrosheds.org/)) | [HydroRIVERS Product Page](https://www.hydrosheds.org/products/hydrorivers) |
| **2. ISRIC SoilGrids 2.0** (India 2 km Topsoil, Global 22 km Backup & Live 250 m Soil) | Local (`soil_india.bin`, `soil_global.bin`) + Live API | [ISRIC – World Soil Information](https://www.isric.org/) | [SoilGrids Portal](https://soilgrids.org/) · [ISRIC Files](https://files.isric.org/soilgrids/latest/data_aggregated/5000m/) |
| **3. FAO ECOCROP** (92 Tree Species Growing Ranges) | Local (`tree-species.json`) | [UN Food and Agriculture Organization (FAO)](https://www.fao.org/) & [UC Davis CropModels (`Recocrop`)](https://github.com/cropmodels/Recocrop) | [FAO ECOCROP Info](https://www.fao.org/land-water/land/land-governance/land-resources-planning-toolbox/category/details/en/c/1027491/) · [Recocrop GitHub](https://github.com/cropmodels/Recocrop) |
| **4. Team Forestry Book Traits** (Extra Indian Tree Traits & Altitudes) | Local (`backend/data/tree-traits/*.json`) | [Forest Research Institute (FRI Dehradun)](https://fri.icfre.gov.in/), [CIFOR-ICRAF (World Agroforestry)](https://www.cifor-icraf.org/), [Botanical Survey of India](https://bsi.gov.in/), [CPCB India](https://cpcb.nic.in/), [IUCN](https://iucn.org/) | [Internet Archive (Troup 1921)](https://archive.org/) · [ICRAF Agroforestree](https://apps.worldagroforestry.org/treedb/) · [eFlora of India](https://efloraindia.bsi.gov.in/) · [IUCN GISD](https://www.iucngisd.org/gisd/) |
| **5. Kew WCVP** (Native vs. Introduced Plant Ranges) | Local (`tree-native.json`) | [Royal Botanic Gardens, Kew](https://www.kew.org/) (via [GBIF](https://www.gbif.org/)) | [Plants of the World Online (POWO)](https://powo.science.kew.org/) · [GBIF WCVP Dataset](https://www.gbif.org/dataset/f382f0ce-323a-4091-bb9f-add557f3a9a2) |
| **6. TDWG WGSRPD Level 3** (369 World Botanical Regions) | Local (`tdwg-level3.geojson`) | [Biodiversity Information Standards (TDWG)](https://www.tdwg.org/) | [TDWG WGSRPD GitHub](https://github.com/tdwg/wgsrpd) |
| **7. CHPclim v1** (Satellite + Rain-Gauge Annual Rainfall) | Local (`rain_chpclim.bin`) | [Climate Hazards Center (CHC), UC Santa Barbara](https://www.chc.ucsb.edu/) | [CHPclim Product Page](https://www.chc.ucsb.edu/data/chpclim) · [CHC Data Archive](https://data.chc.ucsb.edu/products/CHPclim/v1/) |
| **8. WorldClim v2.1** (Monthly Climate & Elevation Normals) | Local (`climate_worldclim.bin`) | [WorldClim / UC Davis](https://www.worldclim.org/) | [WorldClim 2.1 Data Page](https://www.worldclim.org/data/worldclim21.html) · [UC Davis GeoData](https://geodata.ucdavis.edu/climate/worldclim/2_1/base/) |
| **9. UNECE Vegetation Air-Quality Limits** (SO₂, NOₓ, Ozone AOT40) | Local rules (`backend/trees.js`) | [UNECE](https://unece.org/) & [German Environment Agency (UBA / CCE)](https://www.umweltbundesamt.de/en/Coordination-Centre-for-Effects) | [UNECE CLRTAP Mapping Manual (Ch. 3)](https://www.umweltbundesamt.de/en/manual-on-methodologies-criteria-for-modelling) |
| **10. UMD / Hansen Global Forest Change & GLAD Alerts** (2000 Cover & 2001–2024 Loss) | Live APIs + Map Tiles + Demo CSV | [World Resources Institute (WRI)](https://www.wri.org/) & [University of Maryland GLAD Lab](https://glad.umd.edu/) | [Global Forest Watch](https://www.globalforestwatch.org/) · [Global Nature Watch](https://www.globalnaturewatch.org/) |
| **11. AWS Terrain Tiles (Terrarium DEM)** & **Copernicus DEM** | Live PNG Tiles / API | [Amazon Web Services Open Data](https://opendata.aws/), [NASA SRTM](https://www.earthdata.nasa.gov/), [EU Copernicus](https://www.copernicus.eu/en) | [AWS Terrain Tiles Registry](https://registry.opendata.aws/terrain-tiles/) · [Open-Meteo Elevation](https://open-meteo.com/en/docs/elevation-api) |
| **12. JRC Global Surface Water** (1984–2021 Landsat Water Map) | Live PNG Tiles | [European Commission Joint Research Centre (JRC)](https://joint-research-centre.ec.europa.eu/) | [Global Surface Water Explorer](https://global-surface-water.appspot.com/) |
| **13. Copernicus GloFAS v4** (River Flow & 14-Day Flood Forecast) | Live API + Demo CSV | [EU Copernicus Emergency Management Service](https://emergency.copernicus.eu/) (via [Open-Meteo](https://open-meteo.com/)) | [GloFAS Official Portal](https://global-flood.emergency.copernicus.eu/) · [Open-Meteo Flood Docs](https://open-meteo.com/en/docs/flood-api) |
| **14. Copernicus ERA5, CAMS Air Quality & Open-Meteo Weather** | Live APIs | [ECMWF](https://www.ecmwf.int/), [Copernicus Climate (C3S)](https://climate.copernicus.eu/), [Copernicus Atmosphere (CAMS)](https://atmosphere.copernicus.eu/), [Open-Meteo](https://open-meteo.com/) | [Open-Meteo Historical ERA5](https://open-meteo.com/en/docs/historical-weather-api) · [Open-Meteo Air Quality](https://open-meteo.com/en/docs/air-quality-api) |
| **15. NASA POWER** (MERRA-2 & CERES Agroclimatology) | Live Backup API | [NASA Langley Research Center](https://www.nasa.gov/langley/) | [NASA POWER Portal](https://power.larc.nasa.gov/) |
| **16. ESA Copernicus Sentinel-2 L2A** (Multispectral Water Quality) | Live Satellite Data API | [European Space Agency (ESA)](https://www.esa.int/) & [Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/) | [Planetary Computer Sentinel-2 Catalog](https://planetarycomputer.microsoft.com/dataset/sentinel-2-l2a) |
| **17. USGS ComCat Earthquakes** (M5.0+ since 1970) | Live API + Demo CSV | [United States Geological Survey (USGS)](https://www.usgs.gov/) | [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) |
| **18. Esri World Imagery, CARTO Basemaps & OSM Nominatim** | Live Map Tiles & Search API | [Esri](https://www.esri.com/), [CARTO](https://carto.com/), [OpenStreetMap Foundation](https://osmfoundation.org/) | [Esri World Imagery](https://www.arcgis.com/home/item.html?id=10df2279f9684e4a9f6a7f08febac2a9) · [CARTO Basemaps](https://carto.com/basemaps) · [Nominatim Search](https://nominatim.openstreetmap.org/) |
| **19. GBIF Wild Tree Occurrence Records** (Validation) | Offline Validation API | [GBIF (Global Biodiversity Information Facility)](https://www.gbif.org/) | [GBIF Portal](https://www.gbif.org/) |

---

### Detailed Breakdown of Pre-Saved Offline Datasets (`backend/data/`)

Some scientific datasets are huge or live on slow servers that take 20–30 seconds to answer a single click. To make the app **instant (under 1 millisecond per lookup)** and **reliable even when outside servers fail**, the developers downloaded several authoritative scientific datasets ahead of time using the scripts in `backend/scripts/` and packed them into compact files inside `backend/data/`.

Here is every pre-saved dataset in the project, **the exact organisation and link it was taken from**, and **why it is used**:

### 1. HydroRIVERS v1.0 — Global River Network
- **Files in repo**: `backend/data/hydrorivers.bin` & `backend/data/hydrorivers_geom.bin` (built by `backend/scripts/build-rivers.py`)
- **Source Organisation**: **[HydroSHEDS](https://www.hydrosheds.org/)** — a partnership led by the **[World Wildlife Fund (WWF)](https://www.worldwildlife.org/)** and **[McGill University](https://www.mcgill.ca/)** (*Lehner & Grill, 2013*).
- **Dataset Link**: https://www.hydrosheds.org/products/hydrorivers
- **What is inside**: **998,251 river segments** across the entire world — specifically every river worldwide that drains at least `1,000 km²` of land (905,279 segments), **plus** extra-fine detail across the India region down to small streams draining just `100 km²` (92,972 extra segments). For each piece of river, it stores its GPS path, length, upstream drainage area, average water flow (`m³/s`), and which river segment it flows into next (`NEXT_DOWN`).
- **Why it is used**:
  - Draws the interactive river map in River Eyes without needing an external map server.
  - Lets the app trace any river upstream to its source or downstream to the sea in milliseconds.
  - Provides the long-term average water flow (`Q`) needed to calculate how many megawatts of electricity a hydropower site can produce.

### 2. ISRIC SoilGrids 2.0 — High-Speed India Topsoil Grid
- **File in repo**: `backend/data/soil_india.bin` (built by `backend/scripts/build-soil-india.py`)
- **Source Organisation**: **[ISRIC – World Soil Information](https://www.isric.org/)** (International Soil Reference and Information Centre, Wageningen, Netherlands — *Poggio et al., 2021*, CC BY 4.0).
- **Dataset Links**: [SoilGrids Interactive Portal](https://soilgrids.org/) · [ISRIC SoilGrids Overview](https://www.isric.org/explore/soilgrids) · Direct Web Coverage Service used: https://maps.isric.org/
- **What is inside**: A ~2 km (`0.02°`) grid covering the entire Indian subcontinent (`68°E–97.5°E, 6.5°N–37.5°N`), storing the top **0–30 cm of soil**: clay %, sand %, silt %, stones/coarse fragments %, bulk density, organic carbon, and soil pH.
- **Why it is used**:
  - ISRIC's live SoilGrids internet API often takes **20 to 30 seconds** per click (or times out completely). This local file answers in **less than 1 millisecond**.
  - Even better: SoilGrids masks out cities and open river water as "no data." When someone clicks a town or a river in India, this local grid automatically spirals outward up to **5–8 km** to grab the real surrounding soil instead of failing!

### 3. ISRIC SoilGrids 2.0 — Global Backup Topsoil Grid
- **File in repo**: `backend/data/soil_global.bin` (built by `backend/scripts/build-soil-global.py`)
- **Source Organisation**: **[ISRIC – World Soil Information](https://www.isric.org/)** (*Poggio et al., 2021*, CC BY 4.0).
- **Dataset Links**: [SoilGrids Portal](https://soilgrids.org/) · Direct 5 km GeoTIFF archive used: https://files.isric.org/soilgrids/latest/data_aggregated/5000m/
- **What is inside**: A compact `0.2°` (~22 km) worldwide grid of topsoil clay, sand, silt, organic carbon, and pH across all land between `60°S` and `84°N`.
- **Why it is used**: Acts as an instant offline safety net **outside India** whenever the live SoilGrids REST API times out or lands on a masked-out city/water pixel (used by both the Tree Planner and Hazard Lens).

### 4. FAO ECOCROP — Tree Growing Requirements Database
- **File in repo**: `backend/data/tree-species.json` (built by `backend/scripts/build-tree-species.py`)
- **Source Organisations**: **[Food and Agriculture Organization of the United Nations (FAO)](https://www.fao.org/)** (original ECOCROP database), packaged and maintained by **Prof. Robert Hijmans ([UC Davis](https://www.ucdavis.edu/))** in the open-source `Recocrop` R package (GPL-3).
- **Dataset Links**: [FAO ECOCROP Information Page](https://www.fao.org/land-water/land/land-governance/land-resources-planning-toolbox/category/details/en/c/1027491/) · [Recocrop GitHub Repository (`ecocrop.rds`)](https://github.com/cropmodels/Recocrop)
- **What is inside**: **92 carefully selected, non-invasive tree species** (with strong coverage of Indian trees like Neem, Banyan, Sal, Teak, Deodar, Khejri, Mango, Arjun, plus major global forest trees). For every tree, it lists optimal and survival limits for **temperature, frost killing point, growing-season days, yearly rainfall, soil pH, soil texture, drainage, and sunlight**.
- **Why it is used**: Powers the **"Which trees can grow here?"** planner in Forest Eyes. Importantly, notorious **invasive trees** (like *Prosopis juliflora*, *Leucaena*, *Lantana*, and *Eucalyptus*) were **deliberately filtered out** so the app never recommends trees that destroy native ecosystems.

### 5. Team-Curated Forestry Book Records (Extra Tree Traits)
- **Folder in repo**: `backend/data/tree-traits/*.json` (loaded by `backend/tree-traits.js`, checked by `backend/scripts/check-tree-traits.js`)
- **Source Organisations & Official Reference Links**:
  - **Oxford Clarendon Press / [Forest Research Institute (FRI), Dehradun](https://fri.icfre.gov.in/)** — R.S. Troup's *The Silviculture of Indian Trees* (1921 vols. 1–3 digitized on [Internet Archive](https://archive.org/)).
  - **[CIFOR-ICRAF (World Agroforestry Centre)](https://www.cifor-icraf.org/)** — [Agroforestree Database v4.0](https://apps.worldagroforestry.org/treedb/).
  - **[Botanical Survey of India (BSI)](https://bsi.gov.in/)** — [eFlora of India](https://efloraindia.bsi.gov.in/).
  - **[Royal Botanic Gardens, Kew](https://www.kew.org/)** — [Plants of the World Online (POWO)](https://powo.science.kew.org/) for accepted botanical names & families.
  - **[Central Pollution Control Board (CPCB), Ministry of Environment, India](https://cpcb.nic.in/)** — *Guidelines for Developing Green Belts* (`PROBES/75/1999-2000`).
  - **[IUCN (International Union for Conservation of Nature)](https://iucn.org/)** & **[CABI](https://www.cabi.org/)** — [Global Invasive Species Database](https://www.iucngisd.org/gisd/) & [CABI Compendium](https://www.cabidigitallibrary.org/product/qi).
- **What is inside**: Extra species missing from FAO ECOCROP (such as Peepal, Jamun, Semal, Shisham, Burans) or missing traits like **maximum mountain altitude (`elevMaxM`)**.
- **Why it is used**: Fills gaps in FAO's database for Indian forestry while enforcing a strict anti-hallucination rule: every file is rejected unless it contains an **exact verbatim quote from the book/source** and the name of the human who verified it (`checkedBy`).

### 6. Royal Botanic Gardens, Kew — Native Plant Distributions (WCVP)
- **File in repo**: `backend/data/tree-native.json` (built by `backend/scripts/build-native-ranges.js`)
- **Source Organisations**: **[Royal Botanic Gardens, Kew](https://www.kew.org/)** (*World Checklist of Vascular Plants — Govaerts et al., 2021*, CC BY 4.0), accessed programmatically through **[GBIF (Global Biodiversity Information Facility)](https://www.gbif.org/)**.
- **Dataset Links**: [Kew Plants of the World Online (WCVP Portal)](https://powo.science.kew.org/) · [Exact GBIF WCVP Dataset Page (`f382f0ce-323a-4091-bb9f-add557f3a9a2`)](https://www.gbif.org/dataset/f382f0ce-323a-4091-bb9f-add557f3a9a2)
- **What is inside**: Lists every botanical region in the world where each of the 92+ tree species is **naturally native** versus **introduced by humans**.
- **Why it is used**: So the Tree Planner always **ranks native local trees ahead of foreign/exotic trees**, helping restore natural biodiversity rather than just planting anything green.

### 7. TDWG World Botanical Regions Map (WGSRPD Level 3)
- **File in repo**: `backend/data/tdwg-level3.geojson` (read by `backend/tdwg.js`)
- **Source Organisation**: **[Biodiversity Information Standards (TDWG)](https://www.tdwg.org/)** — *World Geographical Scheme for Recording Plant Distributions*.
- **Dataset Link**: https://github.com/tdwg/wgsrpd
- **What is inside**: **369 geographical polygon shapes** dividing the world into standard botanical zones (for example, *West Himalaya*, *East Himalaya*, *Assam*, *India*, *Nepal*, *Brazil North*).
- **Why it is used**: Translates whatever `(latitude, longitude)` you click on the map into Kew's botanical region code so `tree-native.json` knows whether a tree is native right there.

### 8. CHPclim v1 — Satellite + Rain-Gauge Annual Rainfall Map
- **File in repo**: `backend/data/rain_chpclim.bin` (built by `backend/scripts/build-rainfall.py`, read by `backend/rainfall.js`)
- **Source Organisation**: **[Climate Hazards Center (CHC), University of California, Santa Barbara (UCSB)](https://www.chc.ucsb.edu/)** (*Funk et al., 2015*).
- **Dataset Links**: [CHC CHPclim Overview](https://www.chc.ucsb.edu/data/chpclim) · Direct GeoTIFF Archive used: https://data.chc.ucsb.edu/products/CHPclim/v1/
- **What is inside**: Long-term average annual rainfall (`mm/year`) combining real ground rain gauges, satellite infrared readings, and mountain slope effects — stored at **~5.5 km (`0.05°`) resolution in India** and **~11 km (`0.1°`) globally**.
- **Why it is used**: Standard weather-model rainfall (like ERA5) makes massive errors in mountain valleys! For example, ERA5 claims **Srinagar** gets `2,673 mm` of rain a year (like a rainforest!), when Srinagar actually gets only `~699 mm` because mountains block the monsoon. CHPclim gets Srinagar (`699 mm`), Leh (`151 mm`), Jodhpur (`332 mm`), and Cherrapunji (`6,401 mm`) right, preventing the Tree Planner from recommending rainforest trees in dry valleys.

### 9. WorldClim v2.1 — Monthly Climate & Elevation Normals
- **File in repo**: `backend/data/climate_worldclim.bin` (built by `backend/scripts/build-climate-worldclim.py`, read by `backend/worldclim.js`)
- **Source Organisation**: **[WorldClim](https://www.worldclim.org/)** / **[University of California, Davis (UC Davis)](https://www.ucdavis.edu/)** (*Fick & Hijmans, 2017*, CC BY 4.0).
- **Dataset Links**: [WorldClim 2.1 Historical Climate Page](https://www.worldclim.org/data/worldclim21.html) · Direct UC Davis Download Server: https://geodata.ucdavis.edu/climate/worldclim/2_1/base/
- **What is inside**: 30-year (`1970–2000`) monthly averages (January through December) for **average temperature, coldest temperature, solar radiation (sunshine), rainfall, and ground elevation** (`10 arc-minute / ~18 km` resolution in India + global elevation, `0.5° / ~55 km` globally).
- **Why it is used**:
  1. Tells the Tree Planner **which months of the year are the wet monsoon season vs. dry months** so it recommends the right planting window (e.g., *"Plant at the start of the June–September wet season"*).
  2. Serves as the **unbreakable offline backup** for temperature and sunlight if both live climate APIs (Open-Meteo and NASA POWER) are offline or rate-limited, automatically adjusting temperature by `6.5 °C` per `1,000 m` of altitude difference.

### 10. UNECE Air-Quality Rules & Demo CSV Snapshots
- **UNECE Vegetation Pollution Thresholds** (hardcoded in `backend/trees.js`):
  - **Source Organisations**: **[United Nations Economic Commission for Europe (UNECE)](https://unece.org/environment-policy/air)** & the **[Coordination Centre for Effects (CCE) at the German Environment Agency (Umweltbundesamt)](https://www.umweltbundesamt.de/en/Coordination-Centre-for-Effects)**.
  - **Source Manual Link**: [UNECE CLRTAP *Manual on Methodologies and Criteria for Modelling and Mapping Critical Loads and Levels* (2024, Chapter 3)](https://www.umweltbundesamt.de/en/manual-on-methodologies-criteria-for-modelling) — sets safe plant limits for Sulfur Dioxide (`20 µg/m³`), Nitrogen Oxides (`30 µg/m³`), and ground-level Ozone (`AOT40 = 5 ppm·h`).
- **Demo CSV Snapshots** (`backend/datasets/*.csv`, built by `backend/scripts/export-datasets.js`):
  - **Source Organisations**: Sampled directly from **[HydroSHEDS](https://www.hydrosheds.org/)**, **[Global Nature Watch](https://www.globalnaturewatch.org/) / [UMD GLAD](https://glad.umd.edu/)**, **[EU Copernicus GloFAS](https://global-flood.emergency.copernicus.eu/)** (via **[Open-Meteo](https://open-meteo.com/)**), and **[USGS Earthquake Hazards Program](https://earthquake.usgs.gov/)**.
  - **Why it is used**: Small human-readable CSV tables (`places.csv`, `hydrorivers_reaches_demo.csv`, `tree_cover_loss_demo.csv`, `river_discharge_glofas_demo.csv`, `earthquakes_usgs_demo.csv`) capturing real numbers for 6 famous test locations (*Joshimath*, *Uttarkashi*, *Manaus*, *Cologne*, *Derna*, *Sindh*) so anyone inspecting the repo can open them in Excel and see what the raw data looks like.

---

## 4. Every Live API Used & Why It Is Used

On top of the fast local files above, the application talks to **live external APIs** to get up-to-date satellite photos, daily weather, flood forecasts, deforestation alerts, air pollution, earthquakes, and AI reasoning.

Almost every scientific API below is **100% free and requires no API key at all**! Only the AI host (Google AI Studio or AWS Bedrock) and the primary Global Forest Watch proxy use an API key (and even Global Forest Watch has an automatic keyless backup).

---

### A. AI & Vision APIs (The "Explainers")

| API / Service | Source Organisation & Official Link | API Endpoint Used | Needs Key? | Where & Why It Is Used |
| :--- | :--- | :--- | :---: | :--- |
| **1. Amazon Bedrock Converse API** | **[Amazon Web Services (AWS)](https://aws.amazon.com/bedrock/)** | `https://bedrock-runtime.<region>.amazonaws.com/model/<id>/converse` | Optional (`BEDROCK_API_KEY` + `BEDROCK_MODEL_ID`) | **Primary cloud AI host for Gemma 4** (`backend/gemma.js`) when deployed on AWS. Reads satellite photos and writes structured reports for Hazard Lens, Water Quality, Tree Planner, Treeko, and Aqua. |
| **2. Google AI Studio (Generative Language API)** | **[Google DeepMind / Google AI](https://ai.google.dev/)** ([Google AI Studio](https://aistudio.google.com/)) | `https://generativelanguage.googleapis.com/v1beta/models/<model>:generateContent` | Yes (`GEMINI_API_KEY`) | **Default host for [Gemma 4](https://deepmind.google/models/gemma/)** (`gemma-4-26b-a4b-it` and `gemma-4-31b-it`). If the first Gemma model takes longer than 15 seconds, the server automatically starts a "hedged" backup call to the second Gemma model so you don't wait. If both Gemma models are overloaded or hit a repetition loop, it automatically falls back to **Gemini Flash** (`gemini-2.5-flash` / `gemini-2.5-flash-lite`). And if *all* AI is unavailable, every feature falls back to transparent rule-based logic! |

---

### B. Forest & Deforestation APIs

| API / Service | Source Organisation & Official Link | API Endpoint Used | Needs Key? | Where & Why It Is Used |
| :--- | :--- | :--- | :---: | :--- |
| **3. Global Forest Watch (GFW) Data API & Resource Watch Geostore** | **[World Resources Institute (WRI)](https://www.wri.org/)**, **[Global Forest Watch](https://www.globalforestwatch.org/)**, **[Resource Watch](https://resourcewatch.org/)** & **[University of Maryland GLAD Lab](https://glad.umd.edu/)** | `https://api.resourcewatch.org/geostore/` & `https://data-api.globalforestwatch.org/` | Optional (`GFW_API_KEY`, proxied via `/api/gfw/*`) | Used in **Forest Eyes** (`backend/forest-api.js`, `frontend/forest-eyes/app.js`) to register your circular search area and query **official 30-metre Landsat satellite tree-cover loss (2001–2024)**, **CO₂ emissions**, and **recent GLAD deforestation alerts**. |
| **4. Global Nature Watch (GNW) Analytics API** | **[Global Nature Watch](https://www.globalnaturewatch.org/)** / **[WRI](https://www.wri.org/)** | `https://analytics.globalnaturewatch.org/v0/land_change/tree_cover_loss/analytics` | **No (Keyless!)** | **Two jobs:** (1) Automatic **keyless backup** in Forest Eyes if `GFW_API_KEY` is missing or GFW fails, so deforestation stats still load out-of-the-box! (2) Used in **Hazard Lens** (`backend/hazard.js`) to measure **2021–2024 natural-forest loss** inside a 5 km radius. |
| **5. Global Forest Watch Map Tiles (Tree Cover 2000, Tree Loss & GLAD Alerts)** | **[Global Forest Watch (WRI)](https://www.globalforestwatch.org/)** & **[UMD Global Forest Change (Hansen et al.)](https://glad.earthengine.app/view/global-forest-change)** | `https://tiles.globalforestwatch.org/` | **No (Keyless!)** | **Three clever uses:** (1) Drawn as visual layers on the 2D map and 3D Earth globe. (2) **Counted pixel-by-pixel on the backend** (`backend/forest-extent.js`) to calculate exact **year-2000 baseline forest hectares** inside your circle without needing an API key. (3) **Decoded pixel-by-pixel in a browser Web Worker** (`loss-replay.js`, via `/tiles/gfw-loss/...` CORS proxy) because each PNG pixel's blue channel secretly encodes the exact year (`2001–2024`) that tree patch was lost — powering the **Year Replay movie**! |

---

### C. 3D Terrain Elevation & Satellite Water-Alignment Tiles

| API / Service | Source Organisation & Official Link | API Endpoint Used | Needs Key? | Where & Why It Is Used |
| :--- | :--- | :--- | :---: | :--- |
| **6. AWS Open Data Terrain Tiles (Terrarium DEM)** | **[AWS Open Data Registry](https://registry.opendata.aws/terrain-tiles/)** / **[Tilezen (Mapzen)](https://github.com/tilezen/joerd)** (compiled from **[NASA SRTM](https://www.earthdata.nasa.gov/)**, **[USGS GMTED](https://www.usgs.gov/)** & **[NOAA ETOPO1](https://www.ncei.noaa.gov/)**) | `https://s3.amazonaws.com/elevation-tiles-prod/terrarium/{z}/{x}/{y}.png` | **No (Keyless & unthrottled!)** | Decoded by a custom zero-dependency PNG reader in `backend/elevation.js` (`Elevation = R×256 + G + B/256 − 32768`). Used at **~38 m detail (zoom 12)** and **~300 m detail (zoom 9)** to: (1) measure **riverbed height drop and gorge width** for dam siting (`dam.js`), (2) measure **hillslope steepness** for landslide risk (`hazard.js`), and (3) check **site altitude** so lowland trees aren't planted on high mountains (`trees.js`). |
| **7. JRC Global Surface Water Occurrence Tiles** | **[European Commission Joint Research Centre (JRC)](https://joint-research-centre.ec.europa.eu/)** ([Global Surface Water Explorer](https://global-surface-water.appspot.com/), *Pekel et al., 2016*) | `https://storage.googleapis.com/global-surface-water/tiles2021/occurrence/{z}/{x}/{y}.png` | **No (Keyless!)** | Read by `backend/water-snap.js` when zoomed in (`zoom ≥ 9`) in River Eyes. Each pixel tells how often water was seen there by Landsat satellites between 1984 and 2021 (`0–100%`). Used to **pull coarse river lines sideways onto the real permanent water channel** visible on satellite imagery. |

---

### D. Weather, Floods, Climate & Air Pollution APIs

| API / Service | Source Organisation & Official Link | API Endpoint Used | Needs Key? | Where & Why It Is Used |
| :--- | :--- | :--- | :---: | :--- |
| **8. Open-Meteo Weather Forecast API** | **[Open-Meteo](https://open-meteo.com/)** (aggregating national weather services like NOAA, DWD, ECMWF) | `https://api.open-meteo.com/v1/forecast` | **No (Keyless!)** | Fetches **live current temperature, humidity, rain, and wind** for the climate widget in Forest Eyes (`app.js`), and fetches **last 7 days of actual rain + next 3 days of forecast rain** for flood/landslide checks in Hazard Lens (`hazard.js`). |
| **9. Open-Meteo Flood API (Copernicus GloFAS v4)** | **[EU Copernicus Emergency Management Service (GloFAS)](https://global-flood.emergency.copernicus.eu/)** & **[Open-Meteo](https://open-meteo.com/en/docs/flood-api)** | `https://flood-api.open-meteo.com/v1/flood` | **No (Keyless!)** | Provides **3 years of daily river water flow (`m³/s`) plus a 14-day flood forecast** (`site.js`, `hazard.js`). Used to tell if today's river is running dry, normal, or heading toward a dangerous flood peak compared to a 2-year flood. *(Smart detail: the server checks adjacent 5 km grid cells and matches them against HydroRIVERS so it doesn't accidentally read a tiny side creek instead of the main river!)* |
| **10. Open-Meteo Historical Weather Archive (ERA5)** | **[EU Copernicus Climate Change Service (C3S)](https://climate.copernicus.eu/)** / **[ECMWF](https://www.ecmwf.int/)** via **[Open-Meteo](https://open-meteo.com/en/docs/historical-weather-api)** | `https://archive-api.open-meteo.com/v1/archive` | **No (Keyless!)** | Primary climate source for the **Tree Planner** (`backend/trees.js`). Pulls **5 full years (`1,825+ days`)** of daily average temperature, winter cold snaps, and solar radiation (converted into daily sunshine hours using the UN FAO-56 sunshine formula) to test which trees can survive there. |
| **11. Open-Meteo Air Quality API (CAMS Global)** | **[EU Copernicus Atmosphere Monitoring Service (CAMS)](https://atmosphere.copernicus.eu/)** via **[Open-Meteo](https://open-meteo.com/en/docs/air-quality-api)** | `https://air-quality-api.open-meteo.com/v1/air-quality` | **No (Keyless!)** | Pulls **365 days (`8,760 hours`)** of hourly ground-level **Ozone (`O₃`), Sulfur Dioxide (`SO₂`), Nitrogen Dioxide (`NO₂`), Nitrogen Monoxide (`NO`), and `PM2.5`** for the Tree Planner (`trees.js`) to check if air pollution breaches UNECE vegetation safety limits. |
| **12. Open-Meteo Elevation API (Copernicus DEM 90 m)** | **[EU Copernicus DEM (GLO-90)](https://spacedata.copernicus.eu/collections/copernicus-digital-elevation-model)** via **[Open-Meteo](https://open-meteo.com/en/docs/elevation-api)** | `https://api.open-meteo.com/v1/elevation` | **No (Keyless!)** | **Backup elevation source** (`dam.js`, `hazard.js`) automatically used if AWS Terrain Tiles ever fail to download. |
| **13. NASA POWER Agroclimatology API** | **[NASA Langley Research Center — POWER Project](https://power.larc.nasa.gov/)** (MERRA-2 + CERES satellite radiation) | `https://power.larc.nasa.gov/api/temporal/daily/point` | **No (Keyless!)** | **Backup #1 for 5-year temperature & sunshine** in the Tree Planner (`trees.js`) whenever Open-Meteo ERA5 times out or rate-limits. Includes an automatic `6.5 °C/km` mountain altitude temperature correction. |

---

### E. Multispectral Satellites, Live Soil, Earthquakes & Map Search APIs

| API / Service | Source Organisation & Official Link | API Endpoint Used | Needs Key? | Where & Why It Is Used |
| :--- | :--- | :--- | :---: | :--- |
| **14. Microsoft Planetary Computer (STAC + Data API for ESA Sentinel-2 L2A)** | **[Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/)** & **[European Space Agency (ESA Copernicus Sentinel-2)](https://www.esa.int/Applications/Observing_the_Earth/Copernicus/Sentinel-2)** | `https://planetarycomputer.microsoft.com/api/stac/v1/search` & `/api/data/v1/item/...` | **No (Keyless!)** | Powers **Satellite Water Quality** in River Eyes (`backend/water.js`). Searches the newest Sentinel-2 satellite passes (`10 m – 20 m` resolution) and downloads raw scientific reflectance numbers (`.npy` arrays for Green `B03`, Red `B04`, Red-Edge `B05`, Near-Infrared `B08`, and Scene Classification `SCL`) plus a true-colour PNG photo to calculate **water muddiness (Dogliotti 2015 Turbidity)** and **algae levels (Mishra & Mishra 2012 NDCI)**. |
| **15. ISRIC SoilGrids v2.0 Live REST API** | **[ISRIC – World Soil Information](https://www.isric.org/)** ([SoilGrids](https://soilgrids.org/)) | `https://rest.isric.org/soilgrids/v2.0/properties/query` & `/classification/query` | **No (Keyless!)** | Fetches **250-metre resolution live topsoil data** and **UN World Reference Base (WRB) soil group names** (e.g., *Cambisols*, *Ferralsols*) for locations **outside India** in River Eyes (`site.js`), Forest Eyes (`trees.js`), and Treeko's standalone browser brain (`treeko-brain.js`). |
| **16. USGS Earthquake Hazard Program API (ComCat)** | **[United States Geological Survey (USGS)](https://www.usgs.gov/programs/earthquake-hazards)** ([Earthquake Catalog](https://earthquake.usgs.gov/)) | `https://earthquake.usgs.gov/fdsnws/event/1/query` & `/count` | **No (Keyless!)** | Queries every recorded **Magnitude 5.0+ earthquake since 1970** within **150 km** of a clicked site (`site.js`) or within **50 km** of every candidate hydropower dam (`dam.js`), penalizing dam sites in active earthquake zones. |
| **17. Esri ArcGIS World Imagery, Dark Gray Canvas & Place Labels** | **[Esri](https://www.esri.com/)** ([ArcGIS Living Atlas](https://livingatlas.arcgis.com/), imagery from Maxar, Earthstar Geographics & GIS Community) | `https://server.arcgisonline.com/ArcGIS/rest/services/...` | **No (Keyless!)** | **Three jobs:** (1) High-resolution **satellite background map** across all 3 tools (and projected onto the 3D spinning Earth in `globe-imagery.js`). (2) Dark map background in River Eyes. (3) Exports the **768×768 JPEG satellite snapshot** (~6.5 km wide) that **Gemma 4 visually inspects** in Hazard Lens (`hazard.js`). |
| **18. CARTO Dark Matter Basemap Tiles** | **[CARTO](https://carto.com/basemaps)** (built on **[OpenStreetMap](https://www.openstreetmap.org/)** data) | `https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png` | **No (Keyless!)** | Clean dark-mode background map in **Forest Eyes** and **Hazard Lens** when satellite imagery is toggled off so glowing deforestation dots and hazard circles stand out clearly. |
| **19. OpenStreetMap Nominatim Geocoding API** | **[OpenStreetMap Foundation](https://osmfoundation.org/)** ([Nominatim](https://nominatim.org/)) | `https://nominatim.openstreetmap.org/search` | **No (Keyless!)** | Powers the **location and city search bars** across Forest Eyes, River Eyes (City Power Planner), and Hazard Lens — turning typed names like *"Dehradun"*, *"Coorg"*, or *"Amazonas"* into GPS coordinates with autocomplete suggestions. |
| **20. GBIF (Global Biodiversity Information Facility) API** | **[GBIF Secretariat](https://www.gbif.org/)** | `https://api.gbif.org/v1/` | **No (Keyless!)** | Used offline by the developer scripts (`build-native-ranges.js` and `validate-tree-fit.js`) to download Kew's native plant region checklists and **test the Tree Planner's accuracy against thousands of real-world wild tree sightings** (achieving ~87% accuracy on tuned species and ~76% on held-out test species). |

*(Note on UI libraries & minor code details: The browser frontend loads **Leaflet 1.9.4** for 2D maps, **Three.js 0.160.0** + a NASA Earth backup texture from `threejs.org` for the 3D globe, **jsPDF + AutoTable** from `cdnjs` when you click "Download PDF", and **Google Fonts** for typography. There is also a `CLIMATE_WATCH` URL constant defined in `frontend/forest-eyes/app.js`, though it is not actively called.)*

---

## 5. How Fallbacks Keep the App Running Smoothly (At a Glance)

One of the smartest parts of this codebase is that **almost nothing is a single point of failure**. If a free public server is slow or down, the app automatically switches to a backup:

- **AI Brain**: `Amazon Bedrock (Gemma 4)` → `Google AI Studio (Gemma 4 26B, hedged at 15 s with Gemma 4 31B)` → `Gemini Flash backup models` → `Built-in math & rule-based reports`.
- **Deforestation Numbers**: `Global Forest Watch API (with key)` → `Global Nature Watch Analytics API (keyless)` + `Direct pixel counting of GFW Year-2000 tree-cover PNG tiles`.
- **Climate for Tree Planting**: `Open-Meteo ERA5 (5 years daily)` → `NASA POWER satellite climate (5 years daily)` → `Local WorldClim 2.1 binary file (instant offline)`.
- **Rainfall for Tree Planting**: `Local CHPclim satellite + rain-gauge binary file (instant & accurate in mountains)` → `Live reanalysis rainfall only over tiny islands missing from CHPclim`.
- **Soil Properties**: `Local 2 km India binary grid (<1 ms, spirals out from towns/water)` → `Live ISRIC SoilGrids 250 m REST API` → `Local 22 km Global binary grid (<1 ms)`.
- **3D Ground Elevation**: `AWS Open Data Terrarium PNG Tiles (~38 m, keyless)` → `Open-Meteo Copernicus 90 m Elevation API` → `Local WorldClim 10-arc-minute elevation`.

---

## 6. Quick Folder Map of the Codebase

- **`frontend/index.html` & `frontend/transition.js`** — Main shell with the top bar switching between **Forest Eyes**, **River Eyes**, and **Hazard Lens**, plus the water/fish and pine-tree/bird transition animations.
- **`frontend/forest-eyes/`** — Everything for **Forest Eyes**: 2D map & deforestation stats (`app.js`), 3D globe & smooth map-to-globe bending (`globe-*.js`), GPU/SVG heatmap (`heatmap.js`), 2001–2024 replay worker (`loss-replay.js`), 5-check Tree Planner & PDF generator (`tree-fit.js`, `tree-planner.js`), and Treeko chatbot (`treeko*.js`, `ai-guard.js`).
- **`frontend/river-eyes/index.html`** — Everything for **River Eyes**: global river layers, river hover/pick mode, City Power Planner UI, River Dam Atlas UI, Site Conditions UI, Sentinel-2 Water Quality UI, and Aqua chatbot drawer.
- **`frontend/hazard/index.html`** — Everything for **Hazard Lens**: satellite + live telemetry hazard inspector and PDF report builder.
- **`backend/server.js`** — Zero-dependency Node.js web server (uses only built-in Node 22 features — zero `npm install` needed to run!) connecting all API routes (`/api/v1/*`, `/api/gfw/*`, `/tiles/gfw-loss/*`).
- **`backend/*.js`** — Individual scientific & AI engines: `gemma.js` (AI), `rivers.js` & `water-snap.js` (rivers), `dam.js` (hydropower siting), `water.js` (Sentinel-2 water quality), `site.js` (flow + soil + quakes), `hazard.js` (Hazard Lens), `trees.js` & `tree-traits.js` & `tdwg.js` (Tree Planner), `forest-api.js` & `forest-extent.js` (GFW), `elevation.js`, `soil.js`, `rainfall.js`, `worldclim.js`.
- **`backend/data/` & `backend/scripts/`** — All pre-built fast `.bin` / `.json` / `.geojson` datasets and the Python/Node scripts that download and build them from raw scientific sources.

