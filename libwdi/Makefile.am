all-local: libwdi.la
if CREATE_IMPORT_LIB
# Rebuild the import lib from the .def so that MS and MinGW DLLs can be interchanged
	@echo "  CCLD   libwdi.dll.a"
	@$(DLLTOOL) $(DLLTOOLFLAGS) --kill-at --input-def libwdi.def --dllname libwdi.dll --output-lib .libs/libwdi.dll.a
endif

INCLUDES = -I$(top_srcdir)

#BUILT_SOURCES = embedded.h
noinst_PROGRAMS =
noinst_EXES =
bin2coff_OPTIONS =
lib_LTLIBRARIES = libwdi.la
LIB_SRC = resource.h logging.h tokenizer.h installer.h mssign32.h libwdi.h 7z.h logging.c tokenizer.c vid_data.c pki.c libwdi_dlg.c libwdi.c 7z.c

if OPT_M32
noinst_PROGRAMS += installer_x86
noinst_EXES += installer_x86.exe
installer_x86_SOURCES = installer.h installer.c
installer_x86_CFLAGS = -m32 $(AM_CFLAGS)
installer_x86_LDFLAGS = -m32 $(AM_LDFLAGS) -static
installer_x86_LDADD = -lsetupapi -lnewdev -lole32
endif

if OPT_M64
noinst_PROGRAMS += installer_x64
noinst_EXES += installer_x64.exe
installer_x64_SOURCES = installer.h installer.c
installer_x64_CFLAGS = -m64 -D_WIN64 $(AM_CFLAGS)
installer_x64_LDFLAGS = -m64 $(AM_LDFLAGS) -static
installer_x64_LDADD = -lsetupapi -lnewdev -lole32
if !OPT_M32
bin2coff_OPTIONS += 64bit
endif
endif

pkg_v_rc = $(pkg_v_rc_$(V))
pkg_v_rc_ = $(pkg_v_rc_$(AM_DEFAULT_VERBOSITY))
pkg_v_rc_0 = @echo "  RC     $@";
EXTRA_DIST = $(LIB_SRC)

libwdi_rc.lo: libwdi.rc
	$(pkg_v_rc)$(LIBTOOL) $(AM_V_lt) $(AM_LIBTOOLFLAGS) $(LIBTOOLFLAGS) --tag=RC --mode=compile $(RC) $(ARCH_RCFLAGS) -i $< -o $@

wdi_data.lo:
# $* is the target without extension - here 'wdi_data'
	../utilities/bin2coff ../../libwdi-staging/$*.7z $*.o $* $(bin2coff_OPTIONS)
# libtool won't let us get away with a mere .o - we have to generate a .lo wrapper.
# What's more, the first comment from the .lo is mandatory and must contain the libtool version
	@echo "# Generated by $(shell $(LIBTOOL) --version | head -n 1)" > $@
	@echo "pic_object='$*.o'" >> $@
	@echo "non_pic_object='$*.o'" >> $@

libwdi_la_CFLAGS = $(ARCH_CFLAGS) $(VISIBILITY_CFLAGS) $(AM_CFLAGS)
libwdi_la_LDLAGS = $(AM_LDFLAGS)
libwdi_la_LIBADD = -L../common/7z/.libs -L../common/profile/.libs libwdi_rc.lo wdi_data.lo -lsetupapi -lole32 -lversion -l7z -lprofile
libwdi_la_SOURCES = $(LIB_SRC)

#embedded.h: embedder $(noinst_PROGRAMS)
#	@./embedder embedded.h

clean-local:
	-rm -rf embedder embedder.exe bin2coff bin2coff.exe
