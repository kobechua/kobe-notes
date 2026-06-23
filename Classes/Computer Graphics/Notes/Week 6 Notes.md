# Numerical Integration and Light Transport in Ray Tracing
![[Pasted image 20260623161508.png]]

![[Pasted image 20260623162410.png]]
Way to approximate integrals
![[Pasted image 20260623162430.png]]
![[Pasted image 20260623162658.png]]
![[Pasted image 20260623162820.png]]
Riemann Sum approximates the integral by dividing the integration domain into a series of equally spaced intervals and evaluating the the function at the midpoint of each interval. Though simple it is limited in accuracy. Increasing # of segments can improve accuracy though it may become computationally expensive fro more complex functions
![[Pasted image 20260623163140.png]]
It approximates an integral by summing rectangular areas under the curve.
![[Pasted image 20260623163321.png]]
Uses liner interpolations between points/ accounts for the slope rather than values. Accuracy limited for functions with complex curvature or irregular behavior
![[Pasted image 20260623163450.png]]
Improves on the rectangle method. Balance between accuracy and computational accuracy
![[Pasted image 20260623163630.png]]
![[Pasted image 20260623163828.png]]
They are well suited for high dimensional and complex function where computations become very expensive. Monte Carlo integration provide flexibility and efficiency, but has high variance if there isn't enough samples
![[Pasted image 20260623164043.png]]
50 random samples
![[Pasted image 20260623164119.png]]
Random sampling helps estimate with no bias.
![[Pasted image 20260623164247.png]]
Quadrupling samples reduces inaccuracy by half
![[Pasted image 20260623164422.png]]
![[Pasted image 20260623164508.png]]
The integral is way off, showing that Monte Carlo can fail to capture narrow regions.
![[Pasted image 20260623164616.png]]
Only 20 samples used but focused near spike, resulting in a close estimate
![[Pasted image 20260623164645.png]]
![[Pasted image 20260623164758.png]]
![[Pasted image 20260623164824.png]]
