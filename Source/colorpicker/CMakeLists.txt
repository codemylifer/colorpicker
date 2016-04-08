SET( DEFINE
)
SET( INCLUDE
common
photoshop
pica_sp
)
SET( LINK
common
photoshop
pica_sp

odbc32.lib
odbccp32.lib
version.lib
)

create_project(DYNAMIC "${DEFINE}" "${INCLUDE}" "${LINK}")

if(TARGET ${PROJECT_NAME})
	get_target_property(INCLUDE_DIRS ${PROJECT_NAME} INCLUDE_DIRECTORIES)

	foreach(INCLUDE_DIR ${INCLUDE_DIRS})
		string(APPEND args "/I${INCLUDE_DIR} ")
	endforeach()

	file(GLOB_RECURSE MY_R_FILE ${CMAKE_CURRENT_SOURCE_DIR}/*.r)
	file(GLOB_RECURSE PiPLConverter ${CMAKE_SOURCE_DIR}/Cnvtpipl.exe)
	get_filename_component(R_FILE_DIR ${MY_R_FILE} DIRECTORY)
	get_filename_component(R_FILE_NAME ${MY_R_FILE} NAME_WE)
	set(MY_RR_FILE "${R_FILE_DIR}/${R_FILE_NAME}.rr")
	set(MY_PiPL_FILE "${R_FILE_DIR}/${R_FILE_NAME}.pipl")
	
	message("dir: ${FILE_NAME}")
	string(APPEND args "/EP /DMSWindows=1 /Tc\"${MY_R_FILE}\" > \"${MY_RR_FILE}\"")
	#message("args = ${args}")

	add_custom_target(${PROJECT_NAME}PreCompilePiPL ALL
		COMMAND cl ${args}
		COMMAND ${PiPLConverter} ${MY_RR_FILE} ${MY_PiPL_FILE}
		COMMENT "Compiling PiPL resource...")

	add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}PreCompilePiPL)

	if( MSVC )
		SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)
		SET_PROPERTY(TARGET ${PROJECT_NAME}PreCompilePiPL		PROPERTY FOLDER CMakePredefinedTargets)
	endif()
endif()