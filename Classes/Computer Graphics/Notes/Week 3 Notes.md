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

![[Pasted image 20260615155622.png]]

Note bullet 3

Surface normals are hard to transform because it loses orthogonality. We can find a way to transfrom normal $n$ such that $n'$ is still orthogonal to the surface

![[Pasted image 20260615160534.png]]

How we actually transform the surface normal.

normal $n$ is perpendicular to any vector $v$ in the tangent plane. Therefore, we must find an $n'$ such that it is perpendicular to the transformed surface

$n' \cdot (Mv) = 0$, $\forall v \in Tangent\ Plane$    

![[Pasted image 20260615161148.png]]

Shows how we can derive $n'$ using the transpose property

### Object Instancing Method
### Object Instancing Calculation
![[Pasted image 20260615162332.png]]

First transform the ray into ray'

Upon a hit compute into world space, otherwise simply return false

![[Pasted image 20260615162718.png]]


# Subdivision

![[Pasted image 20260615163237.png]]
Break down complexity for in computer graphic scenes. Main categories are

Object Subdivision: Focuses on defining approximations of geometry bounds, which can expand to describe groups of objects
Spatial Subdivision: Organizes the space of the scene

![[Pasted image 20260615163556.png]]

Basic techniques for object subdivision

![[Pasted image 20260615163729.png]]

Divides space in disjoint regions and assigning objects to regions they are intersecting. These methods optimize pruning an ray tracing

![[Pasted image 20260615163855.png]]
Breaking up object geometry into more managable components. This is a special case for single objects

![[Pasted image 20260615164047.png]]

# Ray Box Intersection
![[Pasted image 20260615164220.png]]

AABBs can define bounds of a more complex object to quickly reject or narrow down ray misses. 

![[Pasted image 20260615164843.png]]

![[Pasted image 20260615165132.png]]

### Oriented Bounding Boxes (OBBs)
![[Pasted image 20260615165258.png]]

AABB, but within its own local coordinate system and uses object instancing. Note that OBB is more  efficient if inverse transformation matrix is not already available

### Generating AABB for Geometry
![[Pasted image 20260616165327.png]]
Iterate over all points and find the min and max of each dimension

![[Pasted image 20260616165615.png]]

![[Pasted image 20260616165952.png]]

![[Pasted image 20260616170116.png]]

![[Pasted image 20260616170456.png]]

![[Pasted image 20260616170526.png]]

### Applications and Summary
![[Pasted image 20260616170632.png]]

![[Pasted image 20260616170748.png]]

# 3D Grids and DDA
![[Pasted image 20260616171121.png]]

![[Pasted image 20260616171206.png]]

![[Pasted image 20260616171330.png]]

![[Pasted image 20260616171439.png]]

![[Pasted image 20260616171614.png]]

![[Pasted image 20260616171753.png]]

## 3D Digital Differential Analyzer
![[Pasted image 20260616171846.png]]

![[Pasted image 20260616171947.png]]

![[Pasted image 20260616172052.png]]

![[Pasted image 20260616172140.png]]
We can step through the grid without recomputing because we precomputed
![[Pasted image 20260616172301.png]]

![[Pasted image 20260616172438.png]]
![[Pasted image 20260616172550.png]]

# BVH
![[Pasted image 20260616172638.png]]

![[Pasted image 20260616172800.png]]

![[Pasted image 20260616172911.png]]

![[Pasted image 20260616173209.png]]

### Basic Splitting Methods
![[Pasted image 20260616173424.png]]

![[Pasted image 20260616173519.png]]

1. start with root node containing all primitives
2. apply first split, root node divided into two intermediate nodes
3. apply second split, where intermediate node is further divided into child nodes.

![[Pasted image 20260616173645.png]]

![[Pasted image 20260616173822.png]]

![[Pasted image 20260616174014.png]]

![[Pasted image 20260616174125.png]]

![[Pasted image 20260616174259.png]]

![[Pasted image 20260616174318.png]]

![[Pasted image 20260616174353.png]]
If a parent is hit, what is the probability of a child getting hit
Children becoming intermediate nodes help optimize

![[Pasted image 20260616174516.png]]
BVH that balances traversal efficiency and intersection likelihood

## BVH Traversal

![[Pasted image 20260616174639.png]]

## BVH Applications and Summary
![[Pasted image 20260616174832.png]]

![[Pasted image 20260616174843.png]]

![[Pasted image 20260616174857.png]]

# KD Trees
![[Pasted image 20260616174955.png]]
Organize space to simplify queries
![[Pasted image 20260616175059.png]]
![[Pasted image 20260616175159.png]]
![[Pasted image 20260616175316.png]]
![[Pasted image 20260616175559.png]]
![[Pasted image 20260616175753.png]]


# Recursive Ray Tracing
![[Pasted image 20260619151205.png]]

![[Pasted image 20260619151359.png]]

![[Pasted image 20260619151646.png]]
How can we modify the current ray to handle how light reflects off objects

## Shadow Rays
![[Pasted image 20260619233157.png]]

![[Pasted image 20260619233311.png]]
Cast a shadow ray from the intersection point to the light and if it intersects an object before reaching the light, the point is in shadow

![[Pasted image 20260619233502.png]]
We can determine if a shadow ray is in shadow by looking at the direction of a surface. If the surface of an object is away from the light source, then its probably a shadow.


***understand what is the slide trying to tell you,***
***take notes of things you DONT understand***
