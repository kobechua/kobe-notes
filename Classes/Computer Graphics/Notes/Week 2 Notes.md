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

### 2D Rotation