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
