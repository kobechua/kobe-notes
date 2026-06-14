# Ray Tracing Optimizations
Ray tracing is computationally intensive, involving millions of rays interacting with many objects in a scene. Optimization reduces rendering times for complex scenes

### Optimization Strategy
- Main concept is to quickly determine if a ray does not intersect with geometry to save time.
- Main strategies to employ
	- Skip rays that miss entire regions
	- Minimize detailed intersection tests
	- Leverage structured or hierarchical spatial relationships
- Benefits of Optimization
	- Reduced computational load
	- Faster rendering times for complex scenes

