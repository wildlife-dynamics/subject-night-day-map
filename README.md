# subject-day-night-map

subject-day-night-map

## What it does

| Step | What it does |
|---|---|
| **Set Time Range** | Choose the period of time to analyze. |
| **Connect to EarthRanger** |  |
| **Subject Group Name** | Get observations for a subject group from EarthRanger. |
| **Process Relocations** |  |
| **Classify Is Night** | Classifies if segments occur at night in a trajectory dataframe |
| **Persist Relocations** | Persist dataframe to a file or cloud storage object. |
| **Relocations To Trajectory** |  |
| **Persist Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Sort Values** |  |
| **Apply Color Map** | Adds a color column to the dataframe based on the categorical values in the specified column. |
| **Set Groupers** |  |
| **Split Groups** |  |
| **Create Path Layer** | Creates a polyline layer definition based on the provided configuration. |
| **Compute Fitted View State** | Compute a `ViewState` fitted to `geo_layers`, corrected for rendering inside a smaller-than-full-window map widget (see `_ASSUMED_VIEWPORT_WIDTH_PX`). |
| **Set Base Maps** |  |
| **Draw Map** | Creates a map based on the provided layer definitions and configuration. |
| **Persist Day Night Map** | Persist text to a file or cloud storage object. |
| **Create Map Widget Single View** | Create a map widget with a single view. |
| **Set Workflow Details** | Add information that will help to differentiate this workflow from another. |
| **Merge Widget Views** | Merge widgets with the same `title` and `widget_type`. |
| **Gather Dashboard** |  |

## Requirements

- Access to a **EarthRanger** data source, set up as a connection

---

## 1. Load the Workflow

In the workflow runner, add a new template using this repository's URL. Once added, select
**subject-day-night-map** from the available templates list to load it.

---

## 2. Configure the Workflow

You'll be prompted to fill in the following parameters before running.

### Set Time Range

| Field | Description | Default |
|---|---|---|
| Since *(required)* | The start time | — |
| Until *(required)* | The end time | — |
| Timezone |  | — |

### Connect to EarthRanger

| Field | Description | Default |
|---|---|---|
| Data source *(required)* | Select one of your configured data sources. | — |

### Subject Group Name

| Field | Description | Default |
|---|---|---|
| Subject Group Name *(required)* | ⚠️ The use of a group with mixed subtypes could lead to unexpected results | — |

### Set Base Maps

| Field | Description | Default |
|---|---|---|
|   | Select tile layers to use as base layers in map outputs. The first layer in the list will be the bottommost layer displayed. | `[{"url": "https://server.arcgisonline.com/ArcGIS/rest/ser…` |

### Set Workflow Details

| Field | Description | Default |
|---|---|---|
| Workflow Name *(required)* |  | — |

---

## Developing

```bash
# recompile spec.yaml (first time: --install; after that: --update, which bumps VERSION.yaml)
./dev/recompile.sh --update

# run the test cases in test-cases.yaml
pixi run --manifest-path pixi.toml -e default bash dev/pytest-cli.sh subject_day_night_location_map --all
```

Documentation: [docs/index.html](docs/index.html).
