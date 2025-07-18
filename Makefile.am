##
## Copyright (C) 2019 Red Hat, Inc.
##
## This program is free software: you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published by
## the Free Software Foundation, either version 2 of the License, or
## (at your option) any later version.
##
## This program is distributed in the hope that it will be useful,
## but WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
## GNU General Public License for more details.
##
## You should have received a copy of the GNU General Public License
## along with this program.  If not, see <https://www.gnu.org/licenses/>.
##
## Author(s): Attila Lakatos <alakatos@redhat.com>
##

ACLOCAL_AMFLAGS = -I m4
CLEANFILES =

bin_PROGRAMS = usbguard-notifier
if NOTIFIER_CLI_ENABLED
bin_PROGRAMS += \
	usbguard-notifier-cli
endif

EXTRA_DIST = \
	src/BuildConfig.h.in \
	README.md \
	LICENSE \
	CHANGELOG.md

usbguard_notifier_SOURCES = \
	src/usbguard-icon.hpp \
	src/Notifier.hpp \
	src/NotifyWrapper.hpp \
	src/Serializer.hpp \
	src/Log.hpp \
	src/Main.cpp \
	src/Notifier.cpp \
	src/NotifyWrapper.cpp \
	src/Serializer.cpp \
	src/Log.cpp

usbguard_notifier_LDFLAGS = \
	@rsvg_LIBS@ \
	@notify_LIBS@ \
	@usbguard_LIBS@

usbguard_notifier_CXXFLAGS = \
	@rsvg_CFLAGS@ \
	@notify_CFLAGS@ \
	@usbguard_CFLAGS@ \
	-fPIC

if CUSTOM_USBGUARD_DEVEL_ENABLED
usbguard_notifier_LDADD = \
	@usbguard_LA@
usbguard_notifier_cli_LDADD = \
	@usbguard_LA@
endif

BUILT_SOURCES = \
	src/BuildConfig.h \
	src/usbguard-icon.hpp

usbguard_notifier_cli_SOURCES = \
	src/Serializer.hpp \
	src/NotifierCLI.hpp \
	src/Serializer.cpp \
	src/NotifierCLI.cpp \
	src/MainCli.cpp

usbguard_notifier_cli_LDFLAGS = \
	@usbguard_LIBS@

usbguard_notifier_cli_CXXFLAGS = \
	@usbguard_CFLAGS@ \
	-fPIC

#
# manual pages
#
SUFFIXES = .adoc

man_ADOC_FILES = \
	$(top_builddir)/man/usbguard-notifier.1

if NOTIFIER_CLI_ENABLED
man_ADOC_FILES += \
	$(top_builddir)/man/usbguard-notifier-cli.1
endif

.adoc:
	$(A2X) -f manpage $^

man1_MANS = \
	$(man_ADOC_FILES)

CLEANFILES += \
	$(man_ADOC_FILES)

EXTRA_DIST += \
	$(man_ADOC_FILES)

#
# usbguard icon
#
EXTRA_DIST += \
	$(top_builddir)/icons/usbguard-icon.svg

$(top_builddir)/src/usbguard-icon.hpp: $(top_builddir)/icons/usbguard-icon.svg
	printf "#ifndef ICON_HPP\n#define ICON_HPP\nnamespace notify {\nconst char *icon =" > $@
	$(SED) 's/"/\\"/g' $^ | $(SED) 's/^/"/' | $(SED) 's/$$/\\n"/' >> $@
	printf ";\n}\n#endif" >> $@

CLEANFILES += \
	$(top_builddir)/src/usbguard-icon.hpp

#
# unit file
#
install-data-hook: install-systemd-service
uninstall-hook: uninstall-systemd-service

CLEANFILES += $(top_builddir)/usbguard-notifier.service

#
# Notifier service
#
EXTRA_DIST += \
	$(top_srcdir)/usbguard-notifier.service.in

usbguard-notifier.service: $(top_srcdir)/usbguard-notifier.service.in
	$(SED) -e "s|%bindir%|${bindir}|" $^ > $@ || rm -f $@

install-systemd-service: $(top_builddir)/usbguard-notifier.service
	$(MKDIR_P) $(DESTDIR)$(SYSTEMD_UNIT_DIR)
	$(INSTALL) -m 644 $(top_builddir)/usbguard-notifier.service \
	$(DESTDIR)$(SYSTEMD_UNIT_DIR)/usbguard-notifier.service

uninstall-systemd-service:
	rm -f $(DESTDIR)$(SYSTEMD_UNIT_DIR)/usbguard-notifier.service

CLEANFILES += $(top_builddir)/usbguard-notifier.service

#
# Common defines
#
$(top_builddir)/src/BuildConfig.h: $(top_builddir)/src/BuildConfig.h.in
	$(SED) -e "s|%NOTIFICATION_PATH%|@NOTIFICATION_PATH@|g" $^ > $@ || rm -f $@
	$(MKDIR_P) @NOTIFICATION_PATH@

CLEANFILES += $(top_builddir)/src/BuildConfig.h
