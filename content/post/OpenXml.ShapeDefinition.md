---
title: "OpenXml.ShapeDefinition"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "OpenXml"
toc: true
---


## [Shape Definitions And Attributes](Ecma Office Open XML Part 1-L.4.9  Shape Definitions and Attributes/ 4880)
### The Coordinate Systems
#### The Document Coordinate System
- (0,0) in the upper left of the document
- x++, move right
- y++, move downwards
- Unit
- EMUs
- 1 pixel= 9525 EMUs
- 1 cm= 360000 EMUs
- 1 inch = 914400 EMUs
#### The Shape Coordinate System
- When get the width and height
- (0,0) in the upper left of the shape
- Unit: EMUs
#### The Path Coordinate System
- (0,0) in the upper left of the shape
- Units
- same EMU dimensions
- x
- 1= width
- y
- 1= height

### Specifying
#### Defining
- built using lines, curves and calculations
- code
```
<p:sp>
<p:spPr>
<a:xfrm>
<a:off x="1981200" y="533400"/>
<a:ext cx="1143000" cy="1066800"/>
</a:xfrm>
**<a:prstGeom prst="heart">**
</a:prstGeom>
</p:spPr>
</p:sp>
```
- preset shape redered by the generating application where documented within **ST_ShapeTypes**
#### Adjusting
- code
```
<p:sp>
<p:spPr>
<a:xfrm>
<a:off x="3276600" y="990600"/>
<a:ext cx="978408" cy="484632"/>
</a:xfrm>
<a:prstGeom prst="rightArrow">
<a:avLst>
**<a:gd name="adj1" fmla="val 50000"/>**
**<a:gd name="adj2" fmla="val 50000"/>**
</a:avLst>
</a:prstGeom>
</p:spPr>
</p:sp>
```

### Specifying a Custom Shape
#### Defining
- code
```
<p:sp>
<p:spPr>
<a:xfrm>
<a:off x="3200400" y="1600200"/>
<a:ext cx="1200000" cy="1000000"/>
</a:xfrm>
<a:custGeom>
<a:pathLst>
**<a:path w="2" h="2">**
<!-- x= 2= 1200000 EMUs, y= 2= 1000000 EMUs -->
<a:moveTo>
**<a:pt x="0" y="2"/>**
</a:moveTo>
<a:lnTo>
<a:pt x="2" y="2"/>
</a:lnTo>
<a:lnTo>
<a:pt x="1" y="0"/>
</a:lnTo>
<a:close/>
</a:path>
</a:pathLst>
</a:custGeom>
</p:spPr>
</p:sp>
```
- shape
+ ![c3edf16e-1e3d-11e6-9525-a0c49aa8c3fb.png](/c3edf16e-1e3d-11e6-9525-a0c49aa8c3fb.png)
#### Adjusting
##### Geometry Guides
- code
```
<gdLst>
<gd name="y1" fmla="*/ h adj1 100"/>
</gdLst>
```
- a set number of inputs-> equation-> single output
- */ | h | y1 from the document
##### Adjust Handles
- adjust handle is linked to adjust values that are used as input to the equations
- type
+ XY adjust handle acts in the horizontal/ vertical direction, (horizontal, vertical)
+ polar adjust handle acts in a polar manner, (radial width, radial angle)
- code
```
<ahXY 
gdRefX="adj1" minX="-2147483647" maxX="2147483647" 
gdRefY="adj2" minY="-2147483647" maxY="2147483647">
<pos x="x1" y="y1"/>
</ahXY>
```
- XY Adjust Handle guide the min/ max value for both x and y coordinates **within the shape coordinate system**
##### Additional Properties
###### Connection Sites
- allows to build a diagram from a set of shapes
- params
+ x-coordinate
+ y=coordinate
+ attachment angle
###### Text Rectangle
- define the text rectangle within the shape
- depending on Auto-fit options, the text will flow outside the text rectangle
- Unit: EMUs
- code
```
<a:rect l="0" t="0" r="1200000" b="1000000"/>
```

-----------------

## [PresetShapeDefinitions.xml](\ECMA-376, Fourth Edition, Part 1\OfficeOpenXML-DrawingMLGeometries)
### Reference
- Ecma Office Open XML Part 1 - Fundamentals And Markup Language Reference.pdf
- Shape Guide.fmla - P 2896
- ST_ShapeType - P 2987


### Method
#### avLst
- desc: dajust value num/ fomula
- item
- gd
#### gdLst
- desc: calc val list for pathLst
- param
- item
#### cxnLst
- desc: connection point for line and so on, so that when shape move , line is still connected by the shape connected point
    - ![ca8576cc-20ad-11e6-bf36-a0c49aa8c3fb.png](/ca8576cc-20ad-11e6-bf36-a0c49aa8c3fb.png)
- param
- item
#### rect
- desc: the rectangle for text
- param
- item
#### pathLst
- desc: how to draw the shape
- param
- extrusionOk
- 3D extrusion(梯度急冷)是否允许，忽略则为 false
- fill
- type
- darken
- darkenLess
- lighten
- none
- normal(default value)
- stroke
- h
- w
- item
- arcTo(hR, wR, stAng, swAng)
- 考虑超始点和是否过 90*n 点
- lnTo(pt)
- moveTo(pt)
- cubicBezTo(pt* 3)
- quadBezTo(pts* 2)
- close
