set(LLVM_LINK_COMPONENTS
  Support
  dxcsupport
  hlsl
  )

add_clang_unittest(clang-spirv-tests
  CodeGenSPIRVTest.cpp
  ConstantTest.cpp
  DecorationTest.cpp
  FileTestFixture.cpp
  FileTestUtils.cpp
  InstBuilderTest.cpp
  ModuleBuilderTest.cpp
  SPIRVContextTest.cpp
  SPIRVTestOptions.cpp
  StructureTest.cpp
  TestMain.cpp
  StringTest.cpp
  TypeTest.cpp
  WholeFileTestFixture.cpp
  )

target_link_libraries(clang-spirv-tests
  clangCodeGen
  clangFrontend
  clangSPIRV
  dxcompiler
  effcee
  SPIRV-Tools
  )

# This is necessary so that the linked dxcompiler is loaded into memory space
# first, and in case dxcompiler.so is loaded via 'dlopen', it resolves to the
# same memory space, and prevents getting two copies of global static variables.
add_dependencies(clang-spirv-tests dxcompiler)

target_include_directories(clang-spirv-tests
  PRIVATE ${SPIRV_TOOLS_INCLUDE_DIR} ${DXC_EFFCEE_DIR})

set_output_directory(clang-spirv-tests
  ${LLVM_RUNTIME_OUTPUT_INTDIR} ${LLVM_LIBRARY_OUTPUT_INTDIR})

add_test(NAME test-spirv-codegen
  COMMAND clang-spirv-tests --spirv-test-root
          ${PROJECT_SOURCE_DIR}/tools/clang/test/CodeGenSPIRV)
