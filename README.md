# subject-day-night-map

Maps the movement tracks of every subject in an EarthRanger subject group, colouring each track segment by whether it was travelled by day (amber) or by night (navy). Night is worked out from the sun's position at each location fix, so it follows the real sunset and sunrise at that place and date.

Full documentation: [docs/index.html](docs/index.html) (User Guide, Technical Guide and Troubleshooting).

## What it does

| Step | What it does |
|---|---|
| **Set Workflow Details** | Name and description shown on the dashboard. |
| **Set Time Range** | The period to map. |
| **Connect to EarthRanger** | The EarthRanger site to read from. |
| **Set Groupers** | How the maps are split: one map per subject, per time period or per spatial region. Leave empty for one map. |
| **Basemap URLs** | Tile layers drawn under the tracks. |
| **Subject Group** | Fetches the location fixes of the subject group picked from the drop-down, for the time range. |
| **Process Relocations** | Cleans the fixes and drops known bad coordinates (0,0 / 1,1 / 180,90). |
| **Classify Is Night** | Flags each fix as night when the sun is below the horizon at its location and time. |
| **Persist Relocations** | Saves the classified fixes as `relocations.geoparquet`. |
| **Relocations To Trajectory** | Joins the fixes into track segments, dropping segments outside the Trajectory Segment Filter limits. |
| **Persist Trajectories** | Saves the track segments as `trajectories.geoparquet`. |
| **Map Columns** | Renames the subject and day/night columns (e.g. `subject_name`, `is_night`) and drops unused patrol columns. |
| **Sort Values** | Orders day segments before night ones, so night segments are drawn on top. |
| **Apply Color Map** | Colours day segments amber (`#e7a553`) and night segments navy (`#292965`). |
| **Split Groups** | One table of segments per group from Set Groupers. |
| **Create Path Layer** | Draws each group's segments in their day/night colour, with a Day/Night legend and a tooltip. |
| **Compute Fitted View State** | Zooms each map to its tracks. |
| **Pair up geo_layers, view_state by group** | Pairs each group's path layer with its view. |
| **Draw Map** | Draws each map over the base maps. |
| **Persist Day Night Map** | Saves each map as an HTML file. |
| **Create Map Widget Single View** | Wraps each map as a **Day Night Tracks** widget. |
| **Merge Widget Views** | Combines the per-group maps into one Day Night Tracks widget, so the dashboard filter switches between them. |
| **Gather Dashboard** | Collects the map widget into the dashboard. |

## What it produces

| Output | Description |
|---|---|
| `relocations.geoparquet` | The cleaned location fixes, each with an `is_night` flag. |
| `trajectories.geoparquet` | The track segments built from those fixes. |
| One HTML map per group | The day/night track map, as shown on the dashboard. |
| Dashboard | A **Day Night Tracks** map widget; with groupers set, the dashboard filter switches between groups. |

## Requirements

- Access to an **EarthRanger** data source, set up as a connection

---

## 1. Load the Workflow

In the workflow runner, add a new template using this repository's URL. Once added, select
**subject-day-night-map** from the available templates list to load it.

---

## 2. Configure the Workflow

You'll be prompted to fill in the following parameters before running. Fields marked *Advanced* are collapsed
and can be left at their defaults.

### Set Workflow Details

| Field | Description | Default |
|---|---|---|
| Workflow Name *(required)* | A name for this run. | — |
| Workflow Description | Optional notes about this run. | — |

### Set Time Range

| Field | Description | Default |
|---|---|---|
| Since *(required)* | The start of the period to map. | — |
| Until *(required)* | The end of the period to map. | — |
| Timezone | Timezone the times are given in. | — |

### Connect to EarthRanger

| Field | Description | Default |
|---|---|---|
| Data source *(required)* | Select one of your configured EarthRanger data sources. Choose it first: the Subject Group drop-down lists this data source's groups. | — |

### Set Groupers

| Field | Description | Default |
|---|---|---|
| Group by | Optional. Add a grouper to get one map per group: **Category** (only *Subject Name*, one map per subject), **Temporal** (e.g. one map per month) or **Spatial** (one map per spatial region). Leave empty for a single map of all subjects. | — |

### Basemap URLs

| Field | Description | Default |
|---|---|---|
| Base maps | Tile layers to draw under the tracks. The first layer in the list is the bottommost. Advanced. | Esri World Topo Map, with Esri World Imagery at 50% opacity |

### Subject Group

| Field | Description | Default |
|---|---|---|
| Subject Group Name *(required)* | The EarthRanger subject group to map, picked from a drop-down of the chosen data source's subject groups. ⚠️ A group with mixed subtypes could give unexpected results. | — |

### Relocations To Trajectory

| Field | Description | Default |
|---|---|---|
| Trajectory Segment Filter | Limits on segment length, duration and speed; segments outside them are dropped. Advanced. | length 0.001 m – 100 km, time 1 s – 48 h, speed 0.01 – 500 km/h |

---

## 3. Results

The workflow writes its files to the results folder the runner gives it (`ECOSCOPE_WORKFLOWS_RESULTS`) and
shows its dashboard when it finishes. Day segments are amber, night segments navy.

---

## Developing

The run form is set in `spec.yaml`:

- `rjsf-overrides` turns **Subject Group Name** into a drop-down: the `EarthRangerEnumResolver` transform fills it with the subject groups of the data source picked under **Connect to EarthRanger** (`depends_on: properties.er_connection.properties.data_source.properties.name`).
- `rjsf-overrides` `$defs` limits the **Category** grouper to *Subject Name* (`ValueGrouper.properties.index_name.oneOf`), the only subject column the maps are split on.
- The steps are not in task groups, so each form override is keyed by its step id (e.g. `subjectgroup_observations.properties.subject_group_name`).
- The **Spatial** grouper's *Spatial Regions* is still a text field. To make it a drop-down of the site's spatial feature groups, add an `EarthRangerEnumResolver` transform with `type: spatial_feature_group` on `groupers.properties.groupers`.

```bash
# recompile spec.yaml (first time: --install; after that: --update, which bumps VERSION.yaml)
./dev/recompile.sh --update

# run the test cases in test-cases.yaml
pixi run --manifest-path pixi.toml -e default bash dev/pytest-cli.sh subject_day_night_location_map --all
```

Documentation: [docs/index.html](docs/index.html).
