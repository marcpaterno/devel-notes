# Using OpenMP on macOS

The clang and clang++ compilers, as distributed by Apple (from now on "Apple Clang") are sometimes said not to support OpenMP.
While Apple does not ship the necessary OpenMP runtime library, their compilers *do* understand OpenMP directives,
and if used with a library to provide the OpenMP runtime, then normal use of OpenMP with the Apple Clang compilers is possible.

To use OpenMP with Apple Clang, first install the OpenMP runtime library using Homebrew:

    brew install libomp

To compile code that uses OpenMP directives, you need to use the correct compiler flags.
For my example, I'll use `clang++`, but the same works for `clang`:

    clang++ -Xpreprocessor -fopenmp ...

Note that `-fopenmp` is not enough; the `-Xpreprocessor` must immediately precede it,
to tell the `clang++` compiler driver to pass `-fopenmp` to the preprocessor.

At the link step, you must also provide the right flag to tell the linker to link in the OpenMP runtime, `-lomp`.

The following sample code and build steps illustrate the use.

The program source code:

```
// file hello.cc
#include <omp.h>
#include <iostream>

int main ()
{
   #pragma omp parallel
   {
     #pragma omp critical
     {
       std::cout << "Hello world! I'm thread "
         << omp_get_thread_num()
         << " out of "
         << omp_get_num_threads()
         << '\n';
     }
   }
   return 0;
}
```

Compile and link (shown as separate steps, to make clear what is needed for compiling and what is needed for linking:

```
clang++ -Xpreprocessor -fopenmp -c hello.cc
clang++ -o hello hello.o -lomp
```


