# playbooks
A collection of playbooks for deploying services and functions to an HPC cluster

<pre>
1. iscsi.client.pb - Will add the iscsi service and ACL to connect to a target drive
2. iscsi.target.pb - Will add a target drive given a device ...?
3. ctdb.pb - Will create a ctdb/samba cluster on san[1:2]

After running: ansible-playbook iscsi.yml

Do these steps:

## need to set password for user hacluster: echo Cluster1|passwd --stdin hacluster
## need to enable pcsd: systemctl enable pcsd
## Do this on 1 node
## pcs cluster auth HPC1.local.datos.asia HPC2.local.datos.asia
## Username: hacluster
## Password: Cluster1
##
## Start the cluster on HPC1 with the following command for a 2 node cluster:
## pcs cluster setup --start --name mycluster --wait_for_all=0  HPC1.local.datos.asia HPC2.loca l.datos.asia
##
## Enable cluster services pacemaker and corosync 
## pcs cluster enable --all
##
## check the status
## pcs cluster status
##
## stop the cluster and add this for 2 node operation
## pcs cluster stop --all
##
# vim /etc/corosync/corosync.conf
# quorum {
#    provider: corosync_votequorum
#    two_node: 1
#    wait_for_all: 0
# }
#
# test...
# pcs cluster start --all
#
# corosync-quorumtool
#
# test the config files and fix STONITH errors
# crm_verify -L -V
# 
# fix it....
# pcs property set stonith-enabled=false
#
# crm_verify -L -V
</pre>	
Change the pcs property to no-quorum-policy to freeze. This property is necessary because it means that cluster nodes will do nothing after losing quorum, and this is required for GFS2
<pre>
# pcs property set no-quorum-policy=freeze
</pre>
If you would leave the default setting of stop, mounted GFS2 file system cannot use the cluster to properly stop, which will result in fencing of the entire cluster.
<pre>
pcs resource create dlm ocf:pacemaker:controld op monitor interval=30s on-fail=fence clone interleave=true ordered=true
</pre>
Fencing also has a role to play in the event that a clustered service cannot be stopped. In this case,
the cluster uses fencing to force the whole node offline, thereby making it safe to start the service
elsewhere.
Fencing is also known as STONITH, an acronym for "Shoot The Other Node In The Head", since the
most popular form of fencing is cutting a host’s power.
In order to guarantee the safety of your data, fencing is enabled by default.
