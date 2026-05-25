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
All pixels have the same dimensions
i,j are indices on the plane
u,v are coordinates in the camera's image plane/viewing window

![[../../../resources/Pasted image 20260523111908.png]]

![[../../../resources/Pasted image 20260523112015.png]]

The idea is to calculate the **displacement vector** (vector that tells you how to get from one point to another) from the pixel center position to the camera position. The sample point $S_{i,j}$ is the center of the pixel that the ray should pass through.

To calculate we need 

$u_i$ and $v_j$ which are scalars which represent how far left/right to move
u and v which are vectors on the view plane representing up/down and left/right directions

Ray vector: $dw+u_iu + v_jv$  
- $e$ represents the camera/eye position or origin. When removing **$e$** from the equation it is no longer 
- $-dw$ represents the forward movement towards the image plane. it is negative since when we look forwards towards the scene we are looking in the **negative** w direction
- $u_iu$ and $v_jv$ can be thought of $amount \times direction$ where $u_iu$ is horizontal displacement and $v_jv$ is vertical displacement


Putting it together, we get $S_{ij} = e-dw+u_iu + v_jv$ which represents a point in the world space.

What's the difference between $(i,j)$, $(u_i, v_j)$, and $S_{i,j}$?
 $(i,j)$: which pixel in the image grid
 $(u_i, v_j)$: that pixel's coordinates on the view plane
 $S_{i,j}$: the 3D world-space point on the view plane.
 The view plane sits between the eye and the scene

![[../../../resources/Pasted image 20260523123443.png|455]]

- This is representing the coordinates of the points on a line in terms of $t$ 

- if $0 < t_1 < t_2$ then $P(t_1)$ is closer to the eye than $P(t_2)$

- 0 <= t <= 1 interpolation between eye and screen 
	t > 1 extrapolation past the screen into the scene  
	t < 0 extrapolation behind the eye

### Ray Generation - From Camera Pose and FOV
We will derive ray directions based on camera's pose and field of view
![[Pasted image 20260523220417.png]]

Here we assume a left handed coordinate system where
- right is along the positive x axis
- up is along the positive y axis
- forward is along the positive z axis
(Formulas can be adapted to a right handed coordinated system)

Before we map from pixel coordinates within the camera space, we first center the pixel grid around the middle of the screen using the formula in the picture above. 

![[Pasted image 20260523222308.png]]

After recentering the pixel coordinates we scale them to match the size of the view plane in world space. We place the view plane one unit in front of the camera along the w vector. The distance is **arbitrary** but it simplifies math.

The vertical FOV is defined by $\theta$ and is the total angle from the top to the bottom of the view.

We define k as the tangent of half this angle. This gives us the vertical half height of the view plane

If the screen is square, that is, W equals H, then the view plane will have equal height and width in the world space, both equal to 2k

*screen refers to the 2D pixel representation, while view plane refers to the screen placed in 3D space in front of the camera*

![[Pasted image 20260524104317.png]]

The formulas required to map the screen coordinates to the 3d world space distances on the view plane. This formula first centers the horizontal coordinate then scales the width to world units. Each pixel is assigned a u and v coordinate on the view plane that we can use to compute its corresponding ray direction.

If the display isn't square, our square screen math will result in a distorted image. Aspect ration tells us the shape of the image in terms of width and height , defined as

$$
A = \frac{W}{H}
$$
If A is greater than 1, the image is wider than it is tall. If it is less than 1, then is it is in portrait mode. Our goal is to keep the vertical field of view fixed. This way, when the display shape changes, the horizontal view adjusts automatically to preserve the vertical framing. To do this we'll use the aspect ratio to stretch or compress the horizontal extent of the view plane

![[Pasted image 20260524112541.png]]

The vertical extent of the view plane is defined using the field of view angle $\theta$. From that we compute k as the tangent of half the angle, giving us half the height in world space. So the full height is 2k. **To compute the width, multiply the height by the aspect ratio A** This keeps the top and bottom of the view consistent across different screens while allowing the sides to grow or shrink to match the screen shape.

![[Pasted image 20260524113200.png]]

![[Pasted image 20260524113523.png]]

Previous mapping assumed each ray goes through the corner of a pixel. For better accuracy we want it to go thru the center of a pixel. To do this we shift the pixel indices by one half. That means we replace $i \rightarrow i + 0.5$
and $j\rightarrow j+0.5$. This yields the new mapping formula

$$ u = (\frac{2(i+0.5)}{W}-1)\cdot A \cdot k$$
$$ v = (\frac{2(j+0.5)}{H}-1)\cdot k$$
This form is widely used, and handles centering the pixel, scaling coordinates, and applying aspect ratio all in one line

![[Pasted image 20260524114242.png]]

In many image systems, including Processing the origin of the pixel grid is in the top left corner, where the y coordinate increases as you go down. We was the vertical axis to increases as we move upwards
- We flip the direction of the v coordinate by subtracting 1 from the centered and scaled v expression

$$ v = (\frac{2(j+0.5)}{H}-1)\cdot k \rightarrow v = (1-\frac{2(j+0.5)}{H})\cdot k$$
- when j is at the top of the screen, v is now positive. When j is near H-1 at the bottom, v becomes negative. This aligns screen rows with world space in a more natural way
![[Pasted image 20260524115727.png]]

We are able to define the ray that goes through any given pixel with the formulas above

![[Pasted image 20260524115918.png]]

![[Pasted image 20260524120025.png]]

Fixing FOV ensures that the top and bottom of the scene are always visible in the same way.

![[Pasted image 20260524120121.png]]

As aspect ratio increases, the horizontal FOV gets wider. 

![[Pasted image 20260524120249.png]]

![[Pasted image 20260524120352.png]]

![[Pasted image 20260524120528.png]]

## Ray Sphere Intersection
## Ray Plane Intersection
## Ray Triangle Intersection
## Ray 3D Triangle Intersection
## Ray Box Intersection

Efficiently test if a ray intersects an Axis-Aligned Bounding Box
- this is used for rendering box shapes
- An AABB is defined by its minimum and maximum corners, and can be thought of as the intersection of three slabs. A point P is inside the box if for each dimension $B_{min} \leq P \leq B_{max}$ 

![[Pasted image 20260525104229.png]]

![[Pasted image 20260525105143.png]]

![[Pasted image 20260525105304.png]]

The slab algorithm uses interval testing to determine whether a ray intersects an AABB. We can think of the slab as the box we see when looking down one axis

1. Calculate ray's T values with respect to the bounding planes of each slab
2. Ensure that $t_{near} \le t_{far}$
3. Start with $[t_0,t_1]=[0,\infty]$
4. 
5. 