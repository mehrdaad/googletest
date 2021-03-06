
cmake_minimum_required(VERSION 3.6)

if (NOT TARGET googletest)

	find_package(Threads REQUIRED)

	include(ExternalProject)

	ExternalProject_Add(googletest
		GIT_REPOSITORY https://github.com/google/googletest.git
		GIT_TAG release-1.8.0
		CMAKE_ARGS
			# This does not work on MSVC, but is useful on Linux.
			-DCMAKE_BUILD_TYPE=Release
			# Without this, googletest's CMakeLists.txt replaces "/MD" (dynamic run-time) with "-MT" (static runtime).
			# Default config for most applications is "/MD", so this will cause problems if linked with "-MT" googletest.
			# https://msdn.microsoft.com/en-us/library/2kzt1wy3.aspx
			-Dgtest_force_shared_crt=ON
		# Disable the update step.
		UPDATE_COMMAND ""
		# Disable the install step.
		INSTALL_COMMAND ""
		LOG_DOWNLOAD ON
		LOG_CONFIGURE ON
		LOG_BUILD ON
	)

	# Set include directories.
	ExternalProject_Get_Property(googletest source_dir)
	set(GTEST_INCLUDE_DIR ${source_dir}/googletest/include)
	set(GMOCK_INCLUDE_DIR ${source_dir}/googlemock/include)

	# Add libraries.
	ExternalProject_Get_Property(googletest binary_dir)

	if(MSVC)
		set(DEBUG_DIR "Debug/")
		set(RELEASE_DIR "Release/")
	endif()

	add_library(gtest INTERFACE)
	target_link_libraries(gtest INTERFACE debug ${binary_dir}/googlemock/gtest/${DEBUG_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_link_libraries(gtest INTERFACE optimized ${binary_dir}/googlemock/gtest/${RELEASE_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_include_directories(gtest SYSTEM INTERFACE ${GTEST_INCLUDE_DIR})
	add_dependencies(gtest googletest)

	add_library(gtest_main INTERFACE)
	target_link_libraries(gtest_main INTERFACE debug ${binary_dir}/googlemock/gtest/${DEBUG_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gtest_main${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_link_libraries(gtest_main INTERFACE optimized ${binary_dir}/googlemock/gtest/${RELEASE_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gtest_main${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_include_directories(gtest_main SYSTEM INTERFACE ${GTEST_INCLUDE_DIR})
	add_dependencies(gtest_main googletest)

	add_library(gmock INTERFACE)
	target_link_libraries(gmock INTERFACE debug ${binary_dir}/googlemock/${DEBUG_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gmock${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_link_libraries(gmock INTERFACE optimized ${binary_dir}/googlemock/${RELEASE_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gmock${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_include_directories(gmock SYSTEM INTERFACE ${GTEST_INCLUDE_DIR} ${GMOCK_INCLUDE_DIR})
	add_dependencies(gmock googletest)

	add_library(gmock_main INTERFACE)
	target_link_libraries(gmock_main INTERFACE debug ${binary_dir}/googlemock/${DEBUG_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gmock_main${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_link_libraries(gmock_main INTERFACE optimized ${binary_dir}/googlemock/${RELEASE_DIR}${CMAKE_FIND_LIBRARY_PREFIXES}gmock_main${CMAKE_STATIC_LIBRARY_SUFFIX} ${CMAKE_THREAD_LIBS_INIT})
	target_include_directories(gmock_main SYSTEM INTERFACE ${GTEST_INCLUDE_DIR} ${GMOCK_INCLUDE_DIR})
	add_dependencies(gmock_main googletest)

endif()
