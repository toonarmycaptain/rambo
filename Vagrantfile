# -*- mode: ruby -*-
# vi: set ft=ruby :
#
# This file is meant to contain vars that are most likely to be changed
# when configuring Rambo. Further logic is loaded with vagrant/core, and
# then a Vagrantfile for a specific provider (e.g. Vagrantfile.ec2).

puts "-- NOW IN VAGRANT --"
require "json"

# Change CWD for each VM, as set by Rambo, otherwise relative path resources break.
if ENV.has_key?("VAGRANT_CWD")
  Dir.chdir ENV["VAGRANT_CWD"]
end

SETTINGS = JSON.parse(File.read('rambo/settings.json'))
load "vagrant_resources/modules.rb" # for random_tag

PROJECT_NAME = SETTINGS["PROJECT_NAME"]

if not ENV.has_key?(PROJECT_NAME.upcase + "_ENV")
  puts "", "***CAUTION***",
       "Running Vagrant directly and without %s's official CLI." % PROJECT_NAME.capitalize,
       "This is not supported.",
       "***/CAUTION***", ""
end

Vagrant.require_version ">= 1.9.7"

orphan_links = `find . -xtype l`.split(/\n+/)
for link in orphan_links
  puts "Deleting broken symlink #{link}"
  File.delete(link)
end

#VM_HOST = "ubuntu-16"
VM_HOST = "debian-8"

VM_NAME = "rambo-" + random_tag()

VM_SIZE = "2048mb"

FORWARD_SSH = true

COPY_DIR_WITH_RSYNC = true

PROVISION_WITH_SALT = true

PROVISION_WITH_CMD = true

## Construct commands to have Salt provision the machine.# Pass var to allow Salt to set the hostname to the VM_NAME.
SET_HOSTNAME = "sudo SSH_AUTH_SOCK=$SSH_AUTH_SOCK salt-call --local grains.setval hostname " + VM_NAME + " --log-level info; "

# Set hostname + run highstate.
PROVISION_CMD = SET_HOSTNAME + "sudo SSH_AUTH_SOCK=$SSH_AUTH_SOCK salt-call --local state.highstate --log-level info"

# Add website branch var if applicable.
if ENV["WEBSITE_BRANCH"]
  PROVISION_CMD = "sudo SSH_AUTH_SOCK=$SSH_AUTH_SOCK salt-call --local grains.setval WEBSITE_BRANCH " + ENV["WEBSITE_BRANCH"] + " --log-level info; " + PROVISION_CMD
end

#load the rest of the vagrant ruby code
load File.expand_path("vagrant_resources/vagrant/core")
