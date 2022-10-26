# Developing `gpuintegration` on WC

Note that these instructions reflect that fact that on WC we are also going to build `y3_cluster_cpp`.
Thus we are setting up the environment needed for that work.

## For daily work

All that is needed is [the daily setup](y3_cluster_cpp_conda.md) for working on `y3_cluster_cpp`.

## Installing prerequisites

To use the `cuda` directory, there are currently no prerequisites.

To use the `kokkos` directory, one must build both `kokkos` and `kokkos_kernels`.

### Installing kokkos and kokkos-kernels

    cd ${WORKHOME}
    git clone https://github.com/kokkos/kokkos.git
    git clone https://github.com/kokkos/kokkos-kernels.git
    export KKOS_INSTALL_DIR=${WORKHOME}/kokkos-install
    mkdir -p ${WORKHOME}/kokkos-build
    mkdir -p ${WORKHOME}/kokkos-kernels-build
    cd ${WORKHOME}/kokkos-build
    cmake -G Ninja \
        -DCMAKE_CXX_COMPILER=${CXX} \
        -DCMAKE_INSTALL_PREFIX=${KKOS_INSTALL_DIR} \
        -DKokkos_ENABLE_CUDA=ON \
        -DKokkos_ARCH_VOLTA70=ON \
        -DKokkos_ENABLE_CUDA_LAMBDA=ON \
        -DKokkos_ENABLE_CUDA_UVM=ON \
        ${WORKHOME}/kokkos
    ninja
    ninja install



    cd ${WORKHOME}/kokkos-kernels-build
    cmake -G Ninja \
        -DCMAKE_CXX_COMPILER=${CXX} \
        -DCMAKE_INSTALL_PREFIX=${KKOS_INSTALL_DIR} \
        -DKokkos_ROOT=${KKOS_INSTALL_DIR}/lib64/cmake/Kokkos \
        ${WORKHOME}/kokkos-kernels


The following is left over from Ioannis's original instructions.

    -DKokkos_ROOT="/home/isaki001/numint/kokkos/kokkos-build/lib64/cmake/Kokkos"


# Building gpuintegration

    cmake -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DGPUINTEGRATION_BUILD_KOKKOS=1 \
        -DGPUINTEGRATION_BUILD_CUDA=1 \
        -DPAGANI_TARGET_ARCH=70-real \
        -DKokkos_DIR=$KKOS_INSTALL_DIR/lib64/cmake/Kokkos \
        -DKokkosKernels_DIR=$KKOS_INSTALL_DIR/lib64/cmake/KokkosKernels \
        ..

