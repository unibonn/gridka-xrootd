# gridka-xrootd module
This is the gridka-xrootd module, which originated from 
cern-it-sdc-id/puppet-xrootd. The fork was done to introduce extensive changes
regarding the configuration workflow.

### Usage

Including the `xrootd` class for a node will install the 'xrootd' package,
ensure that a couple of directories, e.g. for configuration files, exist and
try to start the xrootd and cmsd services.

Just applying the `xrootd` class will not give you the full flexibility
for the confiruation management. There are five additional resource types
defined, which may be utilised to generate more sophisticated configuration
files (see [Resource types](#resource-types)).

Those that are experienced Puppet users might notice that the module's base
class does not provide many parameters. In particular, none that have any
effect on the other classes provided. That is because we as the editors
of the forked code have Puppet Hiera in action and Puppet
will look up values for all class parameters there automatically. Hence,
we don't need those parameters. In case you do, simply apply the classes
directly, in the same fashion as the `xrootd` base class does it.

There is _no_ support for any kind of plugin management with this module.

#### Parameters

* `xrootd::xrootd_instances`, `xrootd::cmsd_instances`: 
Used to create Puppet Service resource types for all the xrootd and cmsd
instances with EL7 installations (_work-in-progress_).

* `xrootd::config::xrootd_user_name`, -`xrootd_group_name`:
Sets the (human readable) name for the xrootd user and group resources.
* `xrootd::config::xrootd_user`, -`xrootd_group`:
Define `user` and `group` Puppet resource types. This module will attempt to
ensure the correct user and group configuration. In case these resources
have to be managed outside of this module, simply assign an empty hash (`{}`)
as value.
* `xrootd::config::configdir`:
The directory where xrootd will find its configuration files.
* `xrootd::config::logdir`:
The directory where xrootd is supposed to write its logfiles to.
* `xrootd::config::spooldir`:
The spool directory for xrootd activity.
* `xrootd::config::all_pidpath`:
Here xrootd will keep information about the pids and environmental settings
of its independent processes.
* `xrootd::config::grid_security`:
Here xrootd will search for grid security related certificate files.

* `xrootd::service::certificate`:
Where to find the host certificate file.
* `xrootd::service::key`:
Where to find the host key file.

#### Resource types

##### `create_authfile`
Creates the authfile for xrootd. The sole parameter `entries` must be a list,
which elements are put into the file verbatim.

##### `create_config`
Create a cfg file for xrootd. Conditional statements are _not_ supported.
Instead, one should define a seperate cfg file for each daemon. That is,
every conditional configuration should be met with a split into seperate
cfg files for individual daemons.

There are three parameters to this resource type:
`xrootd::create_config::setenv`, `xrootd::create_config::set` and
`xrootd::create_config::params`. All of them are simple hashes, where the
key-value pairs will be written as they are into the destination file.
Settings from the `setenv` hash will be prefixed with 'setenv', likewise
those of the `set` hash will be prefixed with 'set'. In case a xrootd
parameter needs to be repeated in the configuration file, use a list as value
to the same key. For example, in order to set the local and the meta manager,
the `params` hash could look like this:

```ruby
{ 'all.manager' => ['my_manager', 'meta my_meta_manager'] }
```

##### `create_digauthfile`
Generate the `digauthfile` for the supplied `host` and `group`.

##### `create_sysconfig`
The sysconfig file is particularly important to configure several
different xrootd and cmsd instances, by means of...
* `xrootd::create_sysconfig::xrootd_instances_options`,
* `xrootd::create_sysconfig::cmsd_instances_options`,
* `xrootd::create_sysconfig::purd_instances_options` and
* `xrootd::create_sysconfig::xfrd_instances_options`.

All of these must be a hash, where the key can be 'default' or any other
string that will be used as the name for the instance. Subsequently,
the value to each key is the string of command line arguments of the
respective daemons.

Other self-explanatory parameters this resource type supports are...
* `xrootd::create_sysconfig::exports`
* `xrootd::create_sysconfig::daemon_corefile_limit`,
* `xrootd::create_sysconfig::enable_hdfs` and finally
* `xrootd::create_sysconfig::java_home`.

##### `create_systemd`
Create 'override' files in the systemd hierarchy. Supported parameters are...
* `xrootd::create_systemd::xrootd_user`,
* `xrootd::create_systemd::xrootd_group`,
* `xrootd::create_systemd::exports`,
* `xrootd::create_systemd::daemon_corefile_limit`,
* `xrootd::create_systemd::enable_hdfs` and
* `xrootd::create_systemd::java_home`.

All of which have been explained before at least nonce.

### Requirements

This module does _not_ ensure that the xrootd package for installation can
be found. Administrators have to prepare the environment appropriately!

### License
ASL 2.0

### Contact
(maintainer for this repository) Xavier Mol <xavier.mol@kit.edu>  
(original author) Andrea Manzi <andrea.manzi@cern.ch>
