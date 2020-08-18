# Onion Radio

> :construction: This is a very quick 'n' dirty thing that is currently a work in progress. I'm publishing it mostly so I don't "lose my work" moving forward.

At a minimum, you'll want to set a streaming source password and the Icecast server's administration password when you spin up the server. Use environment variables, like so:

```sh
# These environment variables will get passed down via Vagrant to the Ansible playbook.
icecast_admin_password=CHANGE_ME icecast_source_password=CHANGE_ME vagrant up
```

Once it's online, you'll want to retrieve the Onion domain:

```sh
vagrant ssh -- sudo cat /var/lib/tor/onion-services/onion_radio/hostname
```

Give that Onion address to any listeners. Note that they'll have to be using [Tor Browser](https://torproject.org/) or some other torified client to actually connect, of course.

This Onion domain can be re-used next time you re-create the Vagrant box by setting the `onion_radio_private_key_file` environment variable to the Onion service's private key file. By default, this will be copied out of the Vagrant machine and placed in the `backup/default/var/lib/tor/onion-services/onion_radio/private_key` file. Put another way, that path is `backup/<name_of_Vagrant_machine></absolute/path/in/the/vagrant_machine/to/onion_radio's/private_key>`. This means it's possible to `vagrant destroy` and then run `vagrant up` later like this to recreate the same Onion site:

```sh
# Using the full path (tab completion is your friend), do this:
onion_radio_private_key_file=backup/default/var/lib/tor/onion-services/onion_radio/private_key vagrant up

# Using a command substitution, do this:
onion_radio_private_key_file="$(find backup -name 'private_key')" vagrant up
```

To broadcast, configure your favorite [streaming source client](https://icecast.org/apps/#source-clients) to send your stream to the server at `127.0.0.1` ("`localhost`") at port `8000`. Be sure to set the streaming source client's "source password" to whatever you set it to when you ran your `vagrant up` command, earlier.
