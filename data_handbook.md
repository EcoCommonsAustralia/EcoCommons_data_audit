# [DRAFT] Data Standards and Publishing handbook

## Summary
This is a handbook and style guide for data import operations in the platform. 
It covers the key concepts and conventions for importing a Dataset and ensuring it's correct representation
in the data catalog and function with platform models. The intended audience is anyone publishing 
data to the platform or developing the data architecture or integrations.  

[more intro/mission statement]

## Resources
- Full technical description - [EcoCommons/BSC - Data Architecture]
- [Platform API Reference]

##  Glossary of Terms
| Term | Description |
|---|---|
| CRS | Coordinate Reference System (e.g. EPSG code) |
| Dataset | A structured collection of related information treated as a single unit for analysis, reporting, or computing |

### Document Conventions
This document uses the common technical convention of *Must, Should, May* to convey requirements.
Additionally, terms such as **Dataset** are bold and capitalised when referring to the platform model to 
distinguish from general use.

## Overview of the Data Model

[PRETTY PICTURE]

### Dataset
A dataset is a structured collection of related information. In the platform, a **Dataset** consists 
of one or more layers, which reference a physical data file (e.g a GeoTIFF or CSV).

A Dataset:
- *May* be spatial, spatiotemporal or aspatial.
- *May* contain a single layer or multiple.
- *May* be created in series to describe different temporal or spatial variants.
- *Must* have be accompanied by a minimum set of attributes required by the data catalog interface.

### Collection
A **Collection** is the highest level of data classification. It represents a group of datasets of common origin.
The exact rules of a Collection are not strictly formalized in the platform but typically a Collection represents
related datasets from the same source.  Publishers often organise data in terms of a collection and these
relationships should be reflected in the catalog where possible.

A Collection:
- *Must* contain datasets from the same publisher or source.
- *Must* avoid duplication, redundancy, or ambiguity with other collections.
- *Should* not be created for each individual dataset.
- *May* represent an updated series of publications of the same dataset.
- *May* represent an informal grouping where no formal one exists.

[Review this against other established definitions]

### Data Layers
A **Dataset** consists of one or more **Data Layers**, following common geospatial terminology. 
For climate and environmental data, layers often map to a structured vocabulary of "variables" 
(e.g. Mean Temperature → `bioclim_01`). 

Layers can also be unstructured, using the publisher's own labels or labels adapted to improve readability.

Model inputs such as **SDM** and **Climate Change** 
*predictors* expect Datasets to follow a structured vocabulary, 
but many other model inputs do not consider variable names when processing data.

A multi layer/variable Dataset:
- *Must* contain layers with consistent spatial properties: resolution, CRS and extent.
- *May* contain different data types, e.g. `continuous`, `categorical`.
- *May* contain different data encoding. e.g. `Float32`, `UInt8`
- *May* encode variable labels using structured vocabularies or unstructured labels.

#### Layers, Variables and Parameters
The terms ***layer***, ***variable***, ***parameter*** are often used  
interchangably depending on context, but they all typically refer to 
a **Data Layer** within the platform model.

| Term | Context |
|---|---|
| layer 	| A general term to describe multi layer geospatial data.
| variable 	| A structured set of terms often used in climate and environmental data to describe specific *variables* within a system. e.g. "temperature".
| parameter | The terminology used by CovergeJSON when representing a Data Layer.

> [!NOTE]
> Variable names can be used as a hint to preselect styles in the platform visualiser.

### Temporal Datasets
Generally, climate and environmental Datasets represent a single time interval or aggregate
period for all layers (e.g 2020-2025). 
This is appropriate for the current EcoCommons models e.g. SDM and Climate Change,

A Dataset can also represent a temporal series of layers (e.g `Jan`, `Feb`) where
this is supported for a modelling use case. This should not be considered a supported pattern for
EcoCommons models to date, however it is used by Biosecurity Commons models that share the same catalog.

Temporal data is an area of development, that requires further planning at the catalog and model level.

## Attributes
The data catalog derives it's functionality and value from a set of metadata attributes
that accompany the data itself. 

The platform defines a formal set of attributes for a Dataset. These must be set when publishing data.
Not all attributes are required, but applicable attributes should be included to improve 
the quality and integrity of the data catalog.

### Sourcing Attributes
The publication (e.g. `license`) or domain (e.g. `scientific_name`) attributes of a dataset should 
typically be transcribed from the publisher source.

Other attributes, such as spatial properties, or ranges and encoding are determined 
automatically during the import process and do not need to be explicitly provided.

The table below illustrates where attributes may be sourced:
| Attribute class | Example | Potential source |
|---|---|---|
| Publication 	| year 			| The publisher landing page, or metadata included with data       |
| Domain 		| taxonomy 		| The publisher landing page, or metadata included with data       |
| DataType 		| categorical 	| Metadata included with data (e.g. auxillary xml, or other manifest) |
| Spatial 		| CRS 			| Extracted from spatial data directly, or provided as metadata    |
| Category 	    | Vegetation 	| Determine most appropriate when importing                        |

### Style Guide
The Dataset `title` and `description` attributes are the most visible part of the catalog.
As such a consistent style should be implemented.

#### Title
[Based on existing conventions describe baseline style with example and variants]

#### Description
[describe baseline style with example, do's/dont's]

### Categorisation

> [!NOTE]
> Data categorisation as described here should not be confused with **"categorical data"**,
> which is a particular spatial data type to describe discrete classes of data.

The broadest classification for a Dataset is the `data_category` attribute.

| Categories | Examples |
|---|---|
| Topography 		| DEM data |
| Soil 				| % Sand, % Silt, % Clay |
| Vegetation 		| NVIS, BIOMES, NDVI, Fractional vegetation cover |
| Freshwater 		| Streams, Lakes |
| Marine 			| Ocean |
| Human activity 	| Human population, roads, cities, land use |
| Climate 			| WorldClim, Climond, average monthly climate data |
| Biodiversity 		| Species occurrence data |
| Boundary data 	| LGAs, Countries, NRMs, land parcels |

A secondary classsification attribute exists `data_subcategories`.
This allows a list of sub categories to be assigned to a Dataset.
To date, this list is not formalized and contains a number of variants.
It is closer to an arbitary tagging system than a formal vocabulary.

Data subcategories are presented in the data catalog for each item, however
they are not currrently searchable or browsable. This is a development area to be considered
for the catalog.

### All Attributes
The authoritative source of attributes is available in the [Data API reference]. 

#### General
| Field | Type | Description | Required | Default | Values
|---|---|---|---|---|---|
| `title` | string | Dataset short description | Yes | — | http://purl.org/dc/terms/title
| `description` | string | Dataset long description | Yes | — | http://purl.org/dc/terms/description
| `data_category` | string | Primary data category | Yes | — | *see table above*
| `data_subcategories` | array | Optional secondary data category labels | No | `[]` | *any*
| `time_domain` | string | — | — | `Current` | `Current,Future`
| `year` | integer | Dataset year | No | `null` |
| `year_range` | array | Alternative year range in the form `[2000, 2010]` | No | `[]` |
| `mimetype` | string | Data mimetype | No | `""` |
| `crs` | string | Coordinate Reference System e.g. 'EPSG:4326' (required for tabular data only) | No | `""` |
| `resolution` | string | Optional resolution label (e.g `3 arcmin (~5km)`) | No | `""` |
| `genre` | string | An internal vocabulary for representing scientific/domain detail | No | — | 

#### Publisher
| Field | Type | Description | Required | Default | Values
|---|---|---|---|---|---|
| `license` | string | License short description | Yes | — | http://purl.org/dc/terms/license
| `rights` | string | Rights holder description | Yes | — | http://purl.org/dc/terms/rights
| `downloadable` | boolean | Can be directly downloaded | — | `true` |
| `acknowledgement` | string | Optional additional acknowledgement | No | `""` |
| `doi` | string | Digital Object Identifier | No | `""` |
| `source_url` | string | Publisher data source URL | No | `""` |
| `landing_page` | string | Optional publisher landing page | No | `""` |
| `attributions` | array of objects | Individual attributions | No | `[]` |

#### Species
| Field | Type | Description | Required | Default | Values
|---|---|---|---|---|---|
| `scientific_name` | string | Scientific name for species data | No | `""` |

### Climate
| Field | Type | Description | Required | Default | Values
|---|---|---|---|---|---|
| `emsc` | string | BCC-CSM1-1 | No | `""` |
| `gcm` | string | Global Circulation Model | No | `""` |

> [!NOTE]
> These attributes should be mapped to [standard terms](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/).
> For more information see the "Darwin Core/Dublin Core" section of this document. 

### Data Layer/Variable Attributes
The attributes described so far represent a Dataset as a whole, however individual layers have their own attributes.

- A Data Layer *should* provide human readable label.
- A Data Layer *should* provide units where appropriate (e.g. `mean_temperature` -> `degC`)
- A categorical Data Layer *must* provide a table of values that map values to readable classes.

## Data Import Overview
TBD

### Supported Data Formats
| Format | Extension | Supported domain types
|---|---|---|
| GeoTiff 		| .tif  | Grid
| GeoPackage 	| .gpkg | Polygon, MultiPolygon
| CSV 	    	| .csv  | Point

> [!NOTE]
> The platform can import almost any type of file format (using the opaque `file` import method). 
> However this is only useful for specific model cases and has very limited value in the catalog.

#### GeoTiff
Single or stacked rasters for either continuous or discrete gridded data. Stacked GeoTIFF will
be automatically unpacked by the platform during import so they present as a multi layer Dataset.

Cloud Optimized GeoTIFF is a compatible variant optimised for use in web environments.
The platform does not currently take advantage of this format, however this may change therefore
COG is a valid choice when importing data.

https://en.wikipedia.org/wiki/GeoTIFF

#### GeoPackage
GeoPackage (GPKG) is an open, non-proprietary, platform-independent and standards-based data format 
for geographic information systems built as a set of conventions over a SQLite database.

The GeoPackage standard describes a set of conventions (requirements) for storing vector features, 
tile matrix sets of imagery and raster maps at various scales, schema and metadata.

The platform does not support all variants of GPKG data. The primary implementation is for
polygon data (features). This may be multi polygon and multi layer.  Additional attributes will
be imported, however they are currently opaque to the platform in terms of visualisation
and models do not formally use or expect any.

https://en.wikipedia.org/wiki/GeoPackage

> [!NOTE]
> ESRI Shapefile is also supported, however is deprecated and should be avoided for curated data.

#### CSV
CSV files can be imported to represent point data.
The basic conventions are described in the platform Data Import interfaces, generally requiring
a minimum of a `lat` and `lon` column.  Additional fields can be included, however
their use is model specific.

> [!NOTE]
> A spatial CSV file does not have a standard way of encoding it's CRS. Therefore
> it must be provided as an attribute (`crs`) during import, or it will be assumed *WGS-84*.

### Import Methods
TBD - Describe the API data import methods. 

## Data Representation
So far this document has described how data is published and presented in the platform catalog.
However Datasets are also consumed by modelling workflows and other contexts, such as visualisation
dashboards. This requires a Dataset and it's attributes to be represented in a consistent
and portable format.  The main representation provided by the platform APIs is CoverageJSON.

### CoverageJSON
A coverage is simply a structure that associates positions in space and time with data values. 
CoverageJSON is designed to encode this information in a form easily consumed by web services.

https://covjson.org/cookbook/
https://docs.ogc.org/cs/21-069r2/21-069r2.html

The platform implements a subset of Coverage Domain Types. 

| Supported domain types
|---|
| Grid | 
| Polygon, MultiPolygon | 
| Point |

Additionally coverage:
- Describes the spatial domain of the data (resolution, extent, CRS).
- Represents class information for categorical data.
- Represents Data Layers as coverage "Parameters", including labels, units.
- Describes the data encoding and basic metadata e.g. `min`, `max`, `stddev`.
- Provides pointers (rangeAlternates) for accessing raw data e.g. `.tif`.

### Darwin Core, Dublin Core (DCMI)

> [!NOTE]
> This is in early development and only has a minimal implementation at present.
> Darwin Core in particular currently has no implementation but is here for reference.

Coverage provides a standard for spatial data, but has nothing to say about other data attributes. 
Therefore another way of representing non spatial information is required.

**Dublin Core (DCMI)** is a general-purpose, lightweight metadata standard designed to describe any type of 
digital or physical resource.  In practical terms the platform uses it for basic identifiers, 
e.g. `identifier`, `title`, `description`, `license`, `rights`.

The standard uses a namespace prefix `dct:`. This is a machine readable
pattern and allows CoverageJSON and DCMI to exist in the same document without conflict.

Dataset attributes described earlier in the handbook are mapped to appropriate DCMI terms.

https://www.dublincore.org/specifications/dublin-core/dcmi-terms/

**Darwin Core** is an extension of Dublin Core to provide a standard for biodiversity data.
It includes a conceptual model, specifications and schemas for sharing via various data formats, 
and a glossary of terms (in other contexts these might be called properties, elements, fields, columns, attributes, 
or concepts) by providing identifiers, labels, and definitions.

https://dwc.tdwg.org/
https://www.dublincore.org/



