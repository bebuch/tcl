cmake_minimum_required(VERSION 3.20)

# Extract TCL version from header
set(TCL_HEADER_PATH "tcl/generic/tcl.h")
file(READ "${TCL_HEADER_PATH}" TCL_HEADER_CONTENTS)
set(REGEX_STRING "#define TCL_PATCH_LEVEL[ \t]+\"([0-9]+\\.[0-9]+\\.[0-9]+)\"")
string(REGEX MATCH "${REGEX_STRING}" MATCHED_LINE "${TCL_HEADER_CONTENTS}")
if(MATCHED_LINE)
    string(REGEX REPLACE "${REGEX_STRING}" "\\1" TCL_PATCH_LEVEL "${MATCHED_LINE}")
    message(STATUS "Extracted TCL_PATCH_LEVEL: ${TCL_PATCH_LEVEL}")
else()
    message(FATAL_ERROR "TCL_PATCH_LEVEL not found in ${TCL_HEADER_PATH}")
endif()

project("tcl" VERSION "${TCL_PATCH_LEVEL}" LANGUAGES C)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(zlib CONFIG REQUIRED)

include(GNUInstallDirs)

if(UNIX)
    set(OS_SPECIFIC
        "tcl/unix/tclAppInit.c"
        "tcl/unix/tclLoadAix.c"
        "tcl/unix/tclLoadDl.c"
        "tcl/unix/tclLoadDyld.c"
        "tcl/unix/tclLoadNext.c"
        "tcl/unix/tclLoadOSF.c"
        "tcl/unix/tclLoadShl.c"
        "tcl/unix/tclUnixChan.c"
        "tcl/unix/tclUnixCompat.c"
        "tcl/unix/tclUnixEvent.c"
        "tcl/unix/tclUnixFCmd.c"
        "tcl/unix/tclUnixFile.c"
        "tcl/unix/tclUnixInit.c"
        "tcl/unix/tclUnixNotfy.c"
        "tcl/unix/tclUnixPipe.c"
        "tcl/unix/tclUnixPort.h"
        "tcl/unix/tclUnixSock.c"
        "tcl/unix/tclUnixTest.c"
        "tcl/unix/tclUnixThrd.c"
        "tcl/unix/tclUnixThrd.h"
        "tcl/unix/tclUnixTime.c"
        "tcl/unix/tclXtNotify.c"
        "tcl/unix/tclXtTest.c")
else()
    file(READ "tcl/generic/tclMain.c" content)
    string(PREPEND content "#define UNICODE\n#define _UNICODE\n")
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/tclMainW.c" "${content}")

    set(OS_SPECIFIC
        "tcl/win/tclWin32Dll.c"
        "tcl/win/tclWinChan.c"
        "tcl/win/tclWinConsole.c"
        "tcl/win/tclWinError.c"
        "tcl/win/tclWinFCmd.c"
        "tcl/win/tclWinFile.c"
        "tcl/win/tclWinInit.c"
        "tcl/win/tclWinLoad.c"
        "tcl/win/tclWinNotify.c"
        "tcl/win/tclWinPipe.c"
        "tcl/win/tclWinSerial.c"
        "tcl/win/tclWinSock.c"
        "tcl/win/tclWinThrd.c"
        "tcl/win/tclWinTime.c"
        "tcl/win/tcl.rc"
        "${CMAKE_CURRENT_BINARY_DIR}/tclMainW.c")
endif()

add_library(Tcl SHARED
    "tcl/libtommath/bn_mp_add.c"
    "tcl/libtommath/bn_mp_add_d.c"
    "tcl/libtommath/bn_mp_and.c"
    "tcl/libtommath/bn_mp_clamp.c"
    "tcl/libtommath/bn_mp_clear.c"
    "tcl/libtommath/bn_mp_clear_multi.c"
    "tcl/libtommath/bn_mp_cmp.c"
    "tcl/libtommath/bn_mp_cmp_d.c"
    "tcl/libtommath/bn_mp_cmp_mag.c"
    "tcl/libtommath/bn_mp_cnt_lsb.c"
    "tcl/libtommath/bn_mp_copy.c"
    "tcl/libtommath/bn_mp_count_bits.c"
    "tcl/libtommath/bn_mp_div.c"
    "tcl/libtommath/bn_mp_div_d.c"
    "tcl/libtommath/bn_mp_div_2.c"
    "tcl/libtommath/bn_mp_div_2d.c"
    "tcl/libtommath/bn_s_mp_div_3.c"
    "tcl/libtommath/bn_mp_exch.c"
    "tcl/libtommath/bn_mp_expt_n.c"
    "tcl/libtommath/bn_mp_grow.c"
    "tcl/libtommath/bn_mp_init.c"
    "tcl/libtommath/bn_mp_init_copy.c"
    "tcl/libtommath/bn_mp_init_multi.c"
    "tcl/libtommath/bn_mp_init_set.c"
    "tcl/libtommath/bn_mp_init_size.c"
    "tcl/libtommath/bn_mp_lshd.c"
    "tcl/libtommath/bn_mp_mod.c"
    "tcl/libtommath/bn_mp_mod_2d.c"
    "tcl/libtommath/bn_mp_mul.c"
    "tcl/libtommath/bn_mp_mul_2.c"
    "tcl/libtommath/bn_mp_mul_2d.c"
    "tcl/libtommath/bn_mp_mul_d.c"
    "tcl/libtommath/bn_mp_neg.c"
    "tcl/libtommath/bn_mp_or.c"
    "tcl/libtommath/bn_mp_pack.c"
    "tcl/libtommath/bn_mp_pack_count.c"
    "tcl/libtommath/bn_mp_radix_size.c"
    "tcl/libtommath/bn_mp_radix_smap.c"
    "tcl/libtommath/bn_mp_read_radix.c"
    "tcl/libtommath/bn_mp_rshd.c"
    "tcl/libtommath/bn_mp_set.c"
    "tcl/libtommath/bn_mp_shrink.c"
    "tcl/libtommath/bn_mp_sqr.c"
    "tcl/libtommath/bn_mp_sqrt.c"
    "tcl/libtommath/bn_mp_sub.c"
    "tcl/libtommath/bn_mp_sub_d.c"
    "tcl/libtommath/bn_mp_signed_rsh.c"
    "tcl/libtommath/bn_mp_to_ubin.c"
    "tcl/libtommath/bn_mp_to_radix.c"
    "tcl/libtommath/bn_mp_ubin_size.c"
    "tcl/libtommath/bn_mp_unpack.c"
    "tcl/libtommath/bn_mp_xor.c"
    "tcl/libtommath/bn_mp_zero.c"
    "tcl/libtommath/bn_s_mp_add.c"
    "tcl/libtommath/bn_s_mp_balance_mul.c"
    "tcl/libtommath/bn_s_mp_karatsuba_mul.c"
    "tcl/libtommath/bn_s_mp_karatsuba_sqr.c"
    "tcl/libtommath/bn_s_mp_mul_digs.c"
    "tcl/libtommath/bn_s_mp_mul_digs_fast.c"
    "tcl/libtommath/bn_s_mp_reverse.c"
    "tcl/libtommath/bn_s_mp_sqr.c"
    "tcl/libtommath/bn_s_mp_sqr_fast.c"
    "tcl/libtommath/bn_s_mp_sub.c"
    "tcl/libtommath/bn_s_mp_toom_sqr.c"
    "tcl/libtommath/bn_s_mp_toom_mul.c"
    "tcl/generic/regcomp.c"
    "tcl/generic/regerror.c"
    "tcl/generic/regexec.c"
    "tcl/generic/regfree.c"
    "tcl/generic/tclAlloc.c"
    "tcl/generic/tclAssembly.c"
    "tcl/generic/tclAsync.c"
    "tcl/generic/tclBasic.c"
    "tcl/generic/tclBinary.c"
    "tcl/generic/tclCkalloc.c"
    "tcl/generic/tclClock.c"
    "tcl/generic/tclCmdAH.c"
    "tcl/generic/tclCmdIL.c"
    "tcl/generic/tclCmdMZ.c"
    "tcl/generic/tclCompCmds.c"
    "tcl/generic/tclCompCmdsGR.c"
    "tcl/generic/tclCompCmdsSZ.c"
    "tcl/generic/tclCompExpr.c"
    "tcl/generic/tclCompile.c"
    "tcl/generic/tclConfig.c"
    "tcl/generic/tclDate.c"
    "tcl/generic/tclDictObj.c"
    "tcl/generic/tclDisassemble.c"
    "tcl/generic/tclEncoding.c"
    "tcl/generic/tclEnsemble.c"
    "tcl/generic/tclEnv.c"
    "tcl/generic/tclEvent.c"
    "tcl/generic/tclExecute.c"
    "tcl/generic/tclFCmd.c"
    "tcl/generic/tclFileName.c"
    "tcl/generic/tclGet.c"
    "tcl/generic/tclHash.c"
    "tcl/generic/tclHistory.c"
    "tcl/generic/tclIndexObj.c"
    "tcl/generic/tclInterp.c"
    "tcl/generic/tclIO.c"
    "tcl/generic/tclIOCmd.c"
    "tcl/generic/tclIOGT.c"
    "tcl/generic/tclIOSock.c"
    "tcl/generic/tclIOUtil.c"
    "tcl/generic/tclIORChan.c"
    "tcl/generic/tclIORTrans.c"
    "tcl/generic/tclLink.c"
    "tcl/generic/tclListObj.c"
    "tcl/generic/tclLiteral.c"
    "tcl/generic/tclLoad.c"
    "tcl/generic/tclMain.c"
    "tcl/generic/tclNamesp.c"
    "tcl/generic/tclNotify.c"
    "tcl/generic/tclOO.c"
    "tcl/generic/tclOOBasic.c"
    "tcl/generic/tclOOCall.c"
    "tcl/generic/tclOODefineCmds.c"
    "tcl/generic/tclOOInfo.c"
    "tcl/generic/tclOOMethod.c"
    "tcl/generic/tclOOStubInit.c"
    "tcl/generic/tclObj.c"
    "tcl/generic/tclOptimize.c"
    "tcl/generic/tclPanic.c"
    "tcl/generic/tclParse.c"
    "tcl/generic/tclPathObj.c"
    "tcl/generic/tclPipe.c"
    "tcl/generic/tclPkg.c"
    "tcl/generic/tclPkgConfig.c"
    "tcl/generic/tclPosixStr.c"
    "tcl/generic/tclPreserve.c"
    "tcl/generic/tclProc.c"
    "tcl/generic/tclRegexp.c"
    "tcl/generic/tclResolve.c"
    "tcl/generic/tclResult.c"
    "tcl/generic/tclScan.c"
    "tcl/generic/tclStringObj.c"
    "tcl/generic/tclStrToD.c"
    "tcl/generic/tclStubInit.c"
    "tcl/generic/tclThread.c"
    "tcl/generic/tclThreadAlloc.c"
    "tcl/generic/tclThreadJoin.c"
    "tcl/generic/tclThreadStorage.c"
    "tcl/generic/tclTimer.c"
    "tcl/generic/tclTomMathInterface.c"
    "tcl/generic/tclTrace.c"
    "tcl/generic/tclUtf.c"
    "tcl/generic/tclUtil.c"
    "tcl/generic/tclVar.c"
    "tcl/generic/tclZlib.c"
    ${OS_SPECIFIC})

target_compile_definitions(Tcl PRIVATE "BUILD_tcl" "HAVE_ZLIB" "MP_FIXED_CUTOFFS")
target_compile_definitions(Tcl PRIVATE
    CFG_INSTALL_LIBDIR="" CFG_INSTALL_BINDIR="" CFG_INSTALL_SCRDIR="" CFG_INSTALL_INCDIR="" CFG_INSTALL_DOCDIR=""
    CFG_RUNTIME_LIBDIR="" CFG_RUNTIME_BINDIR="" CFG_RUNTIME_SCRDIR="" CFG_RUNTIME_INCDIR="" CFG_RUNTIME_DOCDIR="")
target_include_directories(Tcl PRIVATE "tcl/generic" "tcl/libtommath")
if(UNIX)
    target_include_directories(Tcl PRIVATE "tcl/unix")
else()
    target_include_directories(Tcl PRIVATE "tcl/win")
endif()

target_include_directories(Tcl INTERFACE $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
target_link_libraries(Tcl PUBLIC ZLIB::ZLIB)

if(NOT UNIX)
    target_link_libraries(Tcl PUBLIC Netapi32)
endif()

# Install
install(FILES
    "tcl/generic/tcl.h"
    "tcl/generic/tclDecls.h"
    "tcl/generic/tclPlatDecls.h"
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "TclConfigVersion.cmake"
    VERSION "${PACKAGE_VERSION}"
    COMPATIBILITY SameMajorVersion)

configure_file("TclConfig.cmake.in" "TclConfig.cmake" @ONLY)
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/TclConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/TclConfigVersion.cmake"
    DESTINATION "lib/cmake/Tcl")

install(TARGETS Tcl EXPORT TclTargets)
install(EXPORT TclTargets
    FILE "TclTargets.cmake"
    NAMESPACE "Tcl::"
    DESTINATION "lib/cmake/Tcl")
