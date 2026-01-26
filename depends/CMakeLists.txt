add_subdirectory(gtest EXCLUDE_FROM_ALL)

if(${CURVE} STREQUAL "BN128")
  include_directories(ate-pairing/include)
  include_directories(xbyak)
  add_library(
    zm
    STATIC

    ate-pairing/src/zm.cpp
    ate-pairing/src/zm2.cpp
  )
  
  install(
    DIRECTORY ate-pairing DESTINATION "include"
    FILES_MATCHING
    PATTERN "*.h"
    PATTERN "*.hpp"
    PATTERN "src" EXCLUDE
    PATTERN "test" EXCLUDE
    PATTERN "proj" EXCLUDE
    PATTERN "java" EXCLUDE
    PATTERN "lib" EXCLUDE
  )
  
  install(
    TARGETS zm DESTINATION lib
  )
  
  install(
    DIRECTORY xbyak DESTINATION "include"
    FILES_MATCHING
    PATTERN "*.h"
    PATTERN "sample" EXCLUDE
    PATTERN "test" EXCLUDE
    PATTERN "gen" EXCLUDE
  )
endif()

if("${WITH_SUPERCOP}")
  include_directories(libsnark-supercop/include)

  enable_language(ASM)

  file(GLOB_RECURSE SUPERCOP_SRCS libsnark-supercop/*.c libsnark-supercop/*.s)

  add_library(
    snark_supercop
    STATIC

    ${SUPERCOP_SRCS}
  )

  target_link_libraries(
    snark_supercop

    ${CRYPTO_LIBRARIES}
  )
endif()

if(NOT "${USE_LINKED_LIBRARIES}")
  OPTION(IS_LIBFF_PARENT OFF)
  add_subdirectory(libff)

  OPTION(IS_LIBFQFFT_PARENT ON)
  add_subdirectory(libfqfft)
endif()