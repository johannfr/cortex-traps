# Cortex inside a privileged container

## Usage

First, on NixOS, you want to create some writable paths for the container.
Add this to your `configuration.nix`:

```
systemd.tmpfiles.rules = [
  "d /var/lib/cortex-agent/traps 0700 root root -"
  "d /var/log/cortex-agent 0755 root root -"
];
```

Clone this repo to your local machine, `cd` into it and extract your Cortex-package into the `package` directory:

```
 $ cd /path/to/this/repo
 $ tar -C package -xvf /path/to/your/cortex/archive_deb.tar.gz
```

And then just run the container:

```
 $ docker compose up -d
```

Your agent should start running, but it takes a while for it to settle and for all of its processes to run.

To check the status you can run:

```
 $ docker exec cortex_agent /opt/traps/bin/cytool runtime query
```

At first, the only running process will be the `pmd` process.

After a while, all the processes, except the bottom two, will be running:

```
              Name       PID           User                Status		Command
               pmd    679180           root               Running		/opt/traps/bin/pmd
              clad    681471       cortexu+               Running		/opt/traps/analyzerd/clad
              dypd    681356           root               Running		/opt/traps/bin/dypdng
          sandboxd    681473       cortexu+               Running		/opt/traps/analyzerd/sandboxd
              lted    681609       cortexu+               Running		/opt/traps/python/payload/lted
              pyxd    681597           root               Running		/opt/traps/python/payload/pyxd
              cned       N/A            N/A               STOPPED		N/A
              piud       N/A            N/A               STOPPED		N/A
```

Done.
