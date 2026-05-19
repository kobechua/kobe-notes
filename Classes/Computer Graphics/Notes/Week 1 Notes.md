# Computer Graphics Fundamentals - Part I

## Scalars and Points

**Scalars** are real numbers. **Points** are a location on space

Points can be represented as:
$$
Cartesian: (P_x, P_y)
$$
$$
Polar: (r, \phi)
$$
## Vectors

Comprises a direction and magnitude/length. Can be 2D or 3D and can be represented in Cartesian or Polar

Adding vectors can create a new vector

Vectors are **commutative** and **associative**. They also have an identity and inverse element.

Vector can be multiplied by scalar to make a new vector with a new length multiplied by the scalar

When scaling vectors, it has **distributive** and **associative** properties as well as an **identity** and **annihilating** property 

A relative vector is the difference between two points, though it doesn't inherently preserve point of origin.

A vector solely conveys direction and magnitude, and lacks information on its point of origin. To solve this we employ a situated vector, which is a tuple of a point position and a vector. <Point, Vector>

New points can be derived by adding a vector to the originating point.

Generally a sum of points is invalid though there are exceptions. If a weighted average of points yields a valid point if th