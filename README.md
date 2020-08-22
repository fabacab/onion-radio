# Onion Radio

Quickly create a private, "dark net" Internet radio station accessible to listeners over the Tor network.

## Quick start

1. Install [VirtualBox](https://virtualbox.org/).
1. Install [Vagrant](https://vagrantup.com/).
1. Clone or download this project's source code.
1. Set up Onion Radio with at least an admin and source (broadcaster) password:
    ```sh
    # These environment variables will get passed down via Vagrant to the Ansible playbook.
    icecast_admin_password=CHANGE_ME icecast_source_password=CHANGE_ME vagrant up
    ```
1. Note the Onion domain generated for your new Torified Internet radio station. The output you're looking for will start with "`Onion Radio online at:`" and will look something like this:
    ```
    TASK [assert] ******************************************************************
    ok: [default] => {
        "changed": false, 
        "msg": "Onion Radio online at: http://em54e322hi3rmkp4.onion/"
    }
    ```
    You'll want to give that Onion address to any listeners. Of course, listeners who are tuning in to your radio station will have to be using [Tor Browser](https://torproject.org/) or some other torified client to actually connect to your broadcast stream, of course.
1. Connect an [Icecast source client](https://icecast.org/apps/#source-clients) to send your stream to the server at `127.0.0.1` ("`localhost`") at port `8000`. Be sure to set the streaming source client's "source password" to whatever you set it to when you ran your `vagrant up` command, earlier.
1. When you're done, shut down the Onion Radio server:
    ```sh
    vagrant halt
    ```
    Your Onion will go offline, and any listeners to your stream who are still connected will be disconnected.
1. To start broadcasting again, bring the server back up:
    ```sh
    vagrant up
    ```
    There is no need for setting passwords again, unless you want to change them.

## Retrieve the current Onion domain

Simply read out the current Onion domain from the running config:

```sh
vagrant ssh -- sudo cat /var/lib/tor/onion-services/onion_radio/hostname
```

## Reuse an existing Onion domain

By default, a new Onion domain is generated each time you run the Vagrant provisioner. This means a command such as `vagrant provision` will wipe out the old Onion domain and replace it with a new one.

An existing Onion domain can be re-used next time you re-create the Vagrant box by setting the `onion_radio_private_key_file` environment variable to the Onion service's private key file. By default, this will be copied out of the Vagrant machine and placed in the `backup/default/var/lib/tor/onion-services/onion_radio/private_key` file. Put another way, that path is `backup/<name_of_Vagrant_machine></absolute/path/in/the/vagrant_machine/to/onion_radio's/private_key>`. This means it's possible to `vagrant destroy` and then run `vagrant up` later like this to recreate the same Onion site:

```sh
# Using the full path (tab completion is your friend), do this:
onion_radio_private_key_file=backup/default/var/lib/tor/onion-services/onion_radio/private_key vagrant up

# Using a command substitution, do this:
onion_radio_private_key_file="$(find backup -name 'private_key')" vagrant up
```
