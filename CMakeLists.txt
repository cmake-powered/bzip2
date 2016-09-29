# cmake -H. -Bbuild -G"Visual Studio 14 2015 Win64" -DCMAKE_INSTALL_PREFIX=E:\libaraies
# cmake --build build --config Release --target install
cmake_minimum_required(VERSION 2.8)


project(bzip2 LANGUAGES C)

set(LIBBZ2_SOURCES
  blocksort.c
  huffman.c
  crctable.c
  randtable.c
  compress.c
  decompress.c
  bzlib.c
)

if (MSVC)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

add_library(libbz2 ${LIBBZ2_SOURCES})
set_target_properties(libbz2 PROPERTIES OUTPUT_NAME "libbz2" PREFIX "")

add_executable(bzip2 bzip2.c)
target_link_libraries(bzip2 libbz2)

add_executable(bzip2recover  bzip2recover.c)


install(TARGETS bzip2 bzip2recover libbz2 RUNTIME DESTINATION "bin" ARCHIVE DESTINATION "lib")
install(PROGRAMS $<TARGET_FILE:bzip2> DESTINATION "bin" RENAME "bunzip2${CMAKE_EXECUTABLE_SUFFIX}")
install(PROGRAMS $<TARGET_FILE:bzip2> DESTINATION "bin" RENAME "bzcat${CMAKE_EXECUTABLE_SUFFIX}")
install(FILES bzip2.1 DESTINATION "man/man1")
install(FILES bzlib.h DESTINATION "include")

# Install bash scripts
if (UNIX OR LINUX)
    install(PROGRAMS bzgrep bzmore bzdiff DESTINATION "bin")
    install(CODE "${CMAKE_COMMAND} -E create_symlink ${CMAKE_INSTALL_PREFIX}/bin/bzgrep ${CMAKE_INSTALL_PREFIX}/bin/bzegrep")
    install(CODE "${CMAKE_COMMAND} -E create_symlink ${CMAKE_INSTALL_PREFIX}/bin/bzgrep ${CMAKE_INSTALL_PREFIX}/bin/bzegrep")
    install(CODE "${CMAKE_COMMAND} -E create_symlink ${CMAKE_INSTALL_PREFIX}/bin/bzmore ${CMAKE_INSTALL_PREFIX}/bin/bzless")
    install(CODE "${CMAKE_COMMAND} -E create_symlink ${CMAKE_INSTALL_PREFIX}/bin/bzdiff ${CMAKE_INSTALL_PREFIX}/bin/bzcmp")

    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/bzegrep.1" ".so man1/bzgrep.1")
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/bzfgrep.1" ".so man1/bzgrep.1")
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/bzless.1" ".so man1/bzmore.1")
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/bzcmp.1" ".so man1/bzdiff.1")
    install(FILES
        bzgrep.1 bzmore.1 bzdiff.1
        "${CMAKE_CURRENT_BINARY_DIR}/bzegrep.1"
        "${CMAKE_CURRENT_BINARY_DIR}/bzfgrep.1"
        "${CMAKE_CURRENT_BINARY_DIR}/bzless.1"
        "${CMAKE_CURRENT_BINARY_DIR}/bzcmp.1"
    DESTINATION "man/man1")
endif()
