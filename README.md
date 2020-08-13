# Onion Radio

> :construction: This is a very quick 'n' dirty thing that is currently a work in progress. I'm publishing it mostly so I don't "lose my work" moving forward.

At a minimum, you'll want to set a streaming source password and the Icecast server's administration password when you spin up the server. Use environment variables, like so:

```sh
# These environment variables will get passed down via Vagrant to the Ansible playbook.
ice_adminpassword=CHANGE_ME ice_sourcepassword=CHANGE_ME vagrant up
```

Once it's online, you'll want to retrieve the Onion domain:

```sh
vagrant ssh -- sudo cat /var/lib/tor/hidden_service/hostname
```

Give that Onion address to any listeners. (In the next revision we'll save the Onion service's private key so we can retain the same Onion domain.) Note that they'll have to be using [Tor Browser](https://torproject.org/) or some other torified client to actually connect, of course.

To broadcast, configure your favorite [streaming source client](https://icecast.org/apps/#source-clients) to send your stream to the server at `127.0.0.1` ("`localhost`") at port `8000`. Be sure to set the streaming source client's "source password" to whatever you set it to when you ran your `vagrant up` command, earlier. (If you didn't set a password, the default is simply `password`).
