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
Uses liner interpolations between points/ accounts for the slope rather than values. Accuracy limited for functions with complex curvature o
