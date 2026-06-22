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
