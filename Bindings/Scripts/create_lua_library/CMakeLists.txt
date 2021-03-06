INCLUDE(FindPythonModule)

FIND_PACKAGE(PythonInterp REQUIRED)
FIND_PYTHON_MODULE(ply REQUIRED)
IF(NOT PY_PLY)
	MESSAGE(SEND_ERROR "The Python PLY (Python Lex-Yacc) module is required to generate Lua bindings.")
ENDIF(NOT PY_PLY)

FILE(GLOB HEADER_DEPENDENCIES_CORE ../../../Core/Contents/Include/*.h)
FILE(GLOB HEADER_DEPENDENCIES_2DPHYSICS ../../../Modules/Contents/2DPhysics/Include/*.h)
FILE(GLOB HEADER_DEPENDENCIES_3DPHYSICS ../../../Modules/Contents/3DPhysics/Include/*.h)
FILE(GLOB HEADER_DEPENDENCIES_UI ../../../Modules/Contents/UI/Include/*.h)

ADD_CUSTOM_COMMAND(
OUTPUT luaGenerateCmd
COMMAND ${PYTHON_EXECUTABLE} create_lua_library.py ../../../Core/Contents/Include Polycode Polycode.h polycore Polycore ../../Contents/LUA/API ../../Contents/LUA/API/Polycode ../../Contents/LUA/Include ../../Contents/LUA/Source
COMMAND ${PYTHON_EXECUTABLE} create_lua_library.py ../../../Modules/Contents/2DPhysics/Include/ Physics2D Polycode2DPhysics.h physics2d Physics2D ../../../Modules/Bindings/2DPhysics/API ../../../Modules/Bindings/2DPhysics/API/Physics2D ../../../Modules/Bindings/2DPhysics/Include ../../../Modules/Bindings/2DPhysics/Source
COMMAND ${PYTHON_EXECUTABLE} create_lua_library.py ../../../Modules/Contents/3DPhysics/Include/ Physics3D Polycode3DPhysics.h physics3d Physics3D ../../../Modules/Bindings/3DPhysics/API ../../../Modules/Bindings/3DPhysics/API/Physics3D ../../../Modules/Bindings/3DPhysics/Include ../../../Modules/Bindings/3DPhysics/Source 
COMMAND ${PYTHON_EXECUTABLE} create_lua_library.py ../../../Modules/Contents/UI/Include/ UI PolycodeUI.h UI UI ../../../Modules/Bindings/UI/API ../../../Modules/Bindings/UI/API/UI ../../../Modules/Bindings/UI/Include ../../../Modules/Bindings/UI/Source
COMMAND ${CMAKE_COMMAND} -E touch ${CMAKE_CURRENT_BINARY_DIR}/luaGenerateCmd # Generate the given output file(empty file), to keep track of whether we need to rebuild.
DEPENDS ${HEADER_DEPENDENCIES_CORE} ${HEADER_DEPENDENCIES_2DPHYSICS} ${HEADER_DEPENDENCIES_3DPHYSICS} ${HEADER_DEPENDENCIES_UI} 
WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)

ADD_CUSTOM_TARGET(PolycodeLuaGenerate 
	DEPENDS Polycore luaGenerateCmd
	COMMENT "Generating Lua API"
	SOURCES create_lua_library.py)

