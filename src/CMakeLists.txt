cmake_minimum_required(VERSION 3.8 FATAL_ERROR)

########################### Executables

add_executable(vcluster_test main.cpp VCluster/VCluster.cpp  ../../openfpm_devices/src/memory/HeapMemory.cpp ../../openfpm_devices/src/memory/PtrMemory.cpp ../../openfpm_devices/src/Memleak_check.cpp ../../openfpm_devices/src/memory/CudaMemory.cu VCluster/cuda/VCluster_semantic_unit_cuda_tests.cu )

add_library(vcluster STATIC VCluster/VCluster.cpp)

###########################

target_compile_options(vcluster_test PUBLIC $<$<COMPILE_LANGUAGE:CUDA>:-Xcudafe "--display_error_number --diag_suppress=2885 --diag_suppress=2887  --diag_suppress=2888 --diag_suppress=186 --diag_suppress=111" --expt-extended-lambda>)

target_include_directories (vcluster_test PUBLIC ${CUDA_INCLUDE_DIRS})
target_include_directories (vcluster_test PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories (vcluster_test PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../openfpm_devices/src/)
target_include_directories (vcluster_test PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../openfpm_vcluster/src/)
target_include_directories (vcluster_test PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../openfpm_data/src/)
target_include_directories (vcluster_test PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/config)

target_include_directories (vcluster PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_include_directories (vcluster PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/config)
target_include_directories (vcluster PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../openfpm_data/src/)
target_include_directories (vcluster PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../openfpm_devices/src/)

target_link_libraries(vcluster_test ${Boost_LIBRARIES})

# Request that particles be built with -std=c++11
# As this is a public compile feature anything that links to particles
# will also build with -std=c++11
target_compile_features(vcluster_test PUBLIC cxx_std_11)
target_link_libraries(vcluster_test ${MPI_C_LIBRARIES})

install(TARGETS vcluster DESTINATION openfpm_vcluster/lib)
install(FILES MPI_wrapper/MPI_IallreduceW.hpp 
	      MPI_wrapper/MPI_IrecvW.hpp 
	      MPI_wrapper/MPI_IBcastW.hpp  
	      MPI_wrapper/MPI_IsendW.hpp 
	      MPI_wrapper/MPI_util.hpp 
	      MPI_wrapper/MPI_IAllGather.hpp
	      DESTINATION openfpm_vcluster/include/MPI_wrapper)

install(FILES VCluster/VCluster_base.hpp
	      VCluster/VCluster.hpp 
	      VCluster/VCluster_meta_function.hpp
	      DESTINATION openfpm_vcluster/include/VCluster )

install (FILES util/Vcluster_log.hpp
	 DESTINATION openfpm_vcluster/include/util)

#if(BUILD_TESTING)

#  add_executable(particle_test test.cu)

#  set_target_properties(particle_test PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
#  target_link_libraries(particle_test PRIVATE particles)

#  add_test(NAME particles_10k COMMAND particle_test 10000 )
#  add_test(NAME particles_256k COMMAND particle_test 256000 )

#  if(APPLE)
    # We need to add the default path to the driver (libcuda.dylib) as an rpath,
    # so that the static cuda runtime can find it at runtime.
    #    set_property(TARGET particle_test PROPERTY BUILD_RPATH ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
    #  endif()
    #endif()
