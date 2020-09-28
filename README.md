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
    For example, to generate and use random passwords in one go:
    ```sh
    # Use a reasonable (two-digit) number of random printable characters,
    # other than ampersand or angle brackets, for both of the passwords.
    icecast_admin_password="$(LC_CTYPE=C tr -dc '[:print:]' < /dev/urandom | tr -d '&<>' | head -c $(( 1 + RANDOM % 10 ))$(( 1 + RANDOM % 10 )))" \
    icecast_source_password="$(LC_CTYPE=C tr -dc '[:print:]' < /dev/urandom | tr -d '&<>' | head -c $(( 1 + RANDOM % 10 ))$(( 1 + RANDOM % 10 )))" \
    vagrant up
    ```
1. Note the Onion domain generated for your new Torified Internet radio station. The output you're looking for will start with "`Onion Radio online at:`" and will look something like this:
    ```
    TASK [assert] ******************************************************************
    ok: [default] => {
        "changed": false, 
        "msg": "Onion Radio online at: http://wsbk3yqico5a322rra5sdoebh2wucczfdm72kcmqrmq2dpnjgepglfyd.onion/"
    }
    ```
    You'll want to give that Onion address to any listeners. Of course, listeners who are tuning in to your radio station will have to be using [Tor Browser](https://torproject.org/) or some other torified client to actually connect to your Torified broadcast stream.
1. Connect an [Icecast source client](https://icecast.org/apps/#source-clients) to send your stream to the server at `127.0.0.1` ("`localhost`") at port `8000`. If the source client and the Icecast server are not running on the same computer, you will need to Torify your source client and configure it to send its stream to the Onion address, as well. In either case, be sure to set the streaming source client's "source password" to whatever password you told the server to expect from authorized source clients when you ran your `vagrant up` command, earlier.
    * If you generated random passwords earlier, you can look them up with:
        ```sh
        vagrant ssh -- sudo grep password /etc/icecast2/icecast.xml
        ```
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

By default, a new Onion domain is generated each time you run the Vagrant provisioner. This means a command such as `vagrant provision` will wipe out the old Onion domain and replace it with a new one. However, a `vagrant halt` followed by a `vagrant up` will retain the same Onion address across the reboot since the Onion service's private key is not deleted or overwritten; `vagrant destroy`, on the other hand, will delete the Onion site's private key.

An existing Onion domain can be re-used next time you re-create the Vagrant box by setting the `onion_radio_private_key_file` environment variable to a private key file for the Onion service. By default, the provisioner will copy out the generated private key file from the Vagrant machine and place it in the `backup/default/var/lib/tor/onion-services/onion_radio/private_key` file. Put another way, that path is `backup/<name_of_Vagrant_machine></absolute/path/in/the/vagrant_machine/to/onion_radio's/private_key>`. This means it's possible to `vagrant destroy` and then run `vagrant up` later like this to recreate the same Onion site:

```sh
# Using the full path (tab completion is your friend), do this:
onion_radio_private_key_file=backup/default/var/lib/tor/onion-services/onion_radio/private_key vagrant up

# Using a command substitution, do this:
onion_radio_private_key_file="$(find backup -name 'private_key')" vagrant up
```

Don't forget to set passwords for your newly recreated Icecast server, though, since those will also have been deleted and need to be re-set to take effect.
