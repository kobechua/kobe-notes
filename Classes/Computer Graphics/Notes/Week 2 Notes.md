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
N is equal to the vector from center 
