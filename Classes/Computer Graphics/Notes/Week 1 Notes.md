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
- Image-order rendering iterates over pixels and traces them back to the scene. Iterates 


