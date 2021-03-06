#
# file_system makefile
#

#
# "PHONY" targets that aren't real build targets, but instead do
# something interesting -- like build a collection of targets and
# then run a server.  Here is the list of them supported by file_system
#
.PHONY: all clean debug release run_server run_debug_server

#
# the default target is 'all'.  If you execute 'make' without specifying
# a target, this is the target it will build
#
all:

#
# set CONFIG for the convenience targets 'debug' and 'run_debug_server'
# we don't need to worry about either of the release targets because
# the default CONFIG is release
#
ifeq (debug,$(MAKECMDGOALS))
 CONFIG=debug
endif
ifeq (run_debug_server,$(MAKECMDGOALS))
 CONFIG=debug
endif

#
# these two phony targets depend on (and so build) 'all'
#
debug: all
release: all

#
# the list of C/C++ files to compile -- all *.cpp files in our src directory
#
SOURCES=$(wildcard src/*.cpp)

#
# Specify what RESOURCES we want included in the build
# Note that excuting 'make display_rez' will show what resources
# it is including, and where they will be placed
#

#
# the list of resources (files we will fopen and fread during execution)
#
RESOURCES=$(wildcard rez/*)

#
# for testing purposes, make sure that file1.txt is in a custom
# directory -- not just the default "/resources" directory
#
rez/file1.txt_DST_DIR=/subdir1

#
# where and what we are building
#
ms.INTERMEDIATE_DIR:=ms_tmp/obj
ms.OUT_DIR:=ms_tmp/out
DEPLOY_DIR:=deploy
BUILD_NAME:=file_system

#
# this defines the functions we use below
#
include ../../src/mutantspider.mk

#
# this constructs all of the dependencies and build rules
# for everything we have defined in SOURCES.
#
# Debugging hint - if you want to see what rules this constructs
# change the 'eval' to 'info' and execute make again.
#
$(eval $(call ms.BUILD_RULES,$(BUILD_NAME),$(SOURCES)))

#
# make clean deletes all built files
#
clean:
	rm -rf $(DEPLOY_DIR) ms_tmp

#############################################################

#
# the files that mutantspider.mk will build
#
MS_TARGETS:=$(call ms.TARGET_LIST,$(BUILD_NAME))

#
# list of files which constitute a deployment
#
DEPLOY_FILES=\
$(DEPLOY_DIR)/$(CONFIG)/index.html\
$(DEPLOY_DIR)/$(CONFIG)/file_system_boot.js\
$(DEPLOY_DIR)/$(CONFIG)/mutantspider.js\
$(patsubst $(ms.OUT_DIR)%,$(DEPLOY_DIR)%,$(MS_TARGETS))

#
# make sure that 'all' builds all of the deploy files
#
all: $(DEPLOY_FILES)

#
# template for build rule to copy/link files from some src location into some deploy/$(CONFIG) location
#
#  $1 = full path to the file that needs to be copied to deploy/$(CONFIG)
#
define copy_template
$(DEPLOY_DIR)/$(CONFIG)/$(notdir $(1)): $(1)
	@mkdir -p $$(@D)
	$(call ms.CALL_TOOL,ln,-f $$< $$@,$$@)

endef

#
# instantiate an instance of copy_template for each file in $(MS_TARGETS)
#
$(foreach cp_file,$(MS_TARGETS),$(eval $(call copy_template,$(cp_file))))

#
# instantiate the copy_template for the three files we just copy to the deployment directory
#
$(eval $(call copy_template,index.html))
$(eval $(call copy_template,file_system_boot.js))
$(eval $(call copy_template,../../src/mutantspider.js))

########################################

#
# both of the "run server" targets build all and then
# run the nacl-provided http deamon tool on the resulting
# deployment directory
#
run_server run_debug_server: all
	@cd $(DEPLOY_DIR)/$(CONFIG) && ../../../../src/nacl_sdk_root/tools/httpd.py --no-dir-check
	


