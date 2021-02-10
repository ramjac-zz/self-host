Installing servers

I usually use Ubuntu https://ubuntu.com/download/server

install micro

install/enable auto-updates (link todo this)


I configure them to work by SSH only and turn off password auth

generate an ssh key if you don't already have one

ssh-copy-id yourUsername@yourMasterNodeHostName

ssh back to box
turn off password auth

restart ssh service

if possible try to ssh to the box from a machine that doesn't have your ssh-key

You should see the "<public key error here>"

Now ssh from the machine where you ran ssh-copy-id


If you run into firewall issues

ufw allow from 6443/tcp




Installing k3s 

https://rancher.com/docs/k3s/latest/en/installation/install-options/agent-config/


Get your credentials

cat /etc/rancher/k3s/k3s.yaml

drop that in

~/kube/config

change 

server: https://127.0.0.1:6443

to

server: https://yourMasterNodeHostName:6443


if you set up more than one node:

Get the token from your master node (probably the first one you installed)

cat /var/lib/rancher/k3s/server/node-token

curl -sfL https://get.k3s.io | K3S_URL=https://yourMasterNodeHostName:6443 K3S_TOKEN=tokenFromThePreviousCommand sh -

Passing in a K3S_URL and K3S_TOKEN runs k3s as an agent which will connect to the master node and wait for work.

Running this command back on master should now show two nodes

kubectl get nodes

--- not sure we need this line
cat /etc/rancher/node/password






kubectl label node workerName node-role.kubernetes.io/worker=worker

https://github.com/rancher/local-path-provisioner/blob/master/README.md#usage

