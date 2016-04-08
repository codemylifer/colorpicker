SET( DEFINE
)
SET( INCLUDE
photoshop
pica_sp
)
SET( LINK
photoshop
pica_sp
)

create_project(STATIC "${DEFINE}" "${INCLUDE}" "${LINK}")
