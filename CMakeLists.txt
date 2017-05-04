# libsystemd
# https://www.freedesktop.org/wiki/Software/systemd/
#
# apt-get -b source libsystemd-dev

file(
    GLOB include_files
    src/systemd/_sd-common.h
    src/systemd/sd-id128.h
    src/systemd/sd-journal.h
)
set(systemd_include_files ${include_files} PARENT_SCOPE)

add_definitions(
    -include "${CMAKE_CURRENT_LIST_DIR}/config.h"
    -DCATALOG_DATABASE="/var/lib/systemd/catalog/database"
    -DPACKAGE_STRING="systemd 229"
    -fno-common
    -fvisibility=hidden
    -Wall
    -Wextra
    -Wundef
    -Wformat=2
    -Wformat-security
    -Wformat-nonliteral
    -Wlogical-op
    -Wmissing-include-dirs
    -Wold-style-definition
    -Wpointer-arith
    -Winit-self
    -Wdeclaration-after-statement
    -Wfloat-equal
    -Wsuggest-attribute=noreturn
    -Werror=missing-prototypes
    -Werror=implicit-function-declaration
    -Werror=missing-declarations
    -Werror=return-type
    -Wstrict-prototypes
    -Wredundant-decls
    -Wmissing-noreturn
    -Wshadow
    -Wendif-labels
    -Wstrict-aliasing=2
    -Wwrite-strings
    -Wno-unused-parameter
    -Wno-missing-field-initializers
    -Wno-unused-result
    -Wno-format-signedness
    -Werror=overflow
    -Wdate-time
    -Wnested-externs
)
include_directories(
    src/basic
    src/systemd
    ${Vendor_INCLUDE_DIRS}
)

#############################################################################
# systemd library
project(systemd)

file(
    GLOB_RECURSE source_files
    src/basic/alloc-util.c
    src/basic/btrfs-util.c
    src/basic/chattr-util.c
    src/basic/conf-files.c
    src/basic/copy.c
    src/basic/dirent-util.c
    src/basic/escape.c
    src/basic/extract-word.c
    src/basic/fd-util.c
    src/basic/fileio.c
    src/basic/fs-util.c
    src/basic/gunicode.c
    src/basic/hash-funcs.c
    src/basic/hashmap.c
    src/basic/hexdecoct.c
    src/basic/hostname-util.c
    src/basic/io-util.c
    src/basic/log.c
    src/basic/mempool.c
    src/basic/mkdir.c
    src/basic/parse-util.c
    src/basic/path-util.c
    src/basic/prioq.c
    src/basic/proc-cmdline.c
    src/basic/process-util.c
    src/basic/random-util.c
    src/basic/replace-var.c
    src/basic/selinux-util.c
    src/basic/sigbus.c
    src/basic/siphash24.c
    src/basic/signal-util.c
    src/basic/smack-util.c
    src/basic/socket-util.c
    src/basic/stat-util.c
    src/basic/strbuf.c
    src/basic/string-table.c
    src/basic/string-util.c
    src/basic/strv.c
    src/basic/syslog-util.c
    src/basic/terminal-util.c
    src/basic/time-util.c
    src/basic/user-util.c
    src/basic/utf8.c
    src/basic/util.c
    src/basic/virt.c
    src/basic/xattr-util.c
    src/journal/compress.c
    src/journal/catalog.c
    # src/journal/fsprg.c
    # src/journal/journal-authenticate.c
    src/journal/journal-file.c
    src/journal/lookup3.c
    src/journal/mmap-cache.c
    src/journal/sd-journal.c
    src/libsystemd/sd-daemon/sd-daemon.c
    src/libsystemd/sd-event/sd-event.c
    src/libsystemd/sd-id128/sd-id128.c
)

add_library(
    ${PROJECT_NAME} SHARED
    ${source_files}
)
target_link_libraries(
    ${PROJECT_NAME}
    xz
)

# inject config.h dependency
set_source_files_properties(
    ${source_files}
    PROPERTIES OBJECT_DEPENDS "${CMAKE_CURRENT_LIST_DIR}/config.h"
)

# copy shared library to executable output path
add_custom_command(
    TARGET systemd
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:${PROJECT_NAME}> ${EXECUTABLE_OUTPUT_PATH}
)
