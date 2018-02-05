DistrictBuilder
===============

[DistrictBuilder](http://www.districtbuilder.org/) is software created by the [Public Mapping Project](http://www.publicmapping.org/resources/software).

The development environment is docker-compose for services inside a Vagrant virtual machine.


Installation
------------

### Requirements ###

Vagrant 1.8.1
VirtualBox 4.3
Ansible 2.2

### tl;dr ###

```bash
$ cp django/publicmapping/config/config.dist.xml django/publicmapping/config/config.xml
$ ./scripts/setup
$ vagrant ssh
$ ./scripts/update
$ ./scripts/load_development_data
```

:zzz:

#### Development Environment Setup ####

`./scripts/setup` provisions the virtual machine. It brings up an Ubuntu 14.04 virtual machine
with docker installed. `vagrant ssh` gets you into the virtual machine so you can run commands.
From there, running `./scripts/update` builds containers. The rest of the setup happens either
directly or indirectly through a setup management command and is addressed in Django application
setup, next.
 
#### Django Application Setup ####

The django application includes a management command that handles application configuration
based on two input files: `settings.py` in the `publicmapping` app in `django/publicmapping`
and `config.xml`.

##### Data Setup #####

The first script to run is `./scripts/load_development_data`. It is not fast, and it's currently
recommended that you run it overnight. We specifically would like to improve performance of the
data load.

This script will do several things

- Fetch zipped shapefile data for Virginia into a specific location
- Drop and recreate the `district_builder**
- Run database migrations: create the relationships that data will be loaded into
- Load shapes from shapefiles at different levels: create records for the shapes and characteristics
  in the configured shapefiles
- Nest the shapes at different levels into each other: calculate the spatial relationships between
  shapes at different zoom levels
- Load some template plans: initialize the database with several example plans that users can start
  drawing with
- Create database views: create the database objects that GeoServer will use to
  create tiles of specific subjects
- Configure GeoServer: create layers and styles that will be served as tiles to the frontend

**IMPORTANT**: after you configure GeoServer, from outside the vm, run `vagrant rsync-back`. If you
don't yet have that plugin, run `vagrant plugin install vagrant-rsync-back`. If you don't do this,
your host won't pick up the files that were created in GeoServer configuration, so when you reboot
your vm, the files won't be present in the guest either, and then they won't be in the GeoServer
docker container. That will break GeoServer in a confusing and surprising way, and you'll be sad,
so just make sure you `rsync-back`.

##### Internationalization #####

To compile language files, use the `-l` flag.

Support
-------

More information about the application settings, configuration information, and run-time information is available in the PublicMapping/DistrictBuilder [wiki](https://github.com/PublicMapping/DistrictBuilder/wiki).

Bug reports and feature requests can be reported to the PublicMapping/DistrictBuilder [issue tracker](https://github.com/PublicMapping/DistrictBuilder/issues).
