puppet-ssh\_statickeys
=====================

A Puppet SSH module that handles generating/storing/retrieving
the SSH keys on the Puppet Master as an alternative to using exported resources.
See [my blog](http://www.emeraldreverie.org/2013/03/managing-ssh-host-keys-in-reliable-way.html)
for example usage.

# Usage

Add this module to your module path, renaming the directory to simply
`ssh`.

## Host Keys

Add something like this to your manifest:

    $rsa_priv = ssh_keygen({name => "ssh_host_rsa_${::fqdn}", dir => 'ssh/hostkeys'})
    $rsa_pub  = ssh_keygen({name => "ssh_host_rsa_${::fqdn}", dir => 'ssh/hostkeys', public => 'true'})
    
    file { '/etc/ssh/ssh_host_rsa_key':
      owner   => 'root',
      group   => 'root',
      mode    => 0600,
      content => $rsa_priv,
    }
    file { '/etc/ssh/ssh_host_rsa_key.pub':
      owner   => 'root',
      group   => 'root',
      mode    => 0644,
      content => "ssh-rsa $rsa_priv host_rsa_${::hostname}\n",
    }

This will store an RSA SSH key on your puppet master for each host
using the module, meaning that after host rebuilds, the host has a
consistent SSH key. This avoids SSH key mismatches for the host.

## Automated SSH access for services

See my Dirvish module ([server
code](https://github.com/GregSutcliffe/puppet-modules/blob/production/modules/dirvish/manifests/install.pp#L25)
and [client code](https://github.com/GregSutcliffe/puppet-modules/blob/production/modules/dirvish/manifests/client.pp#L10))
for an example of using this in giving a central server access to client
systems without needing to explicitly add an SSH key to your manifests.
