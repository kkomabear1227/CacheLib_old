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

add_thrift_file(BLOOM BloomFilter.thrift frozen2)

add_library (cachelib_common
  BloomFilter.cpp
  Cohort.cpp
  FurcHash.cpp
  CountDownLatch.cpp
  ${BLOOM_THRIFT_FILES}
  hothash/HotHashDetector.cpp
  hothash/HotHashDetectorTest.cpp
  PercentileStats.cpp
  PeriodicWorker.cpp
  piecewise/GenericPieces.cpp
  piecewise/GenericPiecesTest.cpp
  piecewise/RequestRange.cpp
  piecewise/RequestRangeTest.cpp
  Serialization.cpp
  TestUtils.cpp
  Utils.cpp
)
add_dependencies(cachelib_common thrift_generated_files)

target_link_libraries(cachelib_common PUBLIC
  Threads::Threads
  FBThrift::thriftcpp2
  FBThrift::thriftprotocol
  Folly::folly
  Folly::follybenchmark
  Folly::folly_exception_tracer
  Folly::folly_exception_tracer_base
  Folly::folly_exception_counter
)

install(TARGETS cachelib_common
        EXPORT cachelib-exports
        DESTINATION ${LIB_INSTALL_DIR} )


if (BUILD_TESTS)
  add_library (common_test_support INTERFACE)
  target_link_libraries (common_test_support INTERFACE
    cachelib_common
    gflags
    GTest::gtest
    GTest::gtest_main
  )

  function (ADD_TEST SOURCE_FILE)
     # Add any additional libraries BEFORE the "common_test_support" to ensure
     # that the linking ordering is correct (e.g. if any of them requires
     # "gflags", gflags should appear AFTER it).
     generic_add_test("common-test" "${SOURCE_FILE}"
                      "${ARGN}" common_test_support)
  endfunction()

  add_test (tests/AccessTrackerTest.cpp)
  # need allocator/memory/tests/TestBase.cpp:
  #add_test (tests/ApproxSplitSetTest.cpp allocator_test_support)
  add_test (tests/BloomFilterTest.cpp)
  add_test (tests/BytesEqualTest.cpp)
  add_test (tests/CohortTests.cpp)
  add_test (tests/CounterTests.cpp)
  add_test (tests/CountMinSketchTest.cpp)
  add_test (tests/EventInterfaceTest.cpp cachelib_allocator)
  add_test (tests/HashTests.cpp)
  add_test (tests/IteratorsTests.cpp)
  add_test (tests/MutexTests.cpp)
  add_test (tests/PeriodicWorkerTest.cpp)
  add_test (tests/SerializationTest.cpp cachelib_allocator)
  add_test (tests/UtilTests.cpp)
  add_test (tests/CountDownLatchTest.cpp)
  add_test (tests/UtilTestsRSS.cpp)
  add_test (tests/TimeTests.cpp)
endif()
