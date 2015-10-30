# nordsoftware-ops

**_Everything here is subject to change_**

This repository holds everything related to infrastructure/operations for all our projects. In a nutshell, we use 
Packer to build Vagrant boxes from a standard Ubuntu Server (or any other image if necessary) image. Packer then uses 
local Ansible provisioning to provision the software required in the box. The built boxes can then be uploaded to 
Atlas which makes them available to Vagrant. See ["The provisioning process"](#the-provisioning-process) for more details.

## Guidelines

The master branch of this repository serves as an example of how everything fits together. The master branch should 
always be generic, i.e. you must assume that the boxes generated by it will be publicy available to anyone using 
Vagrant.

## Requirements

* Make sure you have [Packer](https://packer.io/) installed

## Usage

These instructions are only for simply building the base box from the master branch. If you're on a project-specific 
branch you should probably follow that branch's README.md instead.

* Enter the `packer/` directory. The paths in the Packer template are relative to the directory `packer` is run from 
so this step is important.
* Run `packer validate nginx-php-mariadb-nodejs.yml` to verify that the configuration is valid
* Run `packer build nginx-php-mariadb-nodejs.yml` to build the Vagrant box locally and push it to 
Atlas, or run `packer push nginx-php-mariadb-nodejs.yml` to build the box in Atlas instead. The latter is the 
preferred way of doing it.

## Linking to a project repository

In many cases the base box available in the master branch won't be sufficient for a particular project. Instead of 
adding your own various provisioning hacks, use the following instructions.

* Create a new branch in this repository with the same name as your project's repository
* Add this repository as a submodule in your own project's repository and make it point to the branch you just created
* Change whatever you need to get your box into shape, then follow the steps under ["Usage"](#usage) to build the box and push 
 it to Atlas.
 
### Notes about Atlas

Boxes pushed to Atlas are private by default, meaning strangers on the Internet won't be able to see that you have a 
box named `nordsoftware/secret-project`. To use private boxes in Vagrant you will have to either a) create an Atlas 
token and add it as an environment variable or b) create an Atlas account and run `vagrant login` before you run 
`vagrant up` for the first time.

If you want to create a new base box which is supposed to be public, log in to Atlas, find the Vagrant box, select 
Settings and untick the "Private" checkbox.

## The provisioning process

When a box is created and provisioned Packer starts with a standard Linux distribution image. It then uses shell 
provisioners to do the bare minimum required to get Ansible installed and working properly. This includes things like 
installing the VirtualBox guest additions, configuring sudo and so on.
 
The real provisioning is done by Ansible, and unless you have a very good reason not to you should adapt your Ansible 
playbook(s) instead of provisioning directly from Packer. See [ansible/README.md](ansible/README.md) for further 
details.
