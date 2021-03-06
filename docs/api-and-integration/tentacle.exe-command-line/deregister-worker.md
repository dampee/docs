---
title: Deregister from
description: Using the Tentacle.exe command line executable to deregister a Worker from an Octopus Server.
---

Deregisters this Worker from an Octopus Server

**Deregister from options**

```text
Usage: Tentacle deregister-worker [<options>]

Where [<options>] is any of:

      --instance=VALUE       Name of the instance to use
      --server=VALUE         The Octopus server - e.g., 'http://octopus'
      --apiKey=VALUE         Your API key; you can get this from the Octopus
                               web portal
  -u, --username=VALUE       If not using API keys, your username
  -p, --password=VALUE       If not using API keys, your password
  -m, --multiple             Deregister all worker machines that use the same
                               thumbprint

Or one of the common options:

      --help                 Show detailed help for this command
```

