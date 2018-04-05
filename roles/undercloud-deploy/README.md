undercloud-deploy
==========================================

An Ansible role to execute the deployment of the tripleo undercloud

Requirements
------------

This requires a running host to deploy the undercloud.

Role Variables
--------------

- `undercloud_config_file`: <'undercloud.conf.j2'> -- the name of the jinja template
used as the base for the undercloud.conf
- `undercloud_install_script`: <'undercloud-install.j2'> -- the name of the jinja template
used as the base for the undercloud-install bash script and dev envs hacking. Note, the
defaults imply the 'openstack undercloud install' command will be invoked. See the undercloud
deployment methods section below for the alternative modes.
- `undercloud_install_log`: <'{{ working_dir }}/undercloud_install.log'> -- the full path
to the undercloud install log file.
- `undercloud_hieradata_override_file`: <'quickstart-hieradata-overrides-classic-undercloud.yaml.j2'> -- the name of
jinja template used to override the undercloud's install hieradata
- `undercloud_ironic_ipxe_port`: <'3816'> -- port to use for httpd ipxe server
for ironic deploy
- `enable_vbmc`: <'true'> use a virtual bmc instead of pxe ssh
- `step_introspect`: <'false'> -- boolean value to enable/disable ironic introspection
- `bash_deploy_ramdisk`: <'false'> -- the variable allows older versions of tripleo to upload images
properly with the option --old-deploy-image
- `step_install_undercloud`: <'true'> -- turn on/off the undercloud deployment
- `libvirt_uri`: <'qemu:///session'> -- the URI used by libvirt, by default tripleo-quickstart uses
user sessions to provide greater flexixiblity to our users. ** additional documentation ** is at
http://docs.openstack.org/developer/tripleo-quickstart/accessing-libvirt.html
- `undercloud_conf_extra`: <''> -- extra options to be added to ~/undercloud.conf
- `undercloud_extra_args`: <''> -- extra options for undercloud deploy command.
- `undercloud_install_cli_options`: <''> -- extra options for undercloud install command.
- `undercloud_enable_mistral`: <'true'> -- sets up the 'enable_mistral' option
  in undercloud.conf.
- `undercloud_enable_monitoring`: <'false'> -- sets up the 'enable_monitoring'
  option in undercloud.conf.
- `undercloud_enable_telemetry`: <'true'> -- sets up the 'enable_telemetry'
  option in undercloud.conf.
- `undercloud_enable_tempest`: <'true'> -- sets up the 'enable_tempest' option
  in undercloud.conf.
- `undercloud_enable_ui`: <'true'> -- sets up the 'enable_ui' option in
  undercloud.conf.
- `undercloud_enable_validations`: <'true'> -- sets up the 'enable_validations'
  option in undercloud.conf.
- `undercloud_enable_novajoin`: <'false'> -- sets up the 'enable_novajoin'
  value from undercloud.conf. Note that using 'enable_tls_everywhere' will have
  the same effect.
- `novajoin_connect_timeout`: <5> Sets vendordata_dynamic_connect_timeout when novajoin is enabled
- `novajoin_read_timeout:` <20> Sets vendordata_dynamic_read_timeout when novajoin is enabled
- `prepare_novajoin`: If set to true, it will install novajoin in the undercloud,
  and run a script that will create the required privileges/permissions in
  FreeIPA, as well as the undercloud host entry. this requires
  'enable_tls_everywhere' to be set to true, and the following variables to be
  properly defined: 'freeipa_admin_password', 'freeipa_server_hostname',
  'overcloud_cloud_domain', 'undercloud_undercloud_hostname'. If you plan to do
  this yourself, you can set this variable to false. Defaults to true.
- `freeipa_admin_password`: The password for the admin principal for FreeIPA.
  This will be used to run the script to prepare FreeIPA for novajoin.
- `freeipa_server_hostname`: The hostname for the FreeIPA server.
  This will be used to run the script to prepare FreeIPA for novajoin.
- `overcloud_cloud_domain`: The domain configured for use by the overcloud
  nodes. If TLS everywhere is enabled, this will also be the domain used by
  FreeIPA. This sets up the 'overcloud_domain_name' configuration option in
  undercloud.conf . Note: This also affects `CloudDomain` in the
  `cloud-names.yaml` template used by the `overcloud-deploy` role.
- `undercloud_cloud_domain`: The domain configured for use by containerized
  undercloud via the tripleo client's `--local-domain` option. It is unset by default.
- `tripleo_ui_secure_access`: Defaults to false due to the self signed certificate and
  usability issues. See the tripleo-quickstart documentation `accessing the undercloud` for details.
- `undercloud_docker_registry_host`: <`docker_registry_host`> -- registry host/port
  for containerized undercloud services. Defaults to the value provided for overcloud.
  You may want to diverge it, if building locally, or fetching from remote registries
  not fitting the overcloud deployment needs.
- `undercloud_docker_registry_port`: <null> -- an optional port for
  `undercloud_docker_registry_host`.
- `undercloud_docker_registry_namespace`: <`docker_registry_namespace`> -- namespace of
  docker images used for undercloud deployment. Defaults to the value provided for overcloud.
- `undercloud_docker_image_tag`: <`docker_image_tag`> -- tag of docker images used for
  undercloud deployment. Defaults to the value provided for overcloud. Defaults to the
  value provided for overcloud.
- `undercloud_container_images_file`: <"{{ working_dir }}/undercloud-containers-default-parameters.yaml"> --
  the environment file with default parameters for containers to use with undercloud install CLI.
- `undercloud_extra_services_args`: -- additional t-h-t (TripleO heat templates)
  environment files for extra services deployed on containerized underclouds. By default,
  includes environments for docker. Those are only applied for the `undercloud deploy` CLI and not
  for the `undercloud install`'s `undercloud.conf`. Use `undercloud_custom_env_files` for the latter.
- `undercloud_custom_env_files`: <"{{ working_dir }}/undercloud-parameter-defaults.yaml"> --
  custom t-h-t env files for the `undercloud install`'s `undercloud.conf` interface. By default,
  it provides the default parameters generated from `undercloud_resource_registry_args` and
  `undercloud_network_environment_args`. For the `undercloud deploy`, use
  `undercloud_extra_services_args` instead.
- `undercloud_undercloud_output_dir`: <null> -- allows customize output directory for state, like
  downloaded ansible configs and processed heat templates for heat installer
- `undercloud_undercloud_cleanup`: <null> -- controls tear down of the processed heat templates
  in the output directory for heat installer
- `download_undercloud_templates_rpm`: if set to true, allow the user to
  download a tripleo-heat-templates rpm package from a url defined by the
  variable `tht_rpm_url`
- `undercloud_templates_path`: location of undercloud templates, by default undefined

Role Network Variables
----------------------
- `undercloud_network_cidr`: <'192.168.24.0/24'> -- the network cidr for the undercloud, note this
also currently the default cidr used in other CI environments for tripleo.
- `undercloud_network_gateway`: <a 1st host of the `undercloud_network_cidr`> -- Sets up the
`undercloud_network_gateway` parameter from undercloud.conf.
- `undercloud_local_ip`: <hostvars['undercloud'].undercloud_ip> -- Sets up the `local_ip`
parameter from an inventory. Must be belonging to the `undercloud_network_cidr`. It is used
by overcloud nodes to access the undercloud node via a routable
[ctlplane network]((https://docs.openstack.org/developer/tripleo-docs/advanced_deployment/network_isolation).
Note that the undercloud.conf takes the default value based on the `undercloud_network_cidr`
instead.
- `undercloud_undercloud_public_host`: <a 2nd host of the `undercloud_network_cidr`> -- Sets
up the 'undercloud_public_host' parameter from undercloud.conf. It is also used by overcloud
nodes to access the undercloud node via a VIP/hostname that resolves to a routable IP address.
- `undercloud_undercloud_admin_host`: <a 3rd host of the `undercloud_network_cidr`> -- Sets
up the 'undercloud_admin_host' from undercloud.conf.  Note, use the `undercloud_admin_vip`
instead for Mitaka/Newton releases.
- `undercloud_dhcp_start`: <a 5th host of the `undercloud_network_cidr`> -- Sets
up the 'undercloud_dhcp_start' from undercloud.conf.
- `undercloud_dhcp_end`: <a 30th host of the `undercloud_network_cidr`> -- Sets
up the 'undercloud_dhcp_end' from undercloud.conf.
- `undercloud_undercloud_nameservers`: <['8.8.8.8']> -- Sets up the 'undercloud_undercloud_nameservers'
from undercloud.conf. May be a string or a sequence. Only the last item goes for
the undercloud deploy command.
- `undercloud_undercloud_hostname`: Sets up the 'undercloud_hostname' value from undercloud.conf.
Note, use the `undercloud_public_vip` instead for Mitaka/Newton releases.
- `undercloud_heat_public_endpoints`: <'false'> -- when the ctlplane network is not routable
from overcloud nodes, for example pre-provisioned
[deployed servers](https://docs.openstack.org/developer/tripleo-docs/advanced_deployment/deployed_server.html#undercloud),
the ``undercloud deploy --local_ip`` (and `local_ip` in the undercloud.conf)
may not be used. Enable this variable instead. Doing so changes the heat endpoint
type from the default internal to public and changes the signaling method to use
TempURLs from OpenStack Object Storage (swift).
- `undercloud_resource_registry_args`: Sets up network config for Undercloud
  (resource_registry for heat templates). Defaults to Noop.
- `undercloud_network_environment_args`: Complements Undercloud networking
  setup with the default parameters for heat templates (parameter_defaults).

Undercloud deployment methods
-----------------------------

By default, the undercloud uses ``openstack undercloud install`` command,
`{{working_dir}}/undercloud.conf` config and the file coming from the
``hieradata_override_file_classic_undercloud`` variable to set the hieradata
overrides that will be used for the puppet deployment.

However, there exists another method, explained below, for setting up the
undercloud based on containers with the ``openstack undercloud deploy`` command.
This method can be used by setting the ``containerized_undercloud`` variable to
'true' and it's currently experimental. Instead of using the "classic" way to set
up hieradata, it uses the file coming from the
``hieradata_override_file_t_h_t_undercloud`` variable. As the name hints, this
deployment method is based on tripleo-heat-templates, and thus, the way of
overriding hieradata for it is similar as we do for the overcloud.

Note, the containerized undercloud is a hacking mode for developers, that allows
to test containerized components undercloud, with custom, like very minimal,
setup layouts. Please do not expect more than that when using it.

Example Playbook
----------------

Sample playbook to call the role

```yaml
# Deploy the undercloud
- name:  Install undercloud
  hosts: undercloud
  gather_facts: no
  roles:
    - undercloud-deploy
```

Containerized dev environments (experimental feature)
-----------------------------------------------------

Firstly, update or create a custom undercloud install script template. It can be
found under the locally checked out tripleo-quickstart-extra repo (or at a remote
host, if you prefer to apply ansible playbooks from a non local virthost). The
template is located by the
`roles/undercloud-deploy/templates/undercloud-deploy-my-dev.sh.j2` path.
Once changes are done, update the ``undercloud_install_script`` var, like:

```yaml
undercloud_install_script: undercloud-deploy-dev.sh.j2
```

or use the updated template:
```
undercloud_install_script: undercloud-deploy.sh.j2
```

Note, it is important to change the default value for the ``undercloud_install_script``
to switch from the classic 'install' to the experimental 'deploy' method.

Secondly, you may want to refer an unpackaged t-h-t change refspec or a dev
branch. To do so, override the custom t-h-t repo and branch/refspec
for t-h-t templates to be fetched, for example:

```yaml
undercloud_templates_repo: https://github.com/johndoe/tripleo-heat-templates
undercloud_templates_branch: dev
```

Note, these vars are shared with the overcloud role vars and point to
the same templates path, branch/refspec and repo. The templates path should be
R/W accessible by the given `non_root_user` without sudoing. So it is better
off using the home dir or tmp dirs with sticky bits.

Also note that you should normaly rely on the openstack-tripleo-heat-templates
package. The custom t-h-t repo vars may break that package and should be used with
caution.

Note, checkout/install steps for the remaining yet unpackaged custom changes
like dev branches for puppet modules, tripleo client, heat agent hooks, need
to be covered in the custom ``undercloud-install.sh`` script body (rendered
from a given in the ``undercloud_install_script`` template file name).

The last step is to define the ``undercloud_extra_args`` as needed. For
example you may want to deploy:

 * an optional non containerized Octavia,
 * containerized Keystone,
 * optional containerized Etcd,

with disabled telemetry, default docker images from `docker.io` namespace,
debug logs for services and puppet, given a decent timeout, and keeping
the ephemeral undercloud heat agent running for debug purposes:

```yaml
undercloud_extra_services_args: >-
  -e {{undercloud_templates_path}}/environments/disable-telemetry.yaml
  -e {{undercloud_templates_path}}/environments/docker-minimal.yaml
  -e {{undercloud_templates_path}}/environments/services-docker/etcd.yaml
  -e {{undercloud_templates_path}}/environments/services/octavia.yaml
  -e {{undercloud_templates_path}}/environments/debug.yaml
  -e {{undercloud_templates_path}}/environments/config-debug.yaml
undercloud_extra_args: >-
  --timeout 60
  --keep-running
```

Where the t-h-t's `environments/docker-minimal.yaml` is like:

```
resource_registry:
  OS::TripleO::Services::Docker: ../puppet/services/docker.yaml
  OS::TripleO::Services::Keystone: ../docker/services/keystone.yaml
  OS::TripleO::PostDeploySteps: ../docker/post.yaml
  OS::TripleO::PostUpgradeSteps: ../docker/post-upgrade.yaml
  OS::TripleO::Services: ../docker/services/services.yaml

parameter_defaults:
  DockerNamespace: tripleomaster
  ComputeServices: {}
  SwiftCeilometerPipelineEnabled: false
```

Note, this template complements the default t-h-t's `environments/docker.yaml`
setup that deploys everything in containers. The default template for
the ``undercloud-install.sh`` also provides an additional set of required
services used for underlcoud to deploy overclouds (Ironic, Zaqar, MongoDB,
Mistral). If you need a lightweight undercloud, make sure your custom
undercloud installation script template omits those services and the
`environments/docker.yaml` defaults.

Also note, if you want to override the undercloud roles data, you should
provide a custom roles file and put it in place of the default
`./roles_data_undercloud.yaml` by the given ``undercloud_templates_path``.
This can be done with a ``cp`` command in the custom undercloud deploy
script. For the example above, custom undercloud roles may look like:

```
- name: Undercloud
  CountDefault: 1
  disable_constraints: True
  tags:
    - primary
    - controller
  ServicesDefault:
    - OS::TripleO::Services::Etcd
    - OS::TripleO::Services::Keystone
    - OS::TripleO::Services::OctaviaApi
    - OS::TripleO::Services::OctaviaHealthManager
    - OS::TripleO::Services::OctaviaHousekeeping
    - OS::TripleO::Services::OctaviaWorker
```

And an example playbook to call the role is:

```yaml
# Deploy the undercloud
- name:  Deploy undercloud (experimental)
  hosts: undercloud
  gather_facts: no
  vars:
    containerized_undercloud: true
    undercloud_install_script: undercloud-deploy.sh.j2
    overcloud_templates_repo: https://github.com/johndoe/tripleo-heat-templates
    overcloud_templates_branch: dev
    undercloud_extra_services_args: >-
      -e {{undercloud_templates_path}}/environments/disable-telemetry.yaml
      -e {{undercloud_templates_path}}/environments/docker-minimal.yaml
      -e {{undercloud_templates_path}}/environments/services-docker/etcd.yaml
      -e {{undercloud_templates_path}}/environments/services/octavia.yaml
      -e {{undercloud_templates_path}}/environments/debug.yaml
      -e {{undercloud_templates_path}}/environments/config-debug.yaml
    undercloud_extra_args: >-
      --timeout 60
      --keep-running
  roles:
    - undercloud-deploy
```
