cmake_minimum_required(VERSION 3.16)

project(kgamma)
set(PROJECT_VERSION "6.3.6")

set(QT_MIN_VERSION "6.7.0")
set(KF6_MIN_VERSION "6.10.0")
set(KDE_COMPILERSETTINGS_LEVEL "5.82")

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(ECM ${KF6_MIN_VERSION} REQUIRED NO_MODULE)
set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH}  ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

include(KDEInstallDirs)
include(KDECMakeSettings)
include(KDECompilerSettings NO_POLICY_SCOPE)
include(FeatureSummary)
include(KDEClangFormat)
include(KDEGitCommitHooks)
include(ECMDeprecationSettings)

option(BUILD_DOC "Whether to build the documentation" ON)

find_package(Qt6 ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS Core Gui Widgets)

find_package(KF6 ${KF6_MIN_VERSION} REQUIRED COMPONENTS
    Config
    ConfigWidgets
    I18n
    KCMUtils
)

if (BUILD_DOC)
    find_package(KF6DocTools ${KF6_MIN_VERSION})
    set_package_properties(KF6DocTools PROPERTIES
        DESCRIPTION "Tools to generate documentation"
        TYPE REQUIRED
    )
endif()

find_package(X11 REQUIRED COMPONENTS xf86vmode)

# X11_xf86vmode discovery is done by FindX11
add_feature_info("X11_xf86vmode" X11_xf86vmode_FOUND "Required to build kgamma.")

include_directories(${CMAKE_CURRENT_BINARY_DIR})

ecm_set_disabled_deprecation_versions(QT 6.8.1
    KF 6.9.0
)


add_subdirectory(kcmkgamma)
# add_subdirectory(xf86gammacfg)

if (BUILD_DOC)
    add_subdirectory(doc)
    kdoctools_install(po)
endif()

# add clang-format target for all our real source files
file(GLOB_RECURSE ALL_CLANG_FORMAT_SOURCE_FILES *.cpp *.h)
kde_clang_format(${ALL_CLANG_FORMAT_SOURCE_FILES})
kde_configure_git_pre_commit_hook(CHECKS CLANG_FORMAT)

ki18n_install(po)

feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)
