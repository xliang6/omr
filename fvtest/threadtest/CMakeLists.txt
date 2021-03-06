###############################################################################
# Copyright (c) 2017, 2018 IBM Corp. and others
#
# This program and the accompanying materials are made available under
# the terms of the Eclipse Public License 2.0 which accompanies this
# distribution and is available at http://eclipse.org/legal/epl-2.0
# or the Apache License, Version 2.0 which accompanies this distribution
# and is available at https://www.apache.org/licenses/LICENSE-2.0.
#
# This Source Code may also be made available under the following Secondary
# Licenses when the conditions for such availability set forth in the
# Eclipse Public License, v. 2.0 are satisfied: GNU General Public License,
# version 2 with the GNU Classpath Exception [1] and GNU General Public
# License, version 2 with the OpenJDK Assembly Exception [2].
#
# [1] https://www.gnu.org/software/classpath/license.html
# [2] http://openjdk.java.net/legal/assembly-exception.html
#
# SPDX-License-Identifier: EPL-2.0 OR Apache-2.0 OR GPL-2.0 WITH Classpath-exception-2.0 OR LicenseRef-GPL-2.0 WITH Assembly-exception
###############################################################################

add_executable(omrthreadtest
	abortTest.cpp
	CEnterExit.cpp
	CMonitor.cpp
	createTest.cpp
	CThread.cpp
	joinTest.cpp
	keyDestructorTest.cpp
	lockedMonitorCountTest.cpp
	main.cpp
	ospriority.cpp
	priorityInterruptTest.cpp
	rwMutexTest.cpp
	sanityTest.cpp
	sanityTestHelper.cpp
	threadTestHelp.cpp
)

#TODO Unported makefile fragment:
#ifeq (1,$(OMR_THR_FORK_SUPPORT))
	#OBJECTS += forkResetTest forkResetRWMutexTest
#endif


#ifeq (linux,$(OMR_HOST_OS))
	#MODULE_SHARED_LIBS += rt pthread
#endif
#ifeq (osx,$(OMR_HOST_OS))
	#MODULE_SHARED_LIBS += iconv pthread
#endif
#ifeq (aix,$(OMR_HOST_OS))
	#MODULE_SHARED_LIBS += iconv perfstat
#endif


target_link_libraries(omrthreadtest
	omrtestutil
	omrGtestGlue
	j9hashtable
	omrcore
	omrvmstartup
	${OMR_GC_LIB}
	${OMR_PORT_LIB}
	${OMR_THREAD_LIB}
)
if(OMR_HOST_OS STREQUAL "win")
	target_link_libraries(omrthreadtest
		ws2_32
		shell32
		Iphlpapi
		psapi
		pdh
	)
endif()

if(OMR_HOST_OS STREQUAL "zos")
	target_link_libraries(omrthreadtest j9a2e)
endif()

set_property(TARGET omrthreadtest PROPERTY FOLDER fvtest)

add_test(NAME threadtest COMMAND omrthreadtest)
add_test(NAME threadSetAttrThreadWeightTest COMMAND omrthreadtest --gtest_also_run_disabled_tests --gtest_filter=ThreadCreateTest.DISABLED_SetAttrThreadWeight)
if(OMR_HOST_OS STREQUAL "linux")
	add_test(NAME threadRealtimeTest COMMAND omrthreadtest --gtest_filter=ThreadCreateTest.* -realtime)
endif()
