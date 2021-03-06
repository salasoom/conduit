###############################################################################
# Copyright (c) 2014-2019, Lawrence Livermore National Security, LLC.
# 
# Produced at the Lawrence Livermore National Laboratory
# 
# LLNL-CODE-666778
# 
# All rights reserved.
# 
# This file is part of Conduit. 
# 
# For details, see: http://software.llnl.gov/conduit/.
# 
# Please also read conduit/LICENSE
# 
# Redistribution and use in source and binary forms, with or without 
# modification, are permitted provided that the following conditions are met:
# 
# * Redistributions of source code must retain the above copyright notice, 
#   this list of conditions and the disclaimer below.
# 
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the disclaimer (as noted below) in the
#   documentation and/or other materials provided with the distribution.
# 
# * Neither the name of the LLNS/LLNL nor the names of its contributors may
#   be used to endorse or promote products derived from this software without
#   specific prior written permission.
# 
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL LAWRENCE LIVERMORE NATIONAL SECURITY,
# LLC, THE U.S. DEPARTMENT OF ENERGY OR CONTRIBUTORS BE LIABLE FOR ANY
# DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL 
# DAMAGES  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
# OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, 
# STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING
# IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE 
# POSSIBILITY OF SUCH DAMAGE.
# 
###############################################################################

################################
# Conduit Unit Tests
################################

################################
# conduit relay Unit Tests
################################
set(RELAY_TESTS t_relay_smoke
                t_relay_io_basic
                t_relay_io_file_sizes
                t_relay_io_handle
                t_relay_node_viewer
                t_relay_websocket)

################################
# Optional Unit Tests
################################
set(RELAY_MPI_TESTS      t_relay_mpi_smoke t_relay_mpi_test)
set(RELAY_SILO_TESTS     t_relay_io_silo)

set(RELAY_HDF5_TESTS t_relay_io_hdf5
                     t_relay_io_hdf5_read_and_print
                     t_relay_io_hdf5_slab
                     t_relay_io_hdf5_opts)

set(RELAY_ADIOS_TESTS t_relay_io_adios)
set(RELAY_MPI_ADIOS_TESTS t_relay_mpi_io_adios)
set(RELAY_ZFP_TESTS t_relay_zfp)

################################
# Add our main tests
################################

message(STATUS "Adding conduit_relay unit tests")
foreach(TEST ${RELAY_TESTS})
    add_cpp_test(TEST ${TEST} DEPENDS_ON conduit conduit_relay )
endforeach()


################################
# Add c interface tests
################################
add_subdirectory("c")

################################
# Add our Optional Unit Tests
################################

if(HDF5_FOUND)
    foreach(TEST ${RELAY_HDF5_TESTS})
        add_cpp_test(TEST ${TEST} DEPENDS_ON conduit conduit_relay)
    endforeach()
else()
    message(STATUS "HDF5 disabled: Skipping conduit_relay hdf5 tests")
endif()


if(SILO_FOUND)
    message(STATUS "Silo enabled: Adding conduit_relay Silo unit tests")
    foreach(TEST ${RELAY_SILO_TESTS})
        add_cpp_test(TEST ${TEST} DEPENDS_ON conduit conduit_relay)
    endforeach()
else()
    message(STATUS "Silo disabled: Skipping conduit_relay silo tests")
endif()

if(ADIOS_FOUND)
    message(STATUS "ADIOS enabled: Adding conduit_relay ADIOS unit tests")
    foreach(TEST ${RELAY_ADIOS_TESTS})
        add_cpp_test(TEST ${TEST} DEPENDS_ON conduit conduit_relay)
    endforeach()

    if(MPI_FOUND)
        message(STATUS "MPI enabled: Adding conduit_relay_mpi_adios unit tests")
        foreach(TEST ${RELAY_MPI_ADIOS_TESTS})
            add_cpp_mpi_test(TEST ${TEST} NUM_MPI_TASKS 2 DEPENDS_ON conduit conduit_relay_mpi_io) 
        endforeach()
    endif()
else()
    message(STATUS "ADIOS disabled: Skipping conduit_relay ADIOS tests")
endif()


if(MPI_FOUND)
    message(STATUS "MPI enabled: Adding conduit_relay_mpi unit tests")
    foreach(TEST ${RELAY_MPI_TESTS})
        add_cpp_mpi_test(TEST ${TEST} NUM_MPI_TASKS 2 DEPENDS_ON conduit conduit_relay_mpi) 
    endforeach()
else()
    message(STATUS "MPI disabled: Skipping conduit_relay_mpi tests")
endif()


if(ZFP_FOUND)
    message(STATUS "ZFP enabled: Adding conduit_relay_zfp unit tests")
    foreach(TEST ${RELAY_ZFP_TESTS})
        add_cpp_test(TEST ${TEST} DEPENDS_ON conduit conduit_relay)
    endforeach()
else()
    message(STATUS "ZFP disabled: Skipping conduit_relay_zfp tests")
endif()


################################
# Add optional tests
################################
if(PYTHON_FOUND)
    add_subdirectory("python")
else()
    message(STATUS "Python disabled: Skipping conduit relay python module tests")
endif()


if(FORTRAN_FOUND)
    add_subdirectory("fortran")
else()
    message(STATUS "Fortran disabled: Skipping conduit relay fortran interface tests")
endif()



