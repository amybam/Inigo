# Expansion Documentation

## Imported Objects

defined as constant:

```visual-basic
const name 'unique name of asset'
name = ".path" 'const can be set to path. see note on file structure'

'example of single definition'
const palazzowindowAsset = "facades/palazzo/elem_window_frame.obj"
```

CGA Supported Assets Formats:

- Wavefront OBJ
- Khronos COLLADA DAE
- Autodesk FBX
- Khronos glTF

## Imported CGA Files

For use in rule packages with file expansions e.g. A building generator with seperate rule files for facades, interiors, and roofs. For reference in this section, there is a main file and referenced files. 

To import referenced files into main files:

```objectivec
import fileName: "rules/Buildings/Facades/Italianate.cga"
```

To call functions in referenced files from main file:

```objectivec
fileName.functionName(_attr1, _attr2, _attr3, _attr4)
```

Then in the referenced file the function exists as:

```objectivec
functionName(__attr1, __attr2, __attr3, __attr4) -->
	set(attr1, __attr1)
	set(attr2, __attr2)
	set(attr3, __attr3)
	set(attr3, __attr4)
```

## Geometry Setup

### Mass Input Geometry Setup

To begin operating on the building, geometry has to be aligned to world axes, so that the floors can eventually be subdivided in the y direction.  Then it is cleaned up to account for any modeling errors that might inhibit the model generation.

```objectivec
alignScopeToAxes(y) 'aligns to world axes'
cleanupGeometry(edges, 0.5) 'removes edges on coplanar faces'
```

### Lot Boundary Input Geometry Setup

Lot based systems are far easier to generate for further refinement and are the prefered method for using with this file. 

```objectivec
extrude(world.up, overallheight) 'creates mass based on defined attr'
```

Respective masses are then passed to function that splits mass for decoration

This is the building split heirarchy that organizes and prepares the geometry for decoration.

```
@StartRule
├── Mass (Mass_Lot==1)
│   ├── BuildingSplit (Style==1)
│   │   ├── FrontFacade (front)
│   │   └── SideFacade (side)
│   └── BuildingSplitFacade (Style==2)
│       ├── NIL (bottom)
│       └── BuildingSplitShell (all)
│           ├── BuildingSplit (Style==1)
│           │   ├── FrontFacade (front)
│           │   └── SideFacade (side)
│           └── BuildingSplitPalazzo (Style==2)
│               ├── FrontFacadePalazzo (front)
│               └── SideFacadePalazzo (side)
│               └── Roof (top)
└── Lot (Mass_Lot==2)
    ├── BuildingSplit (Style==1)
    │   ├── FrontFacade (front)
    │   └── SideFacade (side)
    └── BuildingSplitPalazzo (Style==2)
        ├── FrontFacadePalazzo (front)
        ├── SideFacadePalazzo (side)
        └── Roof (top)
```

Note: Style 1 is currently blank and just used for debugging purposes.

## Floor Division

### Standards

Because of the setup we did above, the split can occur in the Y (vertical) direction easily and is structured as seen below:

```objectivec
split(y){ heightA : FloorA
				| heightB : FloorB(split.index) 
				|{~heightB : FloorB(split.index)}* 'floors created to fill building height' 
				| heightC : FloorB(999)} 'top floor'
```

This results in a building with at least 3 floors:

**Floor Structure**

**Floor A:** heightA (Can be foundation or first floor)

**Floor B**: heightB (Standard floor)

**Floor B$^n$**: heightB (Standard floor; n amount of times)

**Floor C**: heightC (Top floor)

### Floor Indexing

Using the floor index key you can use a standard floor height and customize certain floors with further decoration.  See implementation below:

```objectivec
FloorB(floorIndex) --> 'split.index passed from above'
    case floorIndex == 0 : '
        SubFloor(floorIndex) 'passes as subfloor'
    case floorIndex == 2 :
        split(y){~1   : SubFloor(floorIndex) Balcony 'splits floor to include balcony'
                | 0.5 : TopLedge}
    else                 :
        split(y){1 : BottomLedge(floorIndex) | ~1 : SubFloor(floorIndex) | 0.5 : TopLedge}
```

## Notes

### File Structure

```
Folder
├── _assets
│   └── _facades
│		    ├── _palazzo
│       │   ├── elem_facade_halfarch.obj
│       │   ├── elem_facade_ledge.obj
│       │   ├── elem_facade_modillion.obj
│       │   ├── elem_window.obj
│       │   ├── elem_window_frame.obj
│       │   ├── elem_window_glass.obj
│       │   ├── elem_window_roundtop.obj
│       │   └── elem_window_triangletop.obj
│		    └── _italianate
├── _bin
│   └── WREN.cgb
├── _data
│   └── WREN.rpk
├── _images
├── _maps
├── _models
├── _rules
│   └── WREN.cga
├── _scenes
│   └── TestScene.cej
├── _scripts
├── .project
├── .pydevproject
└── .version
```

### Texturing

To set up texturing in facade function use:

```objectivec
setupProjection(0, scope.xy, 1.5, 1, 0, 0, 1)
setupProjection(2, scope.xy, scope.sx, scope.sy)
```