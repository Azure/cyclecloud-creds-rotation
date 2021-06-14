# Rotating node credentials in CycleCloud

This repo contains a package that updates CycleCloud with a tool that can rotate the CycleCloud cluster node credentials. 
To install it, download the rotate_creds.zip file onto the machine running CycleCloud, extract it, and run `install.sh` as root:

``` bash
cd /tmp
wget aka.ms/cyclecloud/creds-rotation
unzip rotate_creds.zip
cd /tmp/rotate_creds
./install.sh
```

This updates the CycleCloud install to provide the `util/rotate_creds.sh` command that can be run to replace the credentials on all nodes. 

To rotate the credentials, make sure **there are no MPI execute nodes running** and all other nodes are stable (either started or terminated). Then run this command as root:

``` bash
/opt/cycle_server/util/rotate_creds.sh
```

It first tests the nodes for SSH connectivity and reports the results. Once confirmed, it will generate new credentials to replace the old ones and update the reachable nodes automatically. If it fails, you can re-run the command and it will retry just the failed nodes without another rotation.

## Notes

This feature has the following limitations:
- All execute nodes using Infiniband (e.g., MPI nodes) **must be terminated first**. The scaleset definition contains the credentials and is not updated.
- It cannot automatically update Windows nodes. However, the script that is executed on the nodes can be manually copied and run from there.
- It cannot update Linux nodes that it cannot reach using the **node's private IP** from the CycleCloud machine. You must make the script available to those nodes and run it manually.
- If the `/opt/cycle_server/.ssh/cyclecloud.pem` SSH key does not allow access to the nodes, you can supply an alternate key for authentication with the `--ssh-identity path_to_key` argument.
- All nodes must be fully Started so that they are available to have the credentials updated..
