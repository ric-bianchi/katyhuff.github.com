---
layout: post
title: Notes on GeoModel persistification
---



Only PhysVols can have children (like SoSeparator). But children can be of any kind: other PhysVols, SerialTransformers, Transformations, ...

The ChildrenPositions stores relationships. Each row is a relationship between a parent PhysVol and a child node. 
The 'id' is only the number of the row; the 'parentId' field stores the id of the parent PhysVol; the 'childId' and the 'childTable' tells how to find the child: it's the 'id' child of the table 'childTable'. The 'position' field is the child position among all the children.
