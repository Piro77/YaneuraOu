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
  set (CMAKE_CXX_FLAGS "-std=c++14 -stdlib=libc++")
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
source/eval/kppt/evaluate_kppt_learner.cpp
source/eval/kppt/evaluate_kppt.cpp
source/eval/evaluate.cpp
source/eval/kppp_kkpt/evaluate_kppp_kkpt_learner.cpp
source/eval/kppp_kkpt/evaluate_kppp_kkpt.cpp
source/eval/evaluate_bona_piece.cpp
source/eval/evaluate_io.cpp
source/eval/kpppt/evaluate_kpppt.cpp
source/eval/kpppt/evaluate_kpppt_learner.cpp
source/eval/kpp_kkpt_fv_var/evaluate_kpp_kkpt_fv_var.cpp
source/eval/kpp_kkpt_fv_var/evaluate_kpp_kkpt_fv_var_learner.cpp
source/eval/kpp_kkpt/evaluate_kpp_kkpt_learner.cpp
source/eval/kpp_kkpt/evaluate_kpp_kkpt.cpp
source/eval/kkppt/evaluate_kkppt_learner.cpp
source/eval/kkppt/evaluate_kkppt.cpp
source/eval/kkpp_kkpt/evaluate_kkpp_kkpt.cpp
source/eval/kkpp_kkpt/evaluate_kkpp_kkpt_learner.cpp
source/eval/evaluate_mir_inv_tools.cpp
source/misc.cpp
source/engine/user-engine/user-search.cpp
source/engine/help-mate-engine/help-mate-search.cpp
source/engine/mate-engine/mate-search.cpp
source/engine/local-game-server/local-game-server.cpp
source/engine/2018-otafuku-engine/2018-otafuku-search.cpp
source/usi.cpp
source/bitboard.cpp
source/thread.cpp
source/shogi.cpp
source/move_picker.cpp
source/position.cpp
source/movegen.cpp
source/extra/mate/mate1ply_with_effect.cpp
source/extra/mate/mate_n_ply.cpp
source/extra/mate/mate1ply_without_effect.cpp
source/extra/see.cpp
source/extra/sfen_packer.cpp
source/extra/kif_converter/kif_convert_tools.cpp
source/extra/entering_king_win.cpp
source/extra/bitop.cpp
source/extra/long_effect.cpp
source/extra/benchmark.cpp
source/extra/book/apery_book.cpp
source/extra/book/book.cpp
source/extra/test_cmd.cpp
source/extra/timeman.cpp
source/learn/learning_tools.cpp
source/learn/multi_think.cpp
source/learn/learner.cpp
source/tt.cpp
)
set(YANE_ALL_SRCS ${YANE_HEADERS} ${YANE_SRCS})

set(TARGET YaneuraOu)

if(NOT DEFINED YANEURAOU_EDITION)
	message(STATUS "BUILD Engine SELECT")
  message(STATUS "cmake -DYANEURAOU_EDITION=LOCAL_GAME_SERVER")
  message(STATUS "cmake -DYANEURAOU_EDITION=HELP_MATE_ENGINE")
  message(STATUS "cmake -DYANEURAOU_EDITION=YANEURAOU_2017_EARLY_ENGINE")
	message(STATUS "cmake -DYANEURAOU_EDITION=YANEURAOU_2018_OTAFUKU_ENGINE")
	set(YANEURAOU_EDITION YANEURAOU_2018_OTAFUKU_ENGINE)
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
