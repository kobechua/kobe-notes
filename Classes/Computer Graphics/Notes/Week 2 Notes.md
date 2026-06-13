# Surface Shading
- Transforms flat geometry in to more visually compelling scenes, done by enhancing perception of depth and material properties, helping us interpret shapes and surfaces in a realistic way.
- We focus on two core models **Diffuse Shading** and **Phong Shading**

### Surface Normals
- A surface normal is a vector that is perpendicular to a surface at a given point, showing which way the surface is facing
- It defines the orientation of the surface for **shading and reflection calculations**
- They are almost always unit vectors (length = 1)
- They are used in light models to determine how light interacts with a surface
- Normals vary continuously for smooth surfaces like spheres

#### Sphere Calculation
- A sphere is defined by its center $C$ and radius $r$. 
- For a point P on the sphere.
$$
N = \frac{P-C}{||P-C||}
$$
N is equal to the vector from center to surface point divided by the **vector magnitude** of the vector from center to surface point. We divide by magnitude to normalize the vector

#### Triangle Calculation
We can compute the surface normal for a triangle starting with its 3 vertices. We begin by defining two edge vectors $E_1$ and $E_2$ relative to a common vertex. 
![[Pasted image 20260605132732.png]]
$E_1 = V_2 - V_1$
$E_2 = V_3 - V_1$

Normal is calculated with $N = E_1 \times E_2$ and Normalize N to ensure its a unit vector

$N = \frac{N}{||N||}$

Direction of $N$ depends on vertex winding order

#### Surface Normals: Box (AABB)
To determine the surface normal, the slabs should track which axis causes updates to $t_{min}$ and $t_{max}$. By doing so we can identify  the plane the ray first intersects in the case of a hit

We calculate surface normal by taking the negative of the sign of the ray's direction along the identified axis

$$
N = -Sign(D[axis])\cdot\hat{e}_{axis}
$$
### Direct and Indirect Illumination
Direct illumination is light that reaches a surface directly from a light source. 
- Synthesized surfaces not directly lit by a light source appear unrealistically dark or completely black.
- Real-world lighting includes contributions from indirect light

Indirect Illumination is light that includes contributions from reflections or scattering events before reaching a surface
- This includes from indirect reflections or subtle interactions like color bleeding.

Global Illumination refers to the comprehensive simulation of light in a scene including both direct and indirect illumination. 
- Components include direct and indirect illumination
- Used to achieve photorealism

### Diffuse Shading Basics
#### Lambertian Model
- Lambertian model represents matte surfaces like paper and unfinished wood
- Follows Lambert's cosine law, where intensity is proportional to $cos\theta$ 
- $c = c_r \cdot c_t \cdot max(0,n\cdot l)$
- $c_r$ = surface reflectance
- $c_l$ = light intensity
- $n$ = surface normal (unit vector)
- $l$ = direction towards light (unit vector)

- Diffuse shading can be used for both direct and global illumination
- When modeling direct lighting, parts of the scene that aren't directly lit often appear too dark
- Global Illumination accounts for both direct light sources and indirect light paths
### Ambient Illumination
- Ambient illumination is a technique for improving direct lighting models. If prevents shadows from appearing completely black even when indirect lighting is not explicitly calculated
- We add a constant baseline illumination $c_a$ to simulate indirect light and crudely approximate global lighting effects
- Ambient illumination often supplement diffuse direct lighting
- This keeps shadows soft and scenes visually comprehensible

### Specular Reflection
- Specular reflections are reflections off shiny or reflective surfaces
- Occurs when light reflects off a surface in a single dominant direction like a mirror.
- This type of reflection is highly directional and produces focused highlights
- Visible when the view direction aligns with the angle of reflection of the light source off the surface

### Implementing Diffuse and Specular Shading

#### Diffuse Shading
- Calculate all vectors in world space for accurate results
- Handle back-facing normals using $max(0, n\cdot l)$ 
	- $n$ represents the surface normal
	- $l$ represents the light direction
	- Or if two-sided, flip the normal if facing away
- Adding an ambient term:
	$c = c_2 \cdot (c_a + c_t \cdot max(0, n \cdot l))$
	
	$c_r$: diffuse reflectance
	$c_a$: ambient intensity
	$c_t$: incoming light color
- Improves realism by simulating global illumination effects
- Does not enforce conservation of energy

#### Phong Shading
- Models highlights on shiny surfaces
- Is a direct illumination method
- Incorporates reflection and viewer direction to simulate specular highlights
- Phong Lighting Equation
	$c = c_r \cdot (c_a + c_l \cdot max(0, n \cdot l)) + c_p \cdot c_l \cdot max(0, e \cdot r)^p$
	$c_p$: specular reflectance
	$p$: Pong exponent to control the sharpness of highlight
	$e$: Unit vector toward the viewer (likely from eye ray):
	$e = -|D|$
	$r$: reflection vector

### Reflection Vector
- Reflection vector r is defined as
	$r = -l + 2(l \cdot n)n$
- Derived geometrically from the light direction ($l$) and surface normal ($n$)
- Used to determine alignment with viewer direction ($e \cdot r$) 
- Specular Highlights occur when the viewer aligns with the reflection direction.
- Later we can use this equation for reflection rays where l is replaced by $v = -D$ where $D$ is the eye ray

#### Reflection Vector Derivation
Reflection vector r is defined as
	$r = -l + 2(l \cdot n)n$
	$l$: light vector
	$n$: surface normal (unit vector)
	$r$: reflection vector (desired output, points away from the surface)

![[Pasted image 20260607142701.png]]

![[Pasted image 20260607142719.png]]

![[Pasted image 20260607142729.png]]

![[Pasted image 20260607144125.png]]

### Blinn-Phong Shading
- Optimized Phong Shading by James Blinn, by replacing the reflection vector with **halfway vector**, efficiently approximating the specular component
- $h = \frac{l+e}{||l+e||}$
- $c = c_r \cdot c_t \cdot max(0,n\cdot l) + c_p \cdot c_l \cdot max(0,n\cdot h)^p$
- Advantages include 
	- avoiding need to compute reflection vector
	- the specular highlight is software than normal Phong Shading, which is sometimes desirable
	- Phong exponent $\approx$ 4 times the Blinn-Phong exponent
### Handling Multiple Light Sources
Superposition Principle
- The total light contribution is the sum of the effects from all individual light sources
- Easily extend the shading model to handle $N$ point light sources
- Applied to Blinn-Phone equation
$$
c = c_r c_a + \sum_{i=1}^{N} [c_r \cdot c_{li} \cdot max(0, n \cdot l_i) + c_p \cdot c_{li} \cdot max(0, n\cdot h_i)^P]
$$
	$c_r$: diffuse reflectance
	$c_a$: ambient intensity
	$c_t$: incoming light color
### Distance-Based Light Attenuation
- Real-world light sources lose intensity with distance
- Without attenuation, distant lights appear unrealistically strong.
- Attenuation simulates spreading and absorption of light
- No attenuation: constant intensity
	- good for sunlight
- Linear: $A = \frac{1}{d}$
- Quadratic: $A = \frac{1}{a+bd+cd^2}$

$$
c = c_r c_a + \sum_{i=1}^{N} A_i \cdot [c_r \cdot c_{li} \cdot max(0, n \cdot l_i) + c_p \cdot c_{li} \cdot max(0, n\cdot h_i)^P]
$$
### Direct Light Shading in Ray Tracing
- Both models are evaluated at ray-object intersection points. Diffuse shading requires **surface normal** and **light direction** at the intersection
- Phong shading adds computation for the reflection vector and view direction.
- Highlights depend on the viewer's perspective, making Phong shading view-dependent

### Diffuse Shading vs Phong Shading
**Diffuse shading** is simple and computationally efficient
- Its more suitable for matte surfaces

**Phong shading** extends diffuse shading
- It adds realism with specular highlights
- Has a higher computational cost due to the reflection vector and exponentiation. Can be improved with Blinn-Phong variant

# Transformations
A transformation transforms points or vectors to a new configuration. They are often paired with matrices

Frames can be represented with transformations

	"A frame consists of an origin and basis vectors within a Euclidean vector space. It can be thought of as a frame of reference for describing the position of points and vectors within the space. Frame coordinates can be used to create points in the vector space. Frame coordinate scalars can be used to scale the frame's basis vectors, which are then summed and offset to determine the point within the Euclidean vector space. Frames often represented as a transformation matrix."

## Linear Transformations
- Preserves linearity
- Preserves the origin
- They are transformations that preserve additivity and homogeneity
	- identity
	- rotation
	- scale
	- shear
	- reflection
- **Homogeneity** means that scaling a vector before applying the transformation is equivalent to scaling the result of transforming the original vector.
- **Additivity** means that a transformation can be applied separately to two vectors, and the sum of the results will be the same as applying the transformation to the sum of the original two vectors
- Translation does preserve these

### Conformal Linear Transformations
- Linear transformations that also preserve angels and shape
	- Identity
	- Rotation
	- Uniform Scale - especially important to qualify as a similarity transform
	- Reflection
- This does not include
	- Non-uniform scale
	- Shear
	- Translation

### Similarity Transformation
- A transformation that preserves the shape and proportions of objects, allowing for changes in position, orientation and uniform scale.
	- Preserves angles and proportional distances
- Sometimes called a **Rigid Transform/Rigid Body Transform**, but formally, a rigid transform does **NOT** include scaling

### Affine Transformations
- Preserves collinearity and parallelism, but does not meet the formal requirements of linear transformations.
	- This is because the origin is not preserved if the affine transformation is applied
- A combination of one or more linear transformations followed by a translation
	- Example: a combination of rotations, scales, and/or shearing could be followed by a translation.
## Scale
- Scaling changes the size of an object by expanding or contracting its coordinates
- Uniform scaling is when the same scaling factor is applied to all axes
- Non-uniform scaling is when different scaling factors are applied to all axes

### Scaling Equations

Scaling factors are denotes as $s_x$ and $s_y$ for the corresponding axes
- $s_x$ = 1 no change
- $s_x$ < 1 Contracts along x axis
- $s_x$ > 1 Contracts along y axis

General scaling equations are as follows:
- $x' = s_x \cdot x$, $y' = s_y \cdot y$ 

Uniform scaling equations are as follows:
- $x' = s \cdot x$, $y' = s \cdot y$
- Where we expand/contract the object uniformly in both directions

Uniform scaling changes the size, but not the shape. Non-uniform scaling distorts the proportions of an object.

### Concerns With Scaling
Annihilation, which is degeneracy due to scaling  to zero, resulting in an object or vector collapsing into a lower dimensional subspace or point

Extreme scaling, when very small or large scaling factors lead to information loss due to floating point rounding

### Scaling Around a Point
Scaling can give unexpected results because the origin is the only point that remains fixed during scaling

To control the scaling effect, it is common to:
1. Translate the desired anchor point of the shape to the origin
2. Apply the scaling operation
3. Translate the shape back to its original position

A similar approach can be used for rotation around an anchor point

Shapes may also be stored such that the origin is already at the desired anchor point

## Shear
A shear transformation slides points in a specific direction, distorting the shape of an object and creating a slanted appearance

Although the shape is altered, the area of the object remains preserved. However, extreme shear factors can distort the shape so much that it becomes visually difficult to interpret.

Shear is linear, points remain on the same line relative to the direction of the shear. While the shape changes, the alignment of points is not broken.

#### 2D Shear Transformations
Horizontal shear
$x' = x + sh_x \cdot y$
$y' = y$

Vertical shear
$x' = x$
$y' = y + sh_y \cdot x$

The x and y axes are no longer orthogonal; shear distorts the right angle between them

## 2D Rotation
- Rotation is when we move a point around the origin by an angle $\theta$
- A point $p = (p_x, p_y)$ is rotated to a new position $p' = (p_x', p_y')$
- The new coordinates after rotation by an angle $\theta$ are:
	- $p_x' = p_x cos\theta - p_ysin\theta$
	- $p_y' = p_xsin\theta+p_ycos\theta$

- Rotations are counterclockwise for positive angles and clockwise for negative angles
- The distance from the origin remains unchanged for any rotated point
- Rotations preserve angles between points

#### Radians
Radians are the default angle measure in most mathematical contexts. 

#### Determine Angle of a 2D Vector with +X Axis
Using the rule of right triangles we get

$\theta = acrtan(\frac{y}{x})$, where $v = \begin{bmatrix} x\\ y \end{bmatrix}$

A special function called `atan2(y,x)` is used in graphics APIs to separately track the sign of x and y to solve the correct angle for all four quadrants and avoid a divide by zero

## Translation
- Translation moves a point $p$ in space by adding a displacement vector $T$
- The resulting point $p'$ is the original point $p$ shifted by the vector $t$

$p = \begin{bmatrix} x \\ y \end{bmatrix}$, $t = \begin{bmatrix} t_x\\ t_y \end{bmatrix}$ 

$p' = p + t = \begin{bmatrix} x \\ y \end{bmatrix} + \begin{bmatrix} t_x\\ t_y \end{bmatrix} = \begin{bmatrix} x+t_x \\ y + t_y \end{bmatrix}$

## Matrix Multiplication
- Matrices can be multiplied
- Points and vectors can be multiplied by a transformation matrix to transform them if dimensions agree
- $\begin{bmatrix}a_{11} a_{12}\\a_{21} a_{22}\end{bmatrix} \begin{bmatrix} x \\ y\end{bmatrix} = \begin{bmatrix} a_{11}x + a_{12}y\\ a_{21}x + a_{22}y\end{bmatrix}$
- Not commutative, but is associative and distributive

Given a matrix $M$ with $a$ rows and $b$ columns, and matrix N with $b$ rows and $c$ columns, if we multiply $M \times N$ we get a matrix $R$ (result) with $a$ rows and $c$ columns

Matrix multiplication is undefined if **the number of columns in M does not match the number of rows of N**

Each element in $R$ is calculated as the sum of products of the corresponding elements from the $i^{th}$ row and the $j^{th}$ column of $N$. Mathematically, each element of $R$:
$$
r_{ij} = \sum^{b}_{k = 1} m_{ik} \cdot n_{kj}
$$
Where $k$ iterates over the shared dimension $b$


### Scale with Matrix
- Scale as a transformation matrix:
$S = \begin{bmatrix} s_x 0 \\ 0 s_y\end{bmatrix}$
$p' = S \cdot p$ 
$p = \begin{bmatrix} x \\ y \end{bmatrix}$ $p' = \begin{bmatrix} x' \\ y' \end{bmatrix}$

$\begin{bmatrix} x' \\ y'\end{bmatrix} = \begin{bmatrix} s_x \ 0 \\ 0 \ s_y\end{bmatrix} \begin{bmatrix} x \\ y\end{bmatrix} = \begin{bmatrix} s_x \cdot x \\ s_y \cdot y \end{bmatrix}$

### Identity Transformation

$I = \begin{bmatrix} 1 \ 0 \\ 0 \ 1 \end{bmatrix}$

### Shear 
$$
shearX(\theta) = \begin{bmatrix} 1 \ tan\theta \\ 0 \   1\end{bmatrix}
$$
$$
shearY(\theta) = \begin{bmatrix} 1 \ 0 \\ tan\theta \   1\end{bmatrix}
$$
### Rotation using Matrix
$R(\theta) = \begin{bmatrix} cos\theta \ -sin\theta \\ sin\theta \ cos\theta \end{bmatrix}$ 
$p' = R(\theta) \cdot p = \begin{bmatrix} cos\theta \ -sin\theta \\ sin\theta \ cos\theta \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix} = \begin{bmatrix} xcos\theta \ -ysin\theta \\ xsin\theta \ ycos\theta \end{bmatrix}$

- $p_x' = p_xcos\theta - p_y sin\theta$
- $p_y' = p_x sin\theta - p_y cos\theta$

### Basis Vector
- The columns of the matrix represent the **basic vectors** of the coordinate system or frame
- The **standard basis** is reflected in the identity matrix:
	- $\begin{bmatrix}1 \ 0 \\ 0 \ 1\end{bmatrix}$
	- The $x$ axis is: (1, 0)
	- The $y$ axis is: (0, 1)
- Effect of transformations:
	- Multiplication by a transformation matrix changes the basis vectors
		- Rotation: Rotates the basis vectors
		- Scaling: Changes the magnitude of the basis vectors

## Homogeneous Coordinates
- Additional dimension added to points and vectors
- The homogeneous coordinate is called: $w$
- Used to facilitate translations in similarity and affine transformations
- We can support perspective projection in 3D as well
	- $\begin{bmatrix} x' \\ y' \\ 1\end{bmatrix} = \begin{bmatrix}m_{11} \ m_{12} \ x_t \\ m_{21} \ m_{22} \ y_t \\ 0 \ 0 \ 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix} m_{11}x + m_{12}y + x_t \\ m_{21}x + m_{22}y + y_t \\ 1\end{bmatrix}$

### Homogeneous Translation of Points
1. Extend $p$ to a 3D column matrix with a $1$ in the last position
2. Translation becomes a 3x3 matrix
3. Then compute the new point $p'$ as shown

$p = \begin{bmatrix} x \\ y \\ 1\end{bmatrix}$   $T = \begin{bmatrix}1 \ 0 \ t_x \\ 0 \ 1 \ t_y \\ 0 \ 0 \ 1\end{bmatrix}$   $p' = T \cdot p = \begin{bmatrix} 1 \ 0 \ t_x \\ 0 \ 1 \ t_y \\ 0 \ 0 \ 1\end{bmatrix} \begin{bmatrix}x \\ y \\ 1\end{bmatrix} = \begin{bmatrix}x+t_x \\ y + t_y \\ 1\end{bmatrix}$   

### Homogeneous Translation of Vectors
- When transforming vectors place a $0$ in the homogeneous dimension for the vector. This effectively excludes translation

$V = \begin{bmatrix} x \\ y \\ 0 \end{bmatrix}$  $V' = T \cdot V = \begin{bmatrix} 1 \ 0 \ t_x \\ 0 \ 1 \ t_y \\ 0 \ 0 \ 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 0 \end{bmatrix} = \begin{bmatrix} x \\ y \\ 0 \end{bmatrix}$

#### Homogeneous Representation of Translation
Advantages
- Translation becomes part of a unified transformation framework
- Supports combining translating with other transformations like rotating and scaling
Disadvantages
- Must remember to correctly configure points versus vectors

## Homogeneous Scale and Rotation
### 2D scaling with homogeneous matrix
- Scaling can be represented with homogeneous matrix as well:
$S = \begin{bmatrix}s_x \ 0 \ 0 \\ 0 \ s_y \ 0 \\ 0 \ 0 \ 1 \end{bmatrix}$   $p' = S \cdot p = \begin{bmatrix}s_x \ 0 \ 0 \\ 0 \ s_y \ 0 \\ 0 \ 0 \ 1 \end{bmatrix} \begin{bmatrix}x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix} s_x \cdot x \\ s_y \cdot y \\ 1\end{bmatrix}$

### 3D scaling with homogeneous matrix

$S = \begin{bmatrix} s_x \ 0 \ 0 \ 0 \\ 0 \ s_y \ 0 \ 0 \\ 0 \ 0 \ s_z \ 0 \\ 0 \ 0 \ 0 \ 1\end{bmatrix}$

$p = \begin{bmatrix}x \\ y \\ z \\ 1\end{bmatrix}$, $p' = S \cdot p = \begin{bmatrix} s_x \ 0 \ 0 \ 0 \\ 0 \ s_y \ 0 \ 0 \\ 0 \ 0 \ s_z \ 0 \\ 0 \ 0 \ 0 \ 1\end{bmatrix} \cdot \begin{bmatrix}x \\ y \\ z \\ 1\end{bmatrix} = \begin{bmatrix} s_x \cdot x \\ s_y \cdot y \\ s_z \cdot z \\ 1 \end{bmatrix}$

### 2D Rotation with Homogeneous Matrix

$R(\theta) = \begin{bmatrix}cos\theta \ -sin\theta \ 0 \\ sin\theta \ cos\theta \ 0 \\ 0 \ 0 \ 1 \end{bmatrix}$ $p' = R(\theta) \cdot p = \begin{bmatrix}cos\theta \ -sin\theta \ 0 \\ sin\theta \ cos\theta \ 0 \\ 0 \ 0 \ 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix}xcos\theta - ysin\theta \\ xsin\theta + ycos\theta \\ 1 \end{bmatrix}$

### Rotation with the Right-Hand 3D Coordinate System
- Axes x, y, z follow the right hand rule
- Positive rotation is counterclockwise relative to the axis of rotation
![[Pasted image 20260613121735.png]]

### Rotation with the Left-Hand 3D Coordinate System
- Axes x, y, z follow the left hand rule
- Positive rotation is clockwise relative to the axis of rotation
![[Pasted image 20260613121822.png]]

Not typing all this shit

Right Handed
![[Pasted image 20260613125337.png]]

Left Handed
![[Pasted image 20260613125400.png]]

## Rotation Representation
- A rotation matrix describes the rotational pose of the major axes, though it does not indicate the sequence of rotations that produced this orientation
- This ambiguity arises because there are multiple way to build a rotation matrix
- Understanding how to represent, build, and update rotations is important for applications like animation, physics, and transformations

**Extrinsic rotations** are described in the frame that contains the object. Each axis's angle of rotation is applied sequentially, relative to the containing frame. They are common in world-space transformations or when manipulating objects externally.

**Intrinsic rotations** are described relative to the object's evolving local frame. Each axis' angle of rotation is applied sequentially, however preceding rotations reorient the local frame, affecting how subsequent angles are measured. Common in animation and simulations where objects move based on internal constraints

**Extrinsic rotations rotate an object around the fixed world axes, while intrinsic rotations rotate an object around its own local axes, which move after every rotation**

### Building a Rotation Matrix
#### Extrinsic Method:
- Choose a rotation order
- Apply the rotations sequentially in global coordinates
- Multiply the corresponding matrices together
#### Intrinsic Method:
- Rotations are described in the evolving local frame
- Choose a rotation order
- Rotate the object around the first axis in the local frame
- Update the frame for the next rotations
- Repeat for all axes, measuring angles in the updated frame.

Both methods can build the same rotation matrix as the other, but serve different use cases

### Euler Angles
**Euler angles** are the sequence of rotations around the principal axes
- Yaw, rotation around the z axis, turning left or right
- Pitch, rotation around the y axis, tilting up or down
- Roll, rotation around the x axis, twisting  left or right
These Euler angles can be applied intrinsically or extrinsically

In some specialized systems, mixed frame applications may also occur

Any sequence of rotations (XYZ, ZYX etc.) is valid

### Gimbal Lock
A **gimbal lock** occurs when two rotation axes align, reducing degrees of freedom.

![[Pasted image 20260613130918.png]]

Example: Pitching $90^\circ$ causes the roll and yaw axes to align

### Axis-Angle Rotation
An **Axis-Angle Rotation** is a rotation using a single axis of rotation represented by a unit vector. This axis can be a primary axis (x, y, or z) or an arbitrary axis chosen for the specific rotation. The rotation is defined by an angle around that axis. 

A key benefit to this approach is that it avoids gimbal lock, which can be an issue in other methods like Euler angles

### Arbitrary Axis-Angle Rotation
The main concept behind **Arbitrary Axis-Angle Rotation** is to break it down into smaller steps using intermediate coordinate systems.

**The Approach:**
1. Align the rotation axis with a major axis:
	- Apply a series of transformations to align the arbitrary axis with the z-axis
2. Perform rotation
	- Rotate about the aligned axis
3. Undo the alignment
	- Reverse the initial transformations to return the rotated object to its original frame of reference
This process allows for arbitrary axis rotations in a manageable way

### Quaternions
A **quaternion** is a compact 4D representation of rotation using a scalar and 3D vector

Why use quaternions 
- avoid gimbal lock
- efficiently interpolating rotations
- is computationally stable for chaining rotations

### Quaternions in Transformation workflow
Steps to combine with transformation matrices
- Convert quaternion angle representation to rotation matrix
- Combine with translation and scaling for a complete transformation matrix
- Use interpolation (SLERP) of quaternion for smooth transitions in animations
Benefits in practice
- Ideal for scenarios requiring continuous, stable, rotations
- Works seamlessly with existing matrix-based workflows.

## Compound Transformations
Compound transformations are sequences of transformations applied in some order. These can include translation, rotation, scaling, etc.

Order matters: transformations are not commutative eg. $T \cdot R \ne R \cdot T$

### How does Order Affect Compound Transformations
Why does order matter?
- Matrix multiplication is not commutative
- Rotating a translated object moves the translation but translating a rotated object does not rotate the translation

Practical implications
- Always apply transformations in the intended order
- Common conventions: $T \cdot R \cdot S$ though flexibility exists

### Decomposability of Similarity Transforms
Similarity Transform Properties
- A similarity transform is any combination of T, R, and S, where S is uniform and non-degenerate
- $M_{compound} = T \cdot R \cdot S$
- The resulting $T, R$ and $S$ may differ from the original components

Why does it work?
- Uniform scaling does not introduce shear
- Rotations are orthogonal and preserve scaling relationships.

### Shear from Chaining Non-Uniform Scales and Rotations
Scenario:
- Apply two transformations: $R_1 \cdot S_1$ followed by $R_2 \cdot S_2$ 
- $S_1 = diag(s_{x1} s_{y1})$, $S_2 = diag(s_{x2}, s_{y2})$ 
- $R_1$: Rotation by $\theta_1$, $R_2$: Rotation by $\theta_2$
- Combined Transformation Matrix:
![[Pasted image 20260613153740.png]]

S1 followed by a rotation R1, then another transformation with a second non-uniform scale S2, and rotation R2. The resulting matrix is the product of M2 and M1 

![[Pasted image 20260613153955.png]]

We can evaluate the dot product of $u$ and $v$. If its non-zero, then the basic vectors are **not orthogonal** and shear is present in the compound transformation

orthogonal: independent, unrelated or perpendicular.

![[Pasted image 20260613154236.png]]

### Decomposition of Similarity Transforms
Simple Decomposition Method for Compound Similarity Transforms
- Translation (R): extract from the last column of homogeneous transform matrix
- Rotation (R): Determined using the normalized basis vectors of the upper-left 3x3 submatrix.
- Uniform Scaling (S): Obtained from the magnitude of any basis vector in the upper-left 3x3 submatrix.

More powerful methods such as Singular Value Decomposition(SVD) works with any $n \times n$ matrix.
- They are more computationally expensive but provides detailed information such as:
	- Decomposition into rotation, scaling, and shear components
	- Identification of matrix properties, including rank and singular values.

## Inverse Transformations
An **Inverse Transformation** is a
- Transformation $T$ that has an inverse $T^{-1}$ if $T \cdot T^{-1} = I$ where $I$ is the identity matrix;
- Useful for undoing transformations and transitioning between coordinate spaces.

### Transformations that are Easy to Invert
Scales
- The inverse of $S(s_x, s_y, s_z) = S(\frac{1}{s_x},\frac{1}{s_y},\frac{1}{s_z})$
Rotations
- Orthogonal Property: $R^{-1} = R^T$ (Transpose equals inverse)
- Rotating back by the same angle undoes the rotation
- R refers to the 3x3 rotation matrix and is in the upper left 3x3 submatrix of a 4x4 homogeneous rotation matrix
Translations
- The inverse of $T(t_x, t_y, t_z) = T(-t_x, -t_y, -t_z)$
- Shifting in the opposite direction undoes the translation

### Inverting Composite Transformations
Similarity Transforms:
- A typical composition: $T \cdot R \cdot S$ where $S$ is uniform
- Inversion: Reverse order $S^{-1} \cdot R^{-1} \cdot T^{-1}$

We need to apply inverses in the reverse order to properly undo transformations. This method works for any combination of translation, rotation, and scaling, as long as you have the parts identified in the correct order. Knowing this sequence is crucial for breaking down the composite transformation and inverting it properly

### Inverting Unknown Compositions
If known to be a similarity transform, decomposition is straightforward

One could use the Singular Value Decomposition to
- Decompose transformation into components
- Invert each component stepwise
SVD is robust but computationally expensive. It is generally avoided in high performance computer graphics

### When Inverses Do Not Exist
- Annihilation
	- Scaling by zero removes dimensionality
	- Implication: no inverse exists for such transforms
- Determinant Test:
	- Determinant: indicates the scaling factor of a transform and reveals if any dimensions are collapsed,
	- If det(T) = 0 the transformation is non-invertible
	- Loss of dimensionality makes recovery impossible

### Inversion in 4x4 Matrices
- Determinant Method(Adjugate Matrix Method):
  - Works well for 4x4 homogeneous matrices.
  - Generally efficient in graphics due to hardware optimizations
- Step-by-step inversion:
	- Compute adjoint matrix
	- Divide by determinant zero

Avoid general determinant inversion for large matrices and consider alternatives like LU decomposition, iterative methods, etc.

### Numerical Accuracy and Inversion
- Floating-Point Precision Limits
	- Computers store numbers with limited precision
	- Extreme scaling can cause loss of precision
	- Scaling by $10^6$ then by $10^{-6}$ may not return the original value due to floating-point rounding errors.
- Numerical Instability in Inversions
	- Errors in inversion grow when the determinant of the matrix is very small (but non-zero)
	- Example: Scaling one axis by $10^{-9}$ effectively collapses that dimension, making inversion unstable.
	- Repeated inversion compounds errors
		- Each inversion introduces small numerical errors due to floating-point limitation
		- Performing repeated inversions should be avoided
- Condition Number of a Matrix
	- The condition number measures how sensitive a matrix is to numerical errors during inversion
	- High condition numbers (near-singular matrices) amplify small numerical inaccuracies, leading to unreliable inversion

### Summary of Inverse Transforms
- Simple transformations(scales, rotations, translations) are straightforward to invert
- Composite transformations require reversing the order during inversion
- Singular matrices with annihilation cannot be inverted
- Watch out for numerical accuracy issues
- Determinant method of inversion is efficient for 4x4 matrices, but numerical stability matters.