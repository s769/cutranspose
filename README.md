cuTranspose: a library to transpose 3D arrays in Nvidia CUDA GPUs
=================================================================

cuTranspose is a library to transpose 3D arrays in Nvidia CUDA GPUs. It is written in CUDA C and all its functionality is exposed through C functions. The library is based on the transpositions described in [this article](http://link.springer.com/article/10.1007/s10766-015-0366-5): Jose L. Jodra, Ibai Gurrutxaga and Javier Muguerza. "Efficient 3D Transpositions in Graphics Processing Units" International Journal of Parallel Programming, 43:4, pp. 876-891, 2015. Please cite us in your publications if you use cuTranspose.

The last version of the library is located at [http://www.aldapa.eus/res/cuTranspose/](http://www.aldapa.eus/res/cuTranspose/).

This document shows how to build and use this library.

Index
-----

*   [Installation](#installation)
*   [Using the library](#usage)
*   [Copyright license](#license)

Installation
------------

To build this library you will need the [Nvidia CUDA SDK](https://developer.nvidia.com/cuda-downloads) and the [CMAKE builiding system](https://cmake.org/) installed. You have to specify the build configuration through CMake. The most important configuration elements are:

*   Build type (**CMAKE\_BUILD\_TYPE**): It should be set to _Release_ unless you want to debug the project, in which case it should be set to _Debug_.
*   Floating point precision (**CUT\_SINGLE\_PRECISION**): Set it to _ON_ if you want to build the library to use it with single precision floating points instead of with double precision floating points. We are working on a version that will include different functions for different data types, but it is not ready.
*   Use complex numbers (**CUT\_USE\_COMPLEX**): Set it to _ON_ if you want to build the library to use it with complex numbers instead of real numbers. In this case the C standard library complex.h is used to define the data in the array.
*   The tile size (**CUT\_TILE\_SIZE**): Set the tile size used in the transposition kernels. Don't change it unless you know what you are doing.
*   The brick size (**CUT\_BRICK\_SIZE**): Set the brick size used in the transposition kernels. Don't change it unless you know what you are doing.

We recommend NOT to build the library in the source code tree, so you should create a new folder. For example, you can type the following commands in a linux system:

    mkdir build
    cd build
    ccmake ..
    make
    

This commands build the code and create 3 files for you in the build folder.

*   **libcuTranspose.a**: The library compiled code. Link yout code to this library.
*   **cutranspose.h**: The header you must include in your source files that call to the library functions.
*   **cutttest**: A test program that you can use to test the library.

Using the library
-----------------

The library has a single C function that allows performing every kind of 3D transpositions. This transpositions are named _xzy_, _yxz_, _yzx_, _zxy_ and _zyx_. As an example, let's define _A_, a 3D array of size _nx_\*_ny_\*_nz_ points. The element _A(i,j,k)_ will be in position _(i + j\*nx + k\*nx\*ny)_. If we perform a _yzx_ transposition, the size of the transposed array, _A'_, will be _ny_\*_nz_\*_nx_, the previously mentioned element will be stored in _A'(j,k,i)_ and its new offset will be _(j + k\*ny + i\*ny\*nz)_. For more information see the article mentioned in the introduction.

The function that performs the 3D transposition is named **cut\_transpose3d** and its prototype is

    int cut_transpose3d( data_t*       output,
                         const data_t* input,
                         const int*    size,
                         const int*    permutation,
                         int           elements_per_thread )
    

The return value is 0 for a successful execution and -1 otherwise. The meaning of each parameter is explained below:

*   **output**: A pointer to an allocated GPU memory space where the transposed array will be stored. The data type (_data\_t_) is automatically set to the type defined in the build configuration: float or double, real or complex.
*   **input**: A pointer to GPU memory where the array that must be transposed is stored. If this parameter is equal to the **output** parameter an in-place transposition is performed. Otherwise, both parameters must not overlap.
*   **size**: A 3 element vector with the number of points of the original array in each dimension. Remind that the first value must correspond to the innermost dimension.
*   **permutation**: Specifies the particular transpose to be performed. It must be a 3 integer vector with a permutation of 0, 1 and 2. The 0, 1 and 2 values represent the _x_, _y_ and _z_ axis, respectively. Even the {0,1,2} vector is allowed, which performs a simple data copy.
*   **elements\_per\_thread**: An integer that specifies how many elements are transposed by each GPU thread. Its value must be 1, 2 or 4 and only applies to out-of-place transpositions, so it will be ignored for in-place transpositions. Since this value can affect the transposition's performance you could try all of the 3 values and measure which of them leads to the best results for your particular GPU architecture. 2 have shown to be a sensible default value.

Copyright license
-----------------

cuTranspose is free software: you can redistribute it and/or modify it under the terms of the GNU Lesser General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

cuTranspose is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Lesser General Public License for more details.

You should have received a copy of the GNU Lesser General Public License along with cuTranspose. If not, see [http://www.gnu.org/licenses/](http://www.gnu.org/licenses/).

Copyright 2016 Ibai Gurrutxaga, Javier Muguerza, Jose L. Jodra.

You can contact the authors at [i.gurrutxaga@ehu.eus](mailto:i.gurrutxaga@ehu.eus), [j.muguerza@ehu.eus](mailto:j.muguerza@ehu.eus) and [joseluis.jodra@ehu.eus](mailto:joseluis.jodra@ehu.eus).
