# Ray Tracing Optimizations
Ray tracing is computationally intensive, involving millions of rays interacting with many objects in a scene. Optimization reduces rendering times for complex scenes

### Optimization Strategy
- Main concept is to eliminate unnecessary computations as early as possible and quickly determine if a ray does not intersect with geometry to save time.
- Main strategies to employ
	- Skip rays that miss entire regions
	- Minimize detailed intersection tests by focusing only on objects or regions likely to be hit by a ray
	- Leverage structured or hierarchical spatial relationships to efficiently organize and query the scene
- Benefits of Optimization
	- Reduced computational load
	- Faster rendering times for complex scenes

### Challenges in Ray Tracing Optimization
- Complex geometry may be expensive to compute ray intersections against
	- This motivates the desire to quickly cull geometry from consideration for a given ray
- Dynamic scenes, where objects may move, require frequent updates to acceleration structures like bounding volumes or 

