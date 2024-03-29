# Copyright (c) Facebook, Inc. and its affiliates.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

add_library (compact_cache_test_support INTERFACE)
add_dependencies(compact_cache_test_support INTERFACE thrift_generated_files)

if (BUILD_TESTS)
  target_link_libraries (compact_cache_test_support INTERFACE
    cachelib_allocator
    glog::glog
    gflags
    GTest::gtest
    GTest::gmock
  )

  function (ADD_TEST SOURCE_FILE)
     generic_add_test("compact-cache-test" "${SOURCE_FILE}"
                      compact_cache_test_support "${ARGN}")
  endfunction()

  add_test (tests/CCacheTests.cpp)
endif()
