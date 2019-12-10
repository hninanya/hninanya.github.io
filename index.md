<p align="center">
	<a href="https://www.puc-rio.br/english/departaments/ccpg/eng_civil.html" alt="Puc-rio">
		<img src="https://img.shields.io/badge/PUC--Rio-Geotechnics-blue">
	</a>
	<a href="https://github.com/hninanya/MPMAXI/graphs/contributors" alt="Contributors">
		<img src="https://img.shields.io/github/contributors/hninanya/MPMAXI">
	</a>
	<a href="https://github.com/hninanya/MPMAXI/commits/master" alt="Last commit">
		<img src="https://img.shields.io/github/last-commit/hninanya/MPMAXI">
	</a>
	<a href="" alt="platforms">
		<img src="https://img.shields.io/powershellgallery/p/DNS.1.1.1.1">
	</a>
	<a href="https://visualstudio.microsoft.com/vs/features/cplusplus/" alt="language">
		<img src="https://img.shields.io/github/languages/top/hninanya/MPMAXI">
	</a>
	<a href="https://github.com/hninanya/MPMAXI" alt="Size">
		<img src="https://img.shields.io/github/repo-size/hninanya/MPMAXI">
	</a>
	<a href="https://github.com/hninanya/MPMAXI" alt="Watchers">
		<img src="https://img.shields.io/github/watchers/hninanya/MPMAXI?style=social">
	</a>
	</a href="" alt="">
	<img>
</p>

# Material Point Method
<p align="center">
	<img src="https://user-images.githubusercontent.com/32991506/68154755-0093bb80-ff27-11e9-8238-0df1e8168038.png"
		width="500">
</p>
# Pulling and pushing
If you want to work in different computer (e.g. Laptop/Desktop)
1. Clone the respository -> git clone https//...
2. Authorize -> git 
	-config user.email "insert github email here"
	-git config user.name "insert github real name here"
3. Then you will be able to pull and push in different computers.

4. dont forget to do a pull before executing pushing/commit
5. in order to clon e a private repository:
 Using HTTPS and providing your username and password in the URL. For example if I 
 was cloning a public repository I might use:
- Public repository:<br>
`git clone https://github.com/hninanya/MPMAXI.git`
- Private repository:<br>
`git clone https://hninanya:password@github.com/hninanya/MPMAXI.git`

**Axisymmetric** implementation of the Material Point Method.
<p align="center">
	<a href="https://user-images.githubusercontent.com/32991506/68080478-e056e600-fdda-11e9-9d01-c6b6383dca94.gif">
		<img src="https://user-images.githubusercontent.com/32991506/68080478-e056e600-fdda-11e9-9d01-c6b6383dca94.gif">
	</a>	
</p>

## Sections
- [Formulation](#Formulation)
- [Dependencies](#Dependencies)
- [Code structure](#Code-structure)
- [Implementation](#Implementation)
- [Options](#Options)
- [GitHub](#GitHub)
<br><br>

## Formulation
C++ implementation of the Material Point Method.

The [Material Point Method](https://www.seas.upenn.edu/~cffjiang/research/mpmcourse/mpmcourse.pdf) is a numerical technique used to simulate the behavior of continuum materials.

The continuum body is described by a number a Lagrangian elements : the material points.
Kinematic equations are solved on the material points  
The material points are surrounded by a background Eulerian grid where the dynamic equations are solved.

It can be summarize in 4 main steps:
1. Transfer data from particles de grid nodes
2. Update node state (apply forces)
3. Transfer data from grid nodes to particles
4. Update particles state
<br><br>

## Dependencies
The following libraries are includes in the `ext/` directory:
- [OpenGL and GLFW](https://www.glfw.org/)
        - Visual.
- [Poisson Generator](https://github.com/corporateshark/poisson-disk-generator)
        - Initialize particle position.
- [Gnuplot](http://www.gnuplot.info/)
        - A portable command-line driven graphing utility for Linux, OS/2, MS Windows, OSX, VMS, and many other platforms.
- [Paraview](https://www.paraview.org/)
        - An open-source, multi-platform data analysis and visualization application. ParaView users can quickly build visualizations to analyze their data using qualitative and quantitative techniques.

The followings are optional dependencies :
- [ffmpeg](https://www.ffmpeg.org/)
        - Output .mp4 videos.
- [OpenMP](https://www.openmp.org/)
        - Parallel computing.
<br><br>

## Code structure
The code, located in `src/`, is structured as following:
- `main.cpp`: OpenGL context. Run simulation.
- `solver.h` and `solver.cpp`: MPM algorithm functions (transfers and updates). Rendering and WriteToFile.
- `node.h` and `node.cpp`: Class for grid nodes.
- `border.h` and `border.cpp`: Class for 2D linear borders. Collision and Friction.
- `paraview.h` and `paraview.cpp`: Class and subclasses for particles and materials. Constitutive model and deformation functions.
- `gnuplot.cpp` and `gnuplot.h` : Export results to folder "gnuplot"
- `mpmread.cpp` and `mpmread.h` : 
- `io.cpp` and `io.h` :
- `material.cpp`and `material.h` : Constitutive Models
- `func.cpp` and `func.h` :
- `gnuplot.cpp` and `gnuplot.h` :
- `tensor.h`:
- `state.cpp` and `state.h` : 
- `constants.h`: Option control and global constants.
<br><br>

## Implementation

#### Characteristics:
Here are the main features of this implementation:
- Sand, Water, Snow and purely elastic simulatiosn already implemented
- 2D.
- Affine-Particle-in-Cell ([APIC](http://www.math.ucla.edu/~jteran/papers/JSSTS15.pdf)) transfer type.
-  B-Spline Quadratic or Cubic interpolation functions (Quadratic is faster, but not as precise).
- Node forces are updated with an explicit method.
- The domain has to be a convex geometry (for collision detection).

#### Add material type:
It is easy to add a new type of material. In `particle.h` and `particle.cpp`, create a new subclasse of `Particle`. Beside constructors, the subclass must contain the following functions:
- In `particle.h`:
```C++
static std::vector<NewMaterial> InitializeParticles() {
        // Define initial particle mass, volume, position, velocity and acceleration
        std::vector<NewMaterial> outParticles;
        // ...
	return outParticles;
}
```
```C++
static std::vector<NewMaterial> AddParticles() {
        // Define mass, volume, position, velocity and acceleration of particles to add during the simulation
	std::vector<NewMaterial> outParticles;
        // ...
	return outParticles;
}
```


- In `particle.cpp`:
```C++
void NewMaterial::ConstitutiveModel() {
    // Update Ap (pre-update deformation gradient)
}
```

```C++
void NewMaterial::UpdateDeformation(const Matrix2f& T) {
    // Update deformation gradient. 
    // T is the sum of the close node velocity gradients.
    // Elasticity, Plasticity functions (return-mapping, hardening) ...
}
```
```C++
void NewMaterial::DrawParticle() {
    // OpenGL output of particle points
}
```

#### Change domain geometry:
The shape of the domain can be changed, but is has to follow this rules:
- It has to be [convex](https://www.easycalculation.com/maths-dictionary/images/convex-nonconvex-set.png).
- It has to be included in [`CUB` ; `X_GRID - CUB`] x [`CUB` ; `Y_GRID - CUB`], where `CUB` is the range of the interpolation function (2 for Cubic, 1.5 for Quadratic).
- Borders have to be straight lines.

To modify the domain, in `border.h`, use the `InitializeBorders` static function:
```C++
static std::vector<Border> InitializeBorders() {
        std::vector<Border> outBorders;
        std::vector<Vector2f> Corners;

        // New border line
	Corners.push_back(Vector2f(X1, Y1));    // First point
	Corners.push_back(Vector2f(X2, Y2));    // Second point
        // type can be [1](sticky), [2](Separating) or [3](Sliding)
        // normal has to be oriented inside the domain and normalized
	outBorders.push_back(Border(type, normal, Corners));
	Corners.clear();

        // Add other border

	return outBorders;
}
```
<br><br>

## Options
Here is a list of different options available. They can be modify in the `constants.h` file.
- Grid:
```C++
// Size of the domain
const static int X_GRID = 128;
const static int Y_GRID = 64;
```
- Particle:
```C++
// Select Particle subclass (material type). [Water], [DrySand], [Snow], [Elastic]
#define Material NewMaterial
```

- Transfer particles <-> grid:
```C++
// Interpolation type: [1] Cubic - [2] Quadratic
#define INTERPOLATION 1	
// Time-step (typically about 1e-4)
const static float DT = 0.0001f;
```
- Output (outputs will be generated in the `out/` directory):
```C++
// Generate a .mp4 of the OpenGL window
#define RECORD_VIDEO true
// Generate a .ply file with node coordinates
#define WRITE_TO_FILE false	
// Draw nodes (active nodes have a different color)
#define DRAW_NODES false        // not recommended (slow)
```
<br><br>
## GitHub
In order to clone this repository:

### .gitignore
A .gitignore file is a list of files to ignore when pushing files to Github.com/repository. It tells git which files should be ignored when changes are staged (added) and committed. Some files are commonly not tracked by git so they are not committed nor sent up to repositories. Sending and receiving too many files can slow down development and takes up unnecessary space.
