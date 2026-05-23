# Computer Graphics Fundamentals - Part I

## Scalars and Points

**Scalars** are real numbers. **Points** are a location on space

Points can be represented as:
$$
Cartesian: (P_x, P_y)
$$
$$
Polar: (r, \phi)
$$
## Vectors

Comprises a direction and magnitude/length. Can be 2D or 3D and can be represented in Cartesian or Polar

Adding vectors can create a new vector

Vectors are **commutative** and **associative**. They also have an identity and inverse element.

Vector can be multiplied by scalar to make a new vector with a new length multiplied by the scalar

When scaling vectors, it has **distributive** and **associative** properties as well as an **identity** and **annihilating** property 

A relative vector is the difference between two points, though it doesn't inherently preserve point of origin.

A vector solely conveys direction and magnitude, and lacks information on its point of origin. To solve this we employ a situated vector, which is a tuple of a point position and a vector. <Point, Vector>

New points can be derived by adding a vector to the originating point.

Generally a sum of points is invalid though there are exceptions. 
- A weighted average of points yields a valid point if the weights sum to 1.
- A weighted average of points yields a vector if the weights sum to 0

## Vector Operators

Vector **magnitude** is the square root of the sum of squares. It represents the distance from the origin to the endpoint of the vector.

$$
||V|| = \sqrt{V_x^2+V_y^2+V_z^2}
$$
Sometimes it useful to just compare the **square magnitude** rather than computing with an expensive square root, though this should be used with caution if not simply comparing the length of two vectors, since it is a nonlinear
$$
||V||^2 = V_x^2+V_y^2+V_z^2
$$
A **unit vector** is a vector with a length of 1. Any vector can be converted to a unit vector by normalization unless its a zero vector. Normalization is used to maintain **direction**, but not **magnitude**. Denoted by ^
$$
\hat{V}=\frac{V}{||V||}
$$
To measure distance between two points, find the **relative vector** between two points, then the **magnitude** of the relative vector

The **dot product** is a vector operator that calculates two vectors. It is both **commutative** and **distributive**. It measures how much of the vector A lies in the direction of vector B, scaled by the magnitude of B
$$
A \cdot B = A_xB_x + A_yB_y
$$
$$
A \cdot B = A_xB_x + A_yB_y + A_zB_z
$$

$$
A \cdot B = |A||B|cos\theta
$$
where $\theta$ is the angle between $A$ and $B$

Timestamp 10:19

The **cross product** takes two 3d vectors and produces a third vector that is perpendicular to both. This new vector represents the area of the parallelogram made by the vectors used.
$$
A \times B = (A_yB_z-A_zB_y), (A_zB_x - A_xB_z), (A_xB_y-A_yB_x)
$$
$$
||A \times B|| = ||A||\ ||B||\ |sin\theta|
$$
Cross product can be represented with right hand rule

## Vector Spaces, Euclidean Spaces, Frames
**Vector space** is a collection of vectors that can be uniquely specified by some number of dimensions

A **Euclidean Vector Space** is a two/three dimensional and consists of a set of points that supports certain mathematical operations. It is a vector space that can make it useful for describing classical physical reality.

- It features a dot product
- The magnitude/length of vectors can be computed
- The distance between 2 points or vectors can be computed

A **frame** consists of an origin and basis vectors within a Euclidean vector space. It can be thought of as a frame of reference for describing the position of points and vectors within the space. Frame coordinates can be used to create points in the vector space. Frame coordinate scalars can be used to scale the frame's basis vectors, which are then summed and offset to determine the point within the Euclidean vector space. Frames often represented as a transformation matrix. 

A 3D vector space can be left or right handed. Once 2 orthogonal basis vectors (y or z), then the third basis vector must be orthogonal, but it allows either direction. 

# Ray Tracing Intro - Fundamental Rendering Approaches

## Rendering
The two approaches are **Object-Order Rendering** and **Image-Order Rendering**.
- Object-order rendering iterates over scene geometry and projects it onto the screen. Because we are considering each object, when an object overlaps we check the distance from the camera to see which goes over. The Z buffer records the depth and compares values to save extra calculations
- Image-order rendering iterates over pixels and traces them back to the scene. It involves analyzing each point in an image plane and determining which object contributes to that pixel. Contribution is determined by the objects surface characteristics in relation to light sources in the scene. Visible surface determination is more expensive but accurate and sharp. This creates more realistic images than object order rendering. Ray-tracing is an example of image-order rendering

## Rays
Rays are the fundamental component of Ray Tracing. They are simply situated vectors. The rays originate from the eye/camera. The ray is directed from the corresponding pixel sensor of the camera towards the scene according to the inherent properties of the camera. (e.g. view plane size and position determines camera FOV)

Ray tracing steps are as follows:
- Ray Generation -> Determine the ray based on the camera view per pixel
- Ray Intersection -> Determine which rays intersect geometry in scene; determine the intersection position and surface normal. The position of the closest intersection is determined and the surface of the normal geometry that is intersected is computed
- Shading -> Assign pixel value according to surface properties and illumination

```python
for each pixel do
	compute viewing ray
	find first object hit by ray and its surface normal n
	set pixel color to value computed from hit point, light, and n
```

## Real World Camera vs Virtual
A real world camera must adhere to physical constraints, such as having the light sensor behind a lens/pinhole. The lens/pinhole focuses light to form an image on the sensor, resulting in an inverted projected image that is corrected by the camera's internal processing. 

A virtual camera in a Euclidean Vector Space is modeled more flexibly. It assumes that the light rays travel directly from the scene to the view plane/sensor as if the sensor pixels measure light from a certain direction defined by a focal point behind the sensor.

This approach simplifies the mathematics of projection by avoiding the need to deal with an inverted image. It directly computes the direction from the camera to the scene, allowing straightforward ray tracing and perspective transformations. The virtual camera' setup allows easier integration with rendering algorithms and eliminates physical constraints in a real world camera

## Ray Generation

Given a camera/eye position (origin) and a point on the view plane that corresponds with a pixel (s) we can find the vector with 
`origin - s` . We need a situated vector, so its important to preserve the ray's point of origin

To determine the view plane point S, we need to be able to relate the view plane to the coordinate system of the scene. 

origin (E) 
distance from plane (d)
the plane is nx * ny total pixels in a grid layout
assume we know the size of the view plane. We know the distance from the center of the view plane to all edges

![[Pasted image 20260523111908.png]]

