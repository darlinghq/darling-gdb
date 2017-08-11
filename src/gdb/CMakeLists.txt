project(gdb)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wno-format-security -Wno-enum-conversion -Wno-pointer-sign -Wno-incompatible-function-pointer-types -Wno-format -Wno-switch")

add_definitions(
	-DWITH_CFM=0
	-DUSE_PTHREADS=1
	-D_DARWIN_UNLIMITED_STREAMS
	-D__MigTypeCheck=1
	-DTARGET_I386
	-DLOCALEDIR="/usr/share/locale"
	-DNO_SECURITY_YET
)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_SOURCE_DIR}/macosx
	${CMAKE_CURRENT_SOURCE_DIR}/config
	${CMAKE_CURRENT_SOURCE_DIR}/../bfd
	${CMAKE_CURRENT_SOURCE_DIR}/../libiberty
	${CMAKE_CURRENT_SOURCE_DIR}/../intl
	${CMAKE_CURRENT_SOURCE_DIR}/../opcodes
	${CMAKE_CURRENT_SOURCE_DIR}/../readline
	${CMAKE_CURRENT_SOURCE_DIR}/../include
	${CMAKE_CURRENT_SOURCE_DIR}/..
	${CMAKE_CURRENT_BINARY_DIR}
)

mig(macosx/macosx.defs)

bison_target(GdbParser1 ada-exp.y ${CMAKE_CURRENT_BINARY_DIR}/ada-exp.c)
bison_target(GdbParser2 c-exp.y ${CMAKE_CURRENT_BINARY_DIR}/c-exp.c)
bison_target(GdbParser3 cp-name-parser.y ${CMAKE_CURRENT_BINARY_DIR}/cp-name-parser.c)
bison_target(GdbParser4 f-exp.y ${CMAKE_CURRENT_BINARY_DIR}/f-exp.c)
bison_target(GdbParser5 jv-exp.y ${CMAKE_CURRENT_BINARY_DIR}/jv-exp.c)
bison_target(GdbParser6 m2-exp.y ${CMAKE_CURRENT_BINARY_DIR}/m2-exp.c)
bison_target(GdbParser7 objc-exp.y ${CMAKE_CURRENT_BINARY_DIR}/objc-exp.c)
bison_target(GdbParser8 p-exp.y ${CMAKE_CURRENT_BINARY_DIR}/p-exp.c)

flex_target(GdbScanner1 ada-lex.l ${CMAKE_CURRENT_BINARY_DIR}/ada-lex.c)

set_source_files_properties(${BISON_GdbParser1_OUTPUTS} PROPERTIES OBJECT_DEPENDS ${FLEX_GdbScanner1_OUTPUTS})

set(libgdb_srcs
	${CMAKE_CURRENT_BINARY_DIR}/macosx/macosxServer.c
	${CMAKE_CURRENT_BINARY_DIR}/macosx/macosxUser.c
	${BISON_GdbParser1_OUTPUTS}
	${BISON_GdbParser2_OUTPUTS}
	${BISON_GdbParser3_OUTPUTS}
	${BISON_GdbParser4_OUTPUTS}
	${BISON_GdbParser5_OUTPUTS}
	${BISON_GdbParser6_OUTPUTS}
	${BISON_GdbParser7_OUTPUTS}
	${BISON_GdbParser8_OUTPUTS}

	version.c
	annotate.c
	auxv.c
	bfd-target.c
	blockframe.c breakpoint.c findvar.c regcache.c
	charset.c disasm.c dummy-frame.c
	source.c value.c eval.c valops.c valarith.c valprint.c printcmd.c
	block.c symtab.c symfile.c symmisc.c linespec.c dictionary.c
	infcall.c
	infcmd.c infrun.c
	inlining.c
	expprint.c environ.c stack.c thread.c
	exceptions.c
	inf-child.c
	interps.c
	main.c
	macrotab.c macrocmd.c macroexp.c macroscope.c
	event-loop.c event-top.c inf-loop.c completer.c
	gdbarch.c arch-utils.c gdbtypes.c osabi.c copying.c
	memattr.c mem-break.c target.c parse.c language.c buildsym.c
	std-regs.c
	signals/signals.c
	kod.c kod-cisco.c
	gdb-events.c
	exec.c bcache.c objfiles.c observer.c minsyms.c maint.c demangle.c
	dbxread.c coffread.c coff-pe-read.c elfread.c
	dwarfread.c dwarf2read.c mipsread.c stabsread.c corefile.c
	dwarf2expr.c dwarf2loc.c dwarf2-frame.c
	ada-lang.c c-lang.c f-lang.c objc-lang.c
	ui-out.c cli-out.c
	varobj.c wrapper.c
	jv-lang.c jv-valprint.c jv-typeprint.c
	m2-lang.c p-lang.c p-typeprint.c p-valprint.c
	scm-exp.c scm-lang.c scm-valprint.c
	sentinel-frame.c
	complaints.c typeprint.c
	ada-typeprint.c c-typeprint.c f-typeprint.c m2-typeprint.c
	ada-valprint.c c-valprint.c cp-valprint.c f-valprint.c m2-valprint.c
	nlmread.c serial.c mdebugread.c top.c utils.c
	ui-file.c
	user-regs.c
	frame.c frame-unwind.c doublest.c
	frame-base.c
	gnu-v2-abi.c gnu-v3-abi.c hpacc-abi.c cp-abi.c cp-support.c
	cp-namespace.c
	reggroups.c regset.c
	trad-frame.c
	tramp-frame.c
	fix-and-continue.c
	checkpoint.c
	solib.c solib-null.c
	x86-shared-tdep.c
	inflow.c

	macosx/macosx-nat-cmds-load.c
	macosx/macosx-xdep.c
	macosx/macosx-nat-watchpoint.c
	macosx/macosx-nat-dyld.c
	macosx/macosx-nat-dyld-path.c
	macosx/macosx-nat-dyld-info.c
	macosx/macosx-nat-dyld-process.c
	macosx/macosx-nat-dyld-io.c
	macosx/macosx-nat-inferior.c
	macosx/macosx-nat-inferior-stubs.c
	macosx/macosx-nat-inferior-util.c
	macosx/macosx-nat-inferior-debug.c
	macosx/macosx-nat-sigthread.c
	macosx/macosx-nat-excthread.c
	macosx/macosx-nat-mutils.c
	macosx/macosx-nat-threads.c
	macosx/macosx-nat-infthread.c
	macosx/macosx-nat-info.c
	macosx/macosx-nat.c
	macosx/darwin-kernel.c

	macosx/i386-macosx-nat-exec.c
	i386-nat.c
	fork-child.c
	inftarg.c
	amd64-tdep.c
	macosx/core-macho.c
	i386-tdep.c
	i387-tdep.c
	macosx/i386-macosx-tdep.c
	macosx/remote-kdp.c
	macosx/kdp-udp.c
	macosx/kdp-transactions.c
	macosx/kdp-protocol.c
	macosx/remote-mobile.c
	macosx/macosx-tdep.c
	macosx/machoread.c
	macosx/macosx-nat-utils.c
	macosx/symread.c
	macosx/pefread.c

	ser-base.c ser-unix.c ser-pipe.c ser-tcp.c
	remote.c dcache.c remote-utils.c tracepoint.c ax-general.c ax-gdb.c remote-fileio.c
	init.c
)

add_library(libgdb OBJECT ${libgdb_srcs})

set(gdb_srcs
	gdb.c
	cli/cli-dump.c
	cli/cli-decode.c
	cli/cli-script.c
	cli/cli-cmds.c
	cli/cli-setshow.c
	cli/cli-utils.c
	cli/cli-logging.c
	cli/cli-interp.c
	mi/mi-out.c
	mi/mi-console.c
	mi/mi-cmds.c
	mi/mi-cmd-env.c
	mi/mi-cmd-var.c
	mi/mi-cmd-break.c
	mi/mi-cmd-stack.c
	mi/mi-cmd-file.c
	mi/mi-cmd-disas.c
	mi/mi-symbol-cmds.c
	mi/mi-interp.c
	mi/mi-main.c
	mi/mi-parse.c
	mi/mi-getopt.c
	mi/mi-common.c

	$<TARGET_OBJECTS:libiberty>
	$<TARGET_OBJECTS:opcodes>
	$<TARGET_OBJECTS:bfd>
	$<TARGET_OBJECTS:libintl>
	$<TARGET_OBJECTS:libreadline>
	$<TARGET_OBJECTS:libgdb>
)

add_darling_executable(gdb ${gdb_srcs})
target_link_libraries(gdb iconv sqlite3 ncurses DebugSymbols ${CoreFoundation})

install(TARGETS gdb DESTINATION libexec/darling/usr/bin)


