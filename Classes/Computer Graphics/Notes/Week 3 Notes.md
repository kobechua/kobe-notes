# Ray Tracing Optimizations
## Overview
Ray tracing is computationally intensive, involving millions of rays interacting with many objects in a scene. Optimization reduces rendering times for complex scenes

### Optimization Strategy
- Main concept is to eliminate unnecessary computations as early as possible and quickly determine if a ray does not intersect with geometry to save time.
- Main strategies to employ
	- Skip rays that miss entire regions
	- Minimize detailed intersection tests by focusing only on objects or regions likely to be hit by a ray
	- Leverage structured or hierarchical spatial relationships to efficiently organize and query the scene
- Benefits of Optimization
	- Reduced computational load
	- Faster rendering times for complex scenes

### Challenges in Ray Tracing Optimization
- Complex geometry may be expensive to compute ray intersections against
	- This motivates the desire to quickly cull geometry from consideration for a given ray
- Dynamic scenes, where objects may move, require frequent updates to acceleration structures like bounding volumes or grids. 
- Memory management
	- Large scenes can exceed memory capacity
	- Instancing and efficient acceleration structures mitigate this issue by reusing resources and minimizing redundancy
- Algorithmic trade-offs
	- Faster methods may result in approximate results
	- Balancing precision and speed is key
## Object Instancing Overview

### Transforming a Sphere
Consider a Sphere (or Circle) of unit radius
- $P \cdot P = 1$

We may want to transform points $P$ by a **Similarity Transform**:
$Q = sRP + d$ 
- Q is the transformed point in 3D space
- s is the scalar representing uniform scaling
- R is a 3x3 rotational matrix
- d is a translation vector

We want to show that the unit sphere is mapped to a new sphere. The new sphere is centered at $d$ with its radius scaled by $s$.

Rearranging P in terms of Q
![[Pasted image 20260614140155.png]]

![[Pasted image 20260614140246.png]]

