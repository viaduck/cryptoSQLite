# Copyright (c) 2017-2020 The ViaDuck Project
#
# This file is part of cryptoSQLite.
#
# cryptoSQLite is free software: you can redistribute it and/or modify
# it under the terms of the GNU Lesser General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# cryptoSQLite is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with cryptoSQLite.  If not, see <http://www.gnu.org/licenses/>.
#

include(ApplyPatches)

# set up cache variables
set(SQLITE_BUILD_VERSION "3350500" CACHE STRING "SQLite version string as used in the URL")
set(SQLITE_BUILD_HASH "b49409ef123e193e719e2536f9b795482a69e61a9cc728933739b9024f035061" CACHE STRING
        "SQLite amalgamation zip sha256 hash used to check the download")

# build url from cache variables
set(SQLITE_URL "https://mirror.viaduck.org/sqlite/sqlite-amalgamation-${SQLITE_BUILD_VERSION}.zip")
set(SQLITE_SUBDIR "sqlite-amalgamation-${SQLITE_BUILD_VERSION}")

# download amalgamation zip with hash check
file(DOWNLOAD
        ${SQLITE_URL}
        ${CMAKE_CURRENT_SOURCE_DIR}/sqlite.zip
        EXPECTED_HASH SHA256=${SQLITE_BUILD_HASH}
        SHOW_PROGRESS
        )

# unpack zip only if needed
if (NOT EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${SQLITE_SUBDIR})
    # unpack the amalgamation zip in current dir
    execute_process(COMMAND ${CMAKE_COMMAND} -E tar x sqlite.zip
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

    # apply our patches to sqlite
    ApplyPatches("${CMAKE_CURRENT_SOURCE_DIR}/patches/*.patch" "${CMAKE_CURRENT_SOURCE_DIR}/${SQLITE_SUBDIR}")
endif()

# find files just downloaded
file(GLOB_RECURSE SQ_FILES ${CMAKE_CURRENT_SOURCE_DIR}/${SQLITE_SUBDIR}/*)

# export variables to parent scope
set(SQLITE_FILES SQ_FILES PARENT_SCOPE)
set(SQLITE_INCLUDES ${CMAKE_CURRENT_SOURCE_DIR}/${SQLITE_SUBDIR}/ PARENT_SCOPE)
