# Boston Building Demolition Analysis Dashboard


An interactive web-based visualization dashboard for analyzing building demolition patterns in the Greater Boston area, focusing on buildings constructed from 1630 onwards. This tool provides comprehensive analysis of demolition trends, material lifespans, zoning district patterns, and urban development using data from the NSI Enhanced USA Structures Dataset and Boston Zoning Subdistricts.

## Live Demo

[View Live Dashboard](https://structural-futures-lab.github.io/Boston-Lifespans/)

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Data Pipeline](#data-pipeline)
- [Installation](#installation)
- [Usage Guide](#usage-guide)
- [Data Structure](#data-structure)
- [Methodology](#methodology)
- [Technologies](#technologies)
- [Support](#support)

## Overview

This dashboard visualizes and analyzes building demolition data from the Greater Boston metropolitan area, integrating:
- **NSI Enhanced USA Structures (MA)**: Massachusetts building inventory
- **Boston Zoning Subdistricts**: Official zoning boundary data for spatial analysis
- **Property Assessment Data**: Building characteristics, materials, foundation types, and occupancy
- **Demolition Permits**: Historical demolition records with status tracking
- **Focus Period**: Buildings constructed 1630 or later


## Key Features

### Interactive Analysis Components

#### 1. **Demolition Type Filtering**
- Four demolition categories:
  - **RAZE**: Complete structure demolition
  - **EXTDEM**: Exterior demolition
  - **INTDEM**: Interior demolition
  - **All Types**: Combined view
- Radio button controls for instant filtering
- Type-specific statistics and visualizations

#### 2. **RAZE Lifespan Summary**
- Detailed breakdown of RAZE permits by lifespan category:
  - **Positive Lifespan (>0)**: Valid building lifecycle records
  - **Zero Lifespan (=0)**: Same-year demolition and replacement
  - **Negative Lifespan (<0)**: Data anomalies or rapid redevelopment
- Open/Closed status badges for each category
- Total counts with status distribution

#### 3. **Zoning District Deep Dive**
- **District Selection**: Dropdown menu and clickable overview table
- **Interactive Zoning Map**: Leaflet map displaying RAZE points within selected district
- **District Statistics**: Average demolished lifespan, RAZE count, average current building age, total building count
- **Comparative Age Distribution Charts**:
  - Demolished Building Lifespan Distribution (10-year bins)
  - Current Building Age Distribution (10-year bins)
- **Multi-Category Analysis Toggle**:
  - **Material**: Wood, Masonry, Concrete, Steel, Unknown
  - **Foundation**: Slab, Basement, Crawl Space, Pile, Pier, Solid Wall, Unknown
  - **Occupancy**: Residential, Commercial, Industrial, Government, Education, Assembly, Utility and Misc, Unclassified
- **Side-by-Side Comparison Views**:
  - Demolished vs. Current Building Heatmaps
  - Demolished vs. Current Boxplots
  - Demolished vs. Current Statistics Tables
- **Metric Options**: Count, Count (%), GFA (Gross Floor Area), GFA (%)
- **Configurable Bin Sizes**: 10, 20, 25, 50 years

#### 4. **Material-Lifespan Heatmap**
- Interactive heatmap showing demolition counts by material and lifespan
- Plasma colormap with linear scale
- Materials by demolition count
- Configurable lifespan bin sizes (10, 20, 25, 30, 50 years)
- Toggle between count and percentage display modes

#### 5. **Stacked Bar Charts**
- Material type vs lifespan distribution
- Separate charts for each demolition type
- Dynamic color coding (darkest for largest values)
- Percentage and count view options

#### 6. **Geospatial Mapping**
- Interactive Leaflet map with OpenStreetMap tiles
- Color-coded markers by demolition type
- Hover tooltips with building details (type, lifespan, year built, material, foundation)
- Zoning overlay layer toggle

#### 7. **Temporal Analysis**
- **Demolitions by Year**: Stacked bar chart with RAZE, EXTDEM, INTDEM, and "Demolished & Replaced" categories
- **Age Distribution of Demolished Buildings by Year**: Shows relative ages at demolition
- **Construction Era of Demolished Buildings by Year**: Historical era breakdown (Pre-1900 to 2020+)
- **Building Lifespan Distribution by Demolition Year** (Strip Plot): Jittered scatter plot showing lifespan density

#### 8. **Building Age Distribution**
- **Demolished Buildings**: Histogram of building lifespans (10-year bins)
- **Current Buildings**: Age distribution of existing buildings in Boston (synced X-axis)
- **Log Scale Toggle**: Option to view outliers with logarithmic scale

#### 9. **Material Statistics Table**
- Top materials by demolition count
- Average lifespan per material
- Demolition type breakdown per material

#### 10. **Boxplot Analysis**
- Building lifespan distribution by material type
- Quartile visualization with outlier detection
- Filterable by demolition type
- Horizontal orientation for readability

#### 11. **City-Level Statistics**
- Demolition counts by city
- Grid layout for easy comparison
- Top 10 cities by demolition activity

## Data Pipeline

### Processing Workflow

```
Stage 1: Data Loading & Spatial Join
├── Input: NSI Enhanced USA Structures (MA) GeoPackage
├── Input: Boston Zoning Subdistricts GeoJSON
├── Convert building polygons to centroids
├── Reproject to EPSG:4326 (WGS84)
├── Spatial join: Link buildings to zoning districts
└── Output: Georeferenced building dataset with zoning info

Stage 2: Data Cleaning & Filtering
├── Anti-Noise Filter for RAZE records:
│   ├── Keyword blacklist (kitchen, plumbing, rewire, etc.)
│   └── Permit-type exclusion (PL/E/G prefixes)
├── Permit Matching: Nearest Neighbor Spatial Join (5m radius)
├── Hierarchical Conflict Resolution:
│   ├── Worktype Rank: RAZE > EXTDEM > INTDEM
│   ├── Status Rank: Closed > Open
│   └── Date: Most Recent
├── Lifespan calculation (demolition year - built year)
├── Remove invalid lifespans (≥500 years)
├── Status normalization (Open/Close)
└── Output: Clean demolition dataset

Stage 3: Current Buildings Inventory
├── Create snapshot of all buildings
├── Remove positive-lifespan RAZE records
├── Calculate current age (2025 - year built)
└── Output: Current buildings dataset

Stage 4: Analysis Generation
├── Summary statistics calculation
├── RAZE lifespan breakdown (positive/zero/negative)
├── Zoning district aggregations
├── Material/Foundation/Occupancy heatmaps
├── Boxplot data generation
├── Temporal aggregations (yearly, by era)
├── Strip plot data compilation
└── Output: Comprehensive JSON structure

Stage 5: Visualization Preparation
├── Multiple bin size variations (10, 20, 25, 30, 50 years)
├── Count and GFA metrics
├── Percentage calculations
├── Color mapping (Plasma colormap)
└── Output: boston_demolition_data.json
```

## Installation

### Quick Start

1. **Clone the repository**
```bash
git clone https://github.com/samueeelsiu/Boston-RAZE.git
cd Boston-RAZE
```

2. **File Structure Required**
```
boston-demolition-analysis/
├── index.html                          # Main dashboard
├── boston_demolition_data.json         # Processed demolition data
├── Boston_Zoning_Subdistricts.geojson  # Zoning boundary data
├── preprocessor.py                     # Data processing script
└── README.md                           # This file
```

3. **Launch the Dashboard**

GitHub Pages
```bash
# Navigate to https://structural-futures-lab.github.io/Boston-Lifespans/
```


## Usage Guide

### Controls

1. **Demolition Type Selection**: Use radio buttons to filter by demolition type (does not apply to Zoning District Deep Dive)
2. **Zoning District Selection**: Use dropdown or click table rows to select a district for deep dive analysis
3. **Category Toggle**: Switch between Material, Foundation, and Occupancy analysis in deep dive
4. **Lifespan Bin Size**: Adjust granularity of lifespan analysis (10-50 years)
5. **Metric Selection**: Choose between Count, Count (%), GFA, or GFA (%)
6. **Log Scale Toggle**: Enable logarithmic scale for age distribution charts

### Key Interactions

- **Overview Table**: Click any row to select that zoning district
- **Zoning Map**: Pan, zoom, and hover over RAZE points for details
- **Heatmaps**: Hover for values, view average lifespan in tooltips
- **Main Map**: Toggle zoning overlay, hover markers for building information
- **Charts**: Hover for tooltips, click legends to show/hide series
- **Boxplots**: View quartiles, median, and outliers for each category

## Data Structure

### JSON Schema Overview

```javascript
{
  "summary_stats": {
    "total_demolitions": ...,
    "average_lifespan": ...,          
    "raze_count": ...,
    "extdem_count": ...,
    "intdem_count": ...,
    "negative_raze_count": ...,
    "zero_raze_count": ...,
    "avg_current_building_age": ...,
    "raze_status_by_lifespan": {
      "positive": { "open": ..., "close": ... },
      "zero": { "open": ..., "close": ... },
      "negative": { "open": ..., "close": ... },
      "total": { "open": ..., "close": ... }
    }
  },
  "material_lifespan_demo_avg": {
    "RAZE": ..., "EXTDEM": ..., "INTDEM": ...
  },
  "zoning_district_stats": {
    "All Boston": {
      "count_raze": ...,
      "avg_raze_lifespan": ...,
      "demolished_age_distribution_10yr": [...],
      "heatmap_data": {...},              // By material
      "heatmap_data_foundation": {...},   // By foundation
      "heatmap_data_occupancy": {...},    // By occupancy
      "current_heatmap_material": {...},
      "current_heatmap_foundation": {...},
      "current_heatmap_occupancy": {...},
      "boxplot_material": {...},
      "boxplot_foundation": {...},
      "boxplot_occupancy": {...},
      "current_boxplot_material": {...},
      "current_boxplot_foundation": {...},
      "current_boxplot_occupancy": {...},
      "stats_material": [...],
      "stats_foundation": [...],
      "stats_occupancy": [...],
      "current_stats_material": [...],
      "current_stats_foundation": [...],
      "current_stats_occupancy": [...],
      "positive_raze_points": [...],
      "count_total": ...,
      "avg_current_age": ...,
      "current_age_distribution_10yr": [...]
    },
    "[District Name]": {...}
  },
  "zoning_district_names": [...],
  "zoning_subdistrict_stats": {...},
  "material_lifespan_demo": {...},        // Heatmap data by demo type
  "material_lifespan_demo_gfa": {...},    // GFA-based heatmap data
  "foundation_lifespan_demo": {...},
  "foundation_lifespan_demo_gfa": {...},
  "occupancy_lifespan_demo": {...},
  "occupancy_lifespan_demo_gfa": {...},
  "yearly_stacked": [...],                // Annual demolition counts
  "yearly_age_distribution": {...},       // Age bins by year
  "yearly_construction_era": {...},       // Era distribution by year
  "lifespan_distribution": [...],         // 10-year bins
  "current_age_distribution_10yr": [...], // Current buildings age
  "lifespan_by_year_boxplot": {...},      // Strip plot data
  "material_stats": [...],                // Material statistics
  "material_lifespan_raw_by_demo": {...}, // Raw data for boxplot
  "map_points": [...],                    // Geospatial points
  "city_stats": [...],                    // City-level statistics
  "metadata": {
    "year_range": ...,
    "generated_date": ...,
    "total_ma_buildings": ...,
    "total_boston_buildings": ...,
    "total_boston_demolitions": ...,
    "year_built_range": ...
  }
}
```

## Methodology

### Key Analytical Approaches

1. **Lifespan Calculation & Filtering**:
   - Formula: Demolition Year - Year Built
   - **Positive Lifespan (>0)**: Valid lifecycle, used in ALL visualizations
   - **Non-Positive Lifespan (≤0)**: Excluded from most charts except yearly stacked and summary badges

2. **Current Buildings Definition**:
   - Total buildings minus confirmed demolished (positive-lifespan RAZE)
   - "Demolished & Replaced" (lifespan ≤0) retained as current structures

3. **Spatial Analysis**:
   - Nearest Neighbor Spatial Join with 5-meter search radius (EPSG:2249)
   - Exact matches: ~91%, Buffer matches (<5m): ~9%
   - Polygon to centroid conversion for visualization

4. **Hierarchical Conflict Resolution**:
   - Multiple permits per building resolved by: Worktype > Status > Date

5. **Material/Foundation/Occupancy Grouping**: Standardized classification with mapping

6. **Binning Strategy**: Multiple bin sizes (10, 20, 25, 30, 50 years) for flexible analysis

7. **Color Mapping**: Plasma colormap for intuitive data visualization

8. **Statistical Methods**: 
   - Mean/median calculations
   - Quartile analysis for boxplots
   - Percentage normalization
   - GFA (Gross Floor Area) aggregation

### Data Quality Measures

- Anti-noise filtering for RAZE records (blacklist + permit-type check)
- Duplicate removal based on BUILD_ID
- Invalid lifespan filtering (≥500 years)
- Null value handling with appropriate defaults
- Status normalization (Open/Close inference)

## Technologies

### Frontend
- **HTML5/CSS3**: Responsive layout with modern styling
- **JavaScript ES6+**: Dynamic interactions and data processing
- **Chart.js v4.4.0**: Primary charting library
- **Leaflet v1.9.4**: Interactive mapping with zoning overlays
- **chartjs-chart-boxplot v4.2.7**: Boxplot visualizations

### Backend Processing
- **Python 3.x**
  - pandas: Data manipulation and analysis
  - numpy: Numerical computations
  - geopandas: Geospatial data handling
  - pyogrio: High-performance GeoPackage reading
  - datetime: Temporal data handling
  - json: Data serialization

### Data Formats
- **JSON**: Primary data exchange format
- **GeoPackage (.gpkg)**: Source building data
- **GeoJSON**: Zoning boundary data

## Performance Considerations

### Optimizations

1. **Map Point Limiting**: Maximum 5,000 points for performance
2. **Pre-computed Aggregations**: All statistics pre-calculated
3. **Efficient Data Structure**: Hierarchical JSON for quick access
4. **Dynamic Chart Updates**: Only re-render changed visualizations
5. **Pyogrio Integration**: Arrow-based reading for faster GeoPackage loading

### Known Limitations

- Map visualization limited to 5,000 points
- Demolition data specific to Greater Boston area only
- Zoning Deep Dive restricted to RAZE-type demolitions

## Credit

### Development Team
- **Developer**: Lang (Samuel) Shao
- **Supervisor**: Prof. Demi Fang
- **Institution**: [Northeastern University](https://www.northeastern.edu/)
- **Lab**: [Structural Futures Lab](https://structural-futures.org/)

### Data Sources
- NSI Enhanced USA Structures Dataset
- Massachusetts Property Assessment Database
- Approved Building Permits From City of Boston
- Boston Zoning Subdistricts (City of Boston)

## Support

For issues, questions, or suggestions regarding this dashboard, please contact: shao.la@northeastern.edu
