# @@@LICENSE
#
#      Copyright (c) 2008-2012 Hewlett-Packard Development Company, L.P.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# LICENSE@@@

cmake_minimum_required(VERSION 2.8.7)

project(webos-shutdownscripts NONE)

include(webOS/webOS)
webos_modules_init(1 0 0 QUALIFIER RC3)
webos_component(2 0 0)

webos_configure_source_files(INITDCONVSCRIPTS
	scripts/public/shutdown-cancel-watchdog.sh
	scripts/public/shutdown-animation
	scripts/public/stop-comms
	scripts/public/stop-fbprogress
	scripts/public/shutdown-watchdog.sh )

webos_configure_source_files(RUNLVLCONVSCRIPTS
	files/launch/runlevel-H-handler
	files/launch/runlevel-U-handler )

install(FILES files/conf/shutdown-watchdog
	DESTINATION ${WEBOS_INSTALL_DEFAULTCONFDIR})

install(PROGRAMS ${RUNLVLCONVSCRIPTS}
	DESTINATION ${WEBOS_INSTALL_UPSTARTCONFDIR})
	# PERMISSIONS OWNER_EXECUTE doesn't work from BitBake

install(PROGRAMS 
	${INITDCONVSCRIPTS}
	scripts/public/orderly-shutdown
	DESTINATION ${WEBOS_INSTALL_SYSCONFDIR}/init.d)

foreach(diritem rc0.d rc6.d rcH.d rcU.d)
	install(DIRECTORY DESTINATION ${WEBOS_INSTALL_SYSCONFDIR}/${diritem})
endforeach()

foreach (diritem rc0.d rc6.d rcH.d rcU.d)
	webos_install_symlink(../init.d/shutdown-watchdog.sh
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S00shutdown-watchdog.sh)
	webos_install_symlink(../init.d/orderly-shutdown
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S10orderly-shutdown)
	webos_install_symlink(../init.d/shutdown_animation
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S25shutdown-animation)
	webos_install_symlink(../init.d/stop-comms
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S30stop-comms)
	webos_install_symlink(../init.d/stop-fbprogress
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S89stop-fbprogress)
endforeach()

foreach (diritem rcH.d rcU.d)
	webos_install_symlink(../init.d/reboot
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S90reboot)
endforeach()

foreach (diritem rcU.d)
	webos_install_symlink(../init.d/shutdown-cancel-watchdog.sh
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S70shutdown-cancel-watchdog.sh)
	webos_install_symlink(${WEBOS_INSTALL_DATADIR}/ota-scripts/make-update-uimage
		${WEBOS_INSTALL_SYSCONFDIR}/${diritem}/S85make_update_uimage)
endforeach()

