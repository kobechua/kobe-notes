# Distribution Ray Tracing
## Distribution Ray Tracing Introduction
![[Pasted image 20260622172055.png]]
Overcome the overly clean look of traditional ray tracing and create physical phenomena for realism.
![[Pasted image 20260622172148.png]]
Shooting multiple rays with small variations, using the averange of randomized distribution of rays.
![[Pasted image 20260622175454.png]]
Refers to the sampling of rays across distributions, not to be confused with distributed computing
![[Pasted image 20260622175536.png]]
Aliasing is when a image distorts due to sampling at too low a resolution, causing jagged edges etc
![[Pasted image 20260622175603.png]]
![[Pasted image 20260622175645.png]]
![[Pasted image 20260622175655.png]]
Stratified or grid sampling. Can get moire patterns 
![[Pasted image 20260622175711.png]]
![[Pasted image 20260622175748.png]]
Random Sampling minimizes patterns and moire, though it can introduce noise unless high number of samples used.
![[Pasted image 20260622175835.png]]
Jittered sampling combines the goods of grid and random sampling
![[Pasted image 20260622175909.png]]
![[Pasted image 20260622175917.png]]
How the sampling principles can be applied to other physical phenomena/effects
![[Pasted image 20260622175927.png]]
## Distribution Ray Tracing Soft Shadows
Hard shadows bad and unrealistic
![[Pasted image 20260622180013.png]]
Soft shadows are more realistic and simulate subtle shading variations in real world
![[Pasted image 20260622180030.png]]

![[Pasted image 20260622180107.png]]
The smoothness increase as sampling increases
![[Pasted image 20260622180246.png]]
 Rectangular light area poorly represents light sources
 ![[Pasted image 20260622180707.png]]
 Each point tested to see if its in the circle, by seeing if the squared distance from the center to the point is less than the radius squared.
![[Pasted image 20260622180837.png]]
Different light fixtures and how the light orients
## Distribution Depth of Field
![[Pasted image 20260622180918.png]]
The range of distances within a scene that appear acceptably sharp in an image. The focused object is sharp while the background is blurry. 
![[Pasted image 20260622181019.png|697]]
![[Pasted image 20260622181031.png]]
Focus plane is the distance where objects appear sharp. The conventional image plane iw colocated with the focus plane

The Aperture square controls the blur strength with configurable size, located at the camera origin to simulate aperture sampling
![[Pasted image 20260622181047.png]]

![[Pasted image 20260622181303.png]]

## Distribution Ray Tracing Glossy Reflections
![[Pasted image 20260622181401.png]]
Reflection where light is scattered in a controlled manner around the ideal reflection direction, resulting in highlights that appear soft or blurred.

![[Pasted image 20260622181443.png]]
After a ray hits an object, the reflected ray is distributed
![[Pasted image 20260622181524.png]]
U and V used as offsets to perturb the reflection direction
![[Pasted image 20260622181629.png]]
High roughness or a larger square means broader scattering. Smaller square are glossier
![[Pasted image 20260622181701.png]]
Invalid rays 

## Distribution Ray Tracing Translucency
![[Pasted image 20260622181735.png]]
Implemented by diffusing light once a ray passes through a translucent surface
![[Pasted image 20260622181803.png]]
## Distributed Ray Tracing Motion Blur
![[Pasted image 20260622181819.png]]
Light over time blends the position of objects
![[Pasted image 20260622181835.png]]
Assign a random time to each ray and distributes the blur among the motion path of the scenes objects. More time samples -> smoother blur.
Interpolate based on the objects velocity/acceleration/derivatives of motion. Combine results of all rays over the exposure interval
## Distribution Ray Tracing Simultaneous Distribution Effects
![[Pasted image 20260622182042.png]]
![[Pasted image 20260622182057.png]]
![[Pasted image 20260622182118.png]]
Reusing the samples across all effects, since sampling for each effect is exponentially expensive $16^n$ where n is the number of distribution effects
![[Pasted image 20260622182202.png]]
![[Pasted image 20260622182218.png]]
## Conclusion
![[Pasted image 20260622182305.png]]

# Recursive Ray Tracing Limitations
![[Pasted image 20260622182403.png]]
Limitations when dealing with complex light interactions, such as indirect illumination and caustics. Recursive ray tracing does not account for the full range of possible light paths. Heckbert's path notation gives insight into light transport
![[Pasted image 20260622182516.png]]
A simple path may indicate direct lighting. More complex paths may describe more refraction and reflection effects
![[Pasted image 20260622182628.png]]
**Reflection** is when light bounces back. **Refraction** is when light changes angles when passing thru a surface. 
**Diffuse reflection** is when light scatters in many directions rather than bounces back in a direction
![[Pasted image 20260622182753.png]]
() - Grouping operator
\* - Zero or more occurences of the preceding interaction
\+ - One or more occurences of the preceding interaction
\^n - n occurences of the preceding interaction
I|I OR - represents OR, allowing alternative paths

![[Pasted image 20260622182931.png]]

![[Pasted image 20260622182946.png]]
Array originates from camera, travels to table, table reflects diffusely and travels to light source.

![[Pasted image 20260622183024.png]]
Light can interact diffusely or specularly with the bowling ball.
![[Pasted image 20260622183053.png]]
The ray passes thru magnifying glass, going thru two specular refractions before reaching the bowling ball. It diffusely reflects light towards the source. 
![[Pasted image 20260622183145.png]]
![[Pasted image 20260622183319.png]]
Indirect illumination is not captured by recursive ray tracing
Recursive ray tracing only considers light contributions from a source, rather than other environmental factors.
![[Pasted image 20260622183519.png]]
Recursive ray tracing cannot simulate such interactions since it terminates the ray after the first diffuse bounce.
![[Pasted image 20260622183600.png]]
Diffuse surfaces are secondary light sources and scatter light in all directions. Recursive ray tracing cannot account lighting from these sceondary light sources and simply ignores them.

![[Pasted image 20260622183729.png]]
Recursive ray tracing can directly follow specular paths that haven't reached a diffuse surface. This limitation results in an incomplete representation of the light in an environment
![[Pasted image 20260622183832.png]]
Caustics is the focusing and redistribution of light
![[Pasted image 20260622183912.png]]
![[Pasted image 20260622183936.png]]
Light begins at L and undergoes 2 specular interactions as it reflects thru the magnifying glass. The light reaches the diffuse surface D then reaches the camera E

![[Pasted image 20260622184028.png]]
Light reflects/refracts off/through a sequence of one or more specular surfaces $S^+$, hits a diffuse surface D, then reaches the camera E

![[Pasted image 20260622184212.png]]

Solve the integral to calculate outgoing radiance
Recursive ray tracing only samples direct lighting. Contributions from indirect lighting are ignored resulting in an incorrect radiance at D

![[Pasted image 20260622184354.png]]

![[Pasted image 20260622184519.png]]
