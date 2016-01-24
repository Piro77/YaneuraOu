project (YaneuraOu C CXX)
set (CMAKE_VERBOSE_MAKEFILE on)

# Tweak build mode
if(NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "" FORCE)
endif()

if(NOT CMAKE_BUILD_TYPE MATCHES "^[Dd][Ee][Bb][Uu][Gg]")
        set(CMAKE_BUILD_RELEASE TRUE)
else()
        set(CMAKE_BUILD_RELEASE FALSE)
endif()

message(STATUS "Building ${PROJECT_NAME} in ${CMAKE_BUILD_TYPE} mode")

cmake_minimum_required(VERSION 3.0.2)
set (CMAKE_VERBOSE_MAKEFILE on)

ENABLE_TESTING()

# Basic build options

find_package (Threads)

add_definitions(-DUSE_MAKEFILE)
add_definitions(-D_LINUX)


IF(${CMAKE_SYSTEM_NAME} MATCHES "FreeBSD")
  set (CMAKE_CXX_FLAGS "-std=c++11 -stdlib=libc++")
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR MINGW OR APPLE)
  set (CMAKE_CXX_FLAGS "-std=c++14 -fno-exceptions -fno-rtti -Wextra -Ofast -MMD -MP -fopenmp -fpermissive")
  set (CMAKE_CXX_FLAGS_RELEASE "-flto")
endif()

include_directories ("${PROJECT_SOURCE_DIR}/source")

if (MINGW)
  ## static
  set (CMAKE_CXX_FLAGS "-std=c++11 -fno-exceptions -fno-rtti -Wextra -static -static-libgcc -static-libstdc++ -fno-strict-aliasing")
endif()

## YaneuraOu
file(GLOB_RECURSE YANE_HEADERS  source/*.h)
set(YANE_SRCS
source/bitboard.cpp
source/engine/2017-early-engine/2017-early-search.cpp
source/engine/help-mate-engine/help-mate-search.cpp
source/engine/mate-engine/mate-search.cpp
source/engine/local-game-server/local-game-server.cpp
source/engine/user-engine/user-search.cpp
source/eval/evaluate.cpp
source/eval/evaluate_bona_piece.cpp
source/eval/evaluate_io.cpp
source/eval/evaluate_kpp.cpp
source/eval/evaluate_kppt.cpp
source/eval/evaluate_no_eval.cpp
source/eval/progress.cpp
source/extra/bitop.cpp
source/extra/benchmark.cpp
source/extra/book/book.cpp
source/extra/book/apery_book.cpp
source/extra/entering_king_win.cpp
source/extra/long_effect.cpp
source/extra/mate/mate1ply_with_effect.cpp
source/extra/mate/mate1ply_without_effect.cpp
source/extra/mate/mate_n_ply.cpp
source/extra/move_picker/move_picker_2017Q2.cpp
source/extra/see.cpp
source/extra/test_cmd.cpp
source/extra/timeman.cpp
source/extra/sfen_packer.cpp
source/learn/evaluate_learn_kppt.cpp
source/learn/evaluate_learn_kpp_kkpt.cpp
source/learn/learner.cpp
source/learn/learning_tools.cpp
source/learn/multi_think.cpp
source/misc.cpp
source/movegen.cpp
source/shogi.cpp
source/position.cpp
source/thread.cpp
source/tt.cpp
source/usi.cpp
)
set(YANE_ALL_SRCS ${YANE_HEADERS} ${YANE_SRCS})

set(TARGET YaneuraOu)

if(NOT DEFINED YANEURAOU_EDITION)
	message(STATUS "BUILD Engine SELECT")
  message(STATUS "cmake -DYANEURAOU_EDITION=LOCAL_GAME_SERVER")
  message(STATUS "cmake -DYANEURAOU_EDITION=HELP_MATE_ENGINE")
  message(STATUS "cmake -DYANEURAOU_EDITION=YANEURAOU_2017_EARLY_ENGINE")
	set(YANEURAOU_EDITION YANEURAOU_2017_EARLY_ENGINE)
endif()

message(STATUS "Building Engine ${YANEURAOU_EDITION}")

add_custom_target(avx2)
set(TARGET YaneuraOu-avx2)
set(TARGET_OPT  "-DUSE_AVX2 -mbmi -mbmi2 -mavx2")
add_executable(${TARGET} ${YANE_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YANEURAOU_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(avx2 ${TARGET})

add_custom_target(sse42)
set(TARGET YaneuraOu-sse42)
set(TARGET_OPT  "-DUSE_SSE42 -msse4.2")
add_executable(${TARGET} ${YANE_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YANEURAOU_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse42 ${TARGET})

add_custom_target(sse41)
set(TARGET YaneuraOu-sse41)
set(TARGET_OPT  "-DUSE_SSE41 -msse4.1")
add_executable(${TARGET} ${YANE_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YANEURAOU_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse41 ${TARGET})

add_custom_target(sse2)
set(TARGET YaneuraOu-sse2)
set(TARGET_OPT  "-DUSE_SSE2 -msse2")
add_executable(${TARGET} ${YANE_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YANEURAOU_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse2 ${TARGET})

add_custom_target(nosse)
set(TARGET YaneuraOu-nosse)
set(TARGET_OPT  " ")
add_executable(${TARGET} ${YANE_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YANEURAOU_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(nosse ${TARGET})
