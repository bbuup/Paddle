add_subdirectory(eigen)
add_subdirectory(blas)
add_subdirectory(lapack)
add_subdirectory(detail)
add_subdirectory(jit)

math_library(deformable_conv_functor DEPS dense_tensor)
math_library(concat_and_split_functor DEPS dense_tensor)
math_library(fc_functor DEPS blas jit_kernel_helper)
math_library(gpc DEPS phi_enforce)
math_library(gru_compute DEPS activation_functions math_function)
math_library(lstm_compute DEPS activation_functions)
math_library(math_function DEPS blas dense_tensor)
math_library(matrix_reduce DEPS dense_tensor)
math_library(matrix_inverse DEPS dense_tensor eigen3 blas)
math_library(pooling DEPS dense_tensor)
math_library(segment_pooling)
math_library(sequence2batch)
math_library(matrix_solve DEPS dense_tensor eigen3 blas math_function)
math_library(cross_entropy)
math_library(im2col)
math_library(vol2col)
math_library(softmax DEPS math_function)
math_library(maxouting)
math_library(matrix_bit_code)
math_library(sequence_scale)
math_library(sequence_padding DEPS lod_utils)

cc_library(
  phi_data_layout_transform
  SRCS data_layout_transform.cc
  DEPS tensor blas)

if(WITH_GPU OR WITH_ROCM)
  if(MKL_FOUND AND WITH_ONEMKL)
    math_library(fft spectral_op.cu DEPS dynload_cuda dynload_mklrt
                 dense_tensor)
    target_include_directories(fft PRIVATE ${MKL_INCLUDE})
  else()
    math_library(fft spectral_op.cu DEPS dynload_cuda dense_tensor pocketfft)
  endif()
else()
  if(MKL_FOUND AND WITH_ONEMKL)
    mathp_library(fft DEPS dynload_mklrt dense_tensor)
    target_include_directories(fft PRIVATE ${MKL_INCLUDE})
  else()
    math_library(fft DEPS dense_tensor pocketfft)
  endif()
endif()

if(WITH_MKLDNN)
  math_library(selected_rows_functor DEPS selected_rows_utils math_function
               blas mixed_vector)
else()
  math_library(selected_rows_functor DEPS selected_rows_utils math_function
               blas mixed_vector)
endif()

if(WITH_ROCM)
  hip_library(
    gather_scatter_functor
    SRCS gather_scatter_functor.cc gather_scatter_functor.cu
    DEPS tensor)
else()
  cc_library(
    gather_scatter_functor
    SRCS gather_scatter_functor.cc gather_scatter_functor.cu
    DEPS tensor)
endif()
