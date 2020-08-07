# Inigo Quick Start


## Prerequisites

* Windows 7/8.1/10
* To create Inigo Expansions, an installation of CityEngine (2020.0 or later)
* For commercial work, a license for CityEngine (2020.0 or later)
* Unreal Engine 4.24 (optional)
* Vitruvio - City Engine Plugin for Unreal Engine 4 (optional)


## Installation

### From Pre-Built RPK 

1. [Download Inigo RPK](https://github.com/amybam/Inigo/blob/master/Inigo.rpk)
1. Move into your City Engine workspace directory, so that the RPK ends up in the `data` sub-directory.
1. Apply RPK to City Engine Geometry


### From Source

1. [Download Inigo Project File](https://github.com/amybam/Inigo/tree/master/CityEngineWorkspace) 
1. In City Engine, go to File > Import...
1. Select Project > Existing Projects into Workspace
1. Select the root directory and then the project to be imported
1. Once imported, CGA file in `rules` sub-directory can be applied to City Engine Geometry
1. To compile, right click CGA file > Share as..., and save as RPK file

### For Unreal Engine
1. [Install Vitruvio](https://community.esri.com/docs/DOC-14445-vitruvio-cityengine-plugin-for-unreal-engine-4) following directions provided
1. Move compiled RPK to `Vitruvio Content` folder
1. Load Unreal Engine Project
1. Use `BP_PRTActor` with the RPK to generate geometry

## Expansion Development

Inigo is built to be expanded upon with expansion files that develop styles, interiors, and roofs.  The included style expansions can be used as blueprints for developing your own expansions and is detailed a little bit [here](docs.md).