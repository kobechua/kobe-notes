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

![[Pasted image 20260614143046.png]]

This shows that rotating a sphere does not change its appearance or equation because a sphere is rotationally symmetric. Only translation $d$ changes its center and the uniform scale $s$ changes its radius

This confirms that the unit sphere $P \cdot P = 1$ transforms into a new sphere centered at $d$ with radius $s$ under the similarity transformation $Q = sRP + d$.

The result highlights that the similarity transformation preserves the geometric properties of the sphere.

We can describe the transformed sphere through the addition of control values introduced to the new equation 
$$
(Q - d) \cdot (Q -d) = s^2
$$
- The sphere center position vector $d$
- Sphere radius $s$
- We can apply a similarity transform to the default spheres initial position $d = 0$ and scale $s = 1$ to determine where the sphere is, then perform a ray intersection against the equation above

![[Pasted image 20260614144849.png]]

If you derived a generic affine transformed sphere equation, you would need to perform transformation of control values in default unit sphere configuration. Then ray casting could be performed.

- Starting with a unit sphere, defined by its basic equation. When an arbitrary affine transformation is applied, which must account for non-uniform scaling or shearing, the result is a more complex quadratic surface that must be derived. 
- The sphere's surface transformed by the affine transformation might represent an ellipsoid or a more generalized quadratic geometry with skewed or off-axis orientations
- The transformed control values define the geometry, but the resulting equation is significantly more complicated than a sphere which only has a center point and radius to describe it

In summary, while the initial transformation of a sphere
into a quadratic surface is technically feasible, it add a lot
of complexity to the description of the geometry and
becomes difficult to work with. This observation
demonstrates the value of keeping transformations
straightforward whenever possible

![[Pasted image 20260614150059.png]]

### Geometry Transformation Strategies
#### Transformation of Geometry 
Many types of geometries can be transformed (Similarity or Affine) while preserving geometric properties. The geometry equation may be more complicated after transformation

**Similarity transformations** preserve shapes, angles, and proportional distances

$q=sRp+d$

Similarity transformations preserve shapes, angles, and proportional distances. This means that the object maintains its original proportions and overall structure.

**Affine transformations** preserve parallelism, collinearity, and convexity, but not angles and lengths

$q=Ap+d$

Affine transformations, on the other hand, preserve parallelism, collinearity, and convexity, but they do not preserve angles or lengths. These transformations allow for more flexibility, like stretching or shearing, but at the cost of distorting the original geometry.

Similarity transformations are a subset of affine transformations.

![[Pasted image 20260614172315.png]]

### Ray Tracing Transformed Geometry

#### Method 1
Simply transforming geometry and camera in world coordinates, computing the rays according to the current camera and scene pose, then determine pixel color according to ray hits.

#### Method 2
![[Pasted image 20260614172647.png]]

In Method 1, we may have to apply transformations to all 10,000 vertices, surface normals, or bounding boxes. Method 2 is more efficient since it transforms the ray rather than the geometry.

**Think of it as saving calculations on the transform by isolating the ray within a space of the ORIGINAL(before it was transformed) object**

#### Object Instancing
![[Pasted image 20260614175552.png]]

![[Pasted image 20260614175930.png]]

### Transforming Surface Normals

![[Pasted image 20260614180033.png]]


