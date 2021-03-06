project(freesound)
cmake_minimum_required(VERSION 2.8.10)

# This is required to ensure the correct node modules are included in the project
execute_process(COMMAND unity-js-scopes-tool install ${CMAKE_SOURCE_DIR}/src/node_modules unity-js-scopes)
add_custom_target(build_modules ALL COMMAND unity-js-scopes-tool build ${CMAKE_SOURCE_DIR}/src/node_modules)

# Do not remove this, its required for the correct functionality of the Ubuntu-SDK
set(UBUNTU_MANIFEST_PATH "manifest.json.in" CACHE INTERNAL "Tells QtCreator location and name of the manifest file")
set(UBUNTU_PROJECT_TYPE "Scope" CACHE INTERNAL "Tells QtCreator this is a Scope project")

# Important project paths
set(CMAKE_INSTALL_PREFIX /)
set(SCOPE_INSTALL_DIR "/freesound")
set(GETTEXT_PACKAGE "freesound")
set(PACKAGE_NAME "freesound.rgsilva")
set(SCOPE_NAME "${PACKAGE_NAME}_freesound")

# This command figures out the target architecture and puts it into the manifest file
execute_process(
  COMMAND dpkg-architecture -qDEB_HOST_ARCH
  OUTPUT_VARIABLE CLICK_ARCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Install the manifest and apparmor files
configure_file(
    manifest.json.in
    ${CMAKE_CURRENT_BINARY_DIR}/manifest.json
)

#configure_file(
#    application.in
#    freesound.application
#)

#configure_file(
#    service.in
#    freesound.service
#)

#configure_file(
#    provider.in
#    freesound.provider
#)

install(
    FILES
        "freesound.apparmor"
        #"freesound-account.apparmor"
        "${CMAKE_CURRENT_BINARY_DIR}/manifest.json"
        #"${CMAKE_CURRENT_BINARY_DIR}/freesound.service"
        #"${CMAKE_CURRENT_BINARY_DIR}/freesound.application"
        #"${CMAKE_CURRENT_BINARY_DIR}/freesound.provider"
    DESTINATION ${CMAKE_INSTALL_PREFIX}
)

#install(
#    DIRECTORY "freesound-qmlplugin"
#    DESTINATION ${CMAKE_INSTALL_PREFIX}
#)

# Make these files show up in QtCreator
file(GLOB_RECURSE
  _PO_JS_FILES
  "po/*.po"
  "*.js"
)
add_custom_target(hidden_files
  ALL
  SOURCES
    manifest.json.in
    freesound.apparmor
    freesound-account.apparmor
    service.in
    application.in
    provider.in
    freesound-qmlplugin/Main.qml
    src/data/freesound.ini.in
    src/data/freesound-settings.ini.in
    po/POTFILES.in.in
    po/${GETTEXT_PACKAGE}.pot
    ${_PO_JS_FILES}
)

# Search for our dependencies
find_package(Intltool)

# Add our main directories
add_subdirectory(po)
add_subdirectory(src)

