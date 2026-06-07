### Surface Shading
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


