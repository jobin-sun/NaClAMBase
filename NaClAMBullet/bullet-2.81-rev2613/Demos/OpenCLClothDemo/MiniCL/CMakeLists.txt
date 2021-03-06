

INCLUDE_DIRECTORIES(
${BULLET_PHYSICS_SOURCE_DIR}/src 
${BULLET_PHYSICS_SOURCE_DIR}/Demos/SharedOpenCL
${BULLET_PHYSICS_SOURCE_DIR}/Demos/OpenGL
)

ADD_DEFINITIONS(-DUSE_MINICL)

IF(WIN32)
ADD_DEFINITIONS(-DGLEW_STATIC)
ENDIF(WIN32)

IF (WIN32)
	IF (CMAKE_CL_64)
		SET(CMAK_GLEW_LIBRARY
			${BULLET_PHYSICS_SOURCE_DIR}/Glut/glew64s.lib		)
	ELSE(CMAKE_CL_64)
		SET(CMAK_GLEW_LIBRARY		${BULLET_PHYSICS_SOURCE_DIR}/Glut/glew32s.lib		)
	ENDIF(CMAKE_CL_64)
ENDIF()

IF (USE_GLUT)
	LINK_LIBRARIES(
		OpenGLSupport 
		BulletSoftBodySolvers_OpenCL_Mini
		MiniCL
		BulletMultiThreaded
		BulletSoftBody
		BulletDynamics  
		BulletCollision  
		LinearMath 
		${GLUT_glut_LIBRARY} 
		${OPENGL_gl_LIBRARY} 
		${OPENGL_glu_LIBRARY}
		${CMAK_GLEW_LIBRARY}
		
	)


	ADD_EXECUTABLE(AppOpenCLClothDemo_Mini
		../cl_cloth_demo.cpp
		../gl_win.cpp
		../clstuff.cpp
		../clstuff.h
		../gl_win.h
		${BULLET_PHYSICS_SOURCE_DIR}/src/BulletMultiThreaded/GpuSoftBodySolvers/OpenCL/MiniCL/MiniCLTaskWrap.cpp
					${BULLET_PHYSICS_SOURCE_DIR}/Demos/SharedOpenCL/btOpenCLUtils.cpp
					${BULLET_PHYSICS_SOURCE_DIR}/Demos/SharedOpenCL/btOpenCLUtils.h
					${BULLET_PHYSICS_SOURCE_DIR}/Demos/SharedOpenCL/btOpenCLInclude.h

	)
ELSE (USE_GLUT)
ENDIF (USE_GLUT)

IF(WIN32)
IF (CMAKE_CL_64)
	IF (NOT INTERNAL_CREATE_DISTRIBUTABLE_MSVC_PROJECTFILES)
		ADD_CUSTOM_COMMAND(	TARGET AppOpenCLClothDemo_Mini		POST_BUILD
					COMMAND ${CMAKE_COMMAND} ARGS -E copy_if_different ${BULLET_PHYSICS_SOURCE_DIR}/glut64.dll ${CMAKE_CURRENT_BINARY_DIR}	
					)
	ENDIF()
ELSE(CMAKE_CL_64)
	IF (NOT INTERNAL_CREATE_DISTRIBUTABLE_MSVC_PROJECTFILES)
		ADD_CUSTOM_COMMAND(	TARGET AppOpenCLClothDemo_Mini	POST_BUILD
					COMMAND ${CMAKE_COMMAND} ARGS -E copy_if_different ${BULLET_PHYSICS_SOURCE_DIR}/GLUT32.DLL ${CMAKE_CURRENT_BINARY_DIR}
					)

	ENDIF()
ENDIF(CMAKE_CL_64)
ENDIF(WIN32)

IF(NOT INTERNAL_CREATE_DISTRIBUTABLE_MSVC_PROJECTFILES)
	ADD_CUSTOM_COMMAND(	TARGET AppOpenCLClothDemo_Mini	POST_BUILD
			COMMAND ${CMAKE_COMMAND} ARGS -E copy_if_different ${BULLET_PHYSICS_SOURCE_DIR}/bullet_logo.png ${CMAKE_CURRENT_BINARY_DIR}
			)
ENDIF()
IF (UNIX)
  TARGET_LINK_LIBRARIES(AppOpenCLClothDemo_Mini pthread)
ENDIF(UNIX)


IF (INTERNAL_ADD_POSTFIX_EXECUTABLE_NAMES)
			SET_TARGET_PROPERTIES(AppOpenCLClothDemo_Mini PROPERTIES  DEBUG_POSTFIX "_Debug")
			SET_TARGET_PROPERTIES(AppOpenCLClothDemo_Mini PROPERTIES  MINSIZEREL_POSTFIX "_MinsizeRel")
			SET_TARGET_PROPERTIES(AppOpenCLClothDemo_Mini PROPERTIES  RELWITHDEBINFO_POSTFIX "_RelWithDebugInfo")
ENDIF(INTERNAL_ADD_POSTFIX_EXECUTABLE_NAMES)