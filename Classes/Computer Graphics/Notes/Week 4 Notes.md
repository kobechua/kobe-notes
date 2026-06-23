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

![[Pasted image 20260620225854.png]]

![[Pasted image 20260620231523.png]]

![[Pasted image 20260620231730.png]]

## Reflection

![[Pasted image 20260620231921.png]]

![[Pasted image 20260620232415.png]]

![[Pasted image 20260620233817.png|697]]
![[Pasted image 20260620234117.png|685]]
## Refraction 
![[Pasted image 20260620233949.png|697]]
![[Pasted image 20260620234559.png]]
![[Pasted image 20260620235644.png]]
![[Pasted image 20260620235704.png]]
$n_t$ for transmission
![[Pasted image 20260620235804.png]]
formula for refracted rays
![[Pasted image 20260621114828.png]]
$n$ = refractive index of the medium the ray is traveling in 
$n_t$ = the refractive index of the other medium
critical angle given by solving for $\theta_c$ 
![[Pasted image 20260621115506.png]]
![[Pasted image 20260621115826.png]]
$n_t \approx n$ means $n_t$ is nearly equal to $n$
![[Pasted image 20260621115925.png]]
![[Pasted image 20260621120039.png]]
switching $n$ and $n_t$
![[Pasted image 20260621120128.png]]
![[Pasted image 20260621120221.png]]
Approximates Fresnel effect.
Changes strength of Blinn-Phong highlight based on angle
![[Pasted image 20260621151546.png]]
# Radiometry
## Introduction to Radiometry: The Measurement of Light
- Radiometry is the measurement of light passing through space in terms of energy and power

![[Pasted image 20260621151835.png]]

![[Pasted image 20260621152022.png]]

![[Pasted image 20260621152930.png]]

## Radiometry Spectral Quantities

![[Pasted image 20260621153007.png]]

## Radiometry Light Directions with Polar Coordinates
![[Pasted image 20260621153319.png]]
![[Pasted image 20260621155102.png]]
![[Pasted image 20260621160114.png]]
$\theta$ Polar angle
$\phi$ Azimuthal angle
Shows how the size of a horizontal circle narrows as the polar angle converges on either pole, reducing the contribution of directions near the poles. Allows us to integrate light in different parts of a sphere
![[Pasted image 20260621160533.png]]

## Radiometry Radiant Intensity
![[Pasted image 20260621160603.png]]
## Radiometry Isotropic Light Source
![[Pasted image 20260621160707.png]]

## Radiometry Radiance Measurement
![[Pasted image 20260621160810.png]]

![[Pasted image 20260621160917.png]]

![[Pasted image 20260621161134.png]]

## Radiometry Projected Power
![[Pasted image 20260621161246.png]]

## Radiometry Radiance
![[Pasted image 20260621162044.png]]
![[Pasted image 20260621162055.png]]
## Radiometry Irradiance
![[Pasted image 20260621162140.png]]

## Radiometry Reflectance
![[Pasted image 20260621162321.png]]

## Radiometry Bidirectional Reflectance Distribution Function
![[Pasted image 20260621162447.png]]
Glossiness, roughness, and other material reflectance characteristics
![[Pasted image 20260621162612.png]]
![[Pasted image 20260621162637.png]]

![[Pasted image 20260621162745.png]]

![[Pasted image 20260621162813.png]]

![[Pasted image 20260621162903.png]]

![[Pasted image 20260621162956.png]]
The angle of incidence is equal to the angle of reflection

![[Pasted image 20260621163041.png]]

## Radiometry Radiosity
![[Pasted image 20260621163158.png]]

![[Pasted image 20260621163244.png]]

![[Pasted image 20260621163301.png]]
![[Pasted image 20260621163329.png]]
What is irradiance

***understand what is the slide trying to tell you,***
***take notes of things you DONT understand***

