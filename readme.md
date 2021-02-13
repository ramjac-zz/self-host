# Self-hosting Everything

This is a series of blog entries on how you could self-host an entire enterprise style software stack using containers in a kubernetes cluster at home.

Here's what we're running:

* Source Control - [gitea](https://docs.gitea.io/en-us/)
* Task Runner (will CI/CD, backups, monitoring) - [Concourse](https://concourse-ci.org/docs.html)
* Chat - [Mattermost](https://docs.mattermost.com/install/install-kubernetes.html)
* User Management -[Recongnizer](https://github.com/system76/recognizer)
* Notifications - [Bullhorn](https://github.com/system76/bullhorn)
* File Store - [Perkeep](https://perkeep.org/doc/
)
* Webserver - [Caddy](https://caddyserver.com/docs/)
* VPN - maybe... - [Tailscale](https://tailscale.com/kb/)
* Physical World Interface - [Home Assistant](https://www.home-assistant.io/getting-started/)
* Mail hosting - maybe... - [mailcow](https://mailcow.github.io/mailcow-dockerized-docs/)

Well that's an ambitious list... I suspect some of those will change before I get through them.

Before I get into any of that I'm going to tell you a little about the hardware and software that will run my humble kubernetes cluster.

I have one Dell Latitude 7275 (a fanless laptop with an Intel m7 and 8gb RAM) and a desktop with an AMD 2400g and 16gb RAM.

That's an excessive amount of hardware. If you're following along, you can start with a lot less especially if you're not planning on running everything from the list. One old-ish laptop or a new Raspberry Pi should be fine. If it isn't, kubernetes will happily let you add another to the cluster and you'll be fine.

I gotta be running two of everything (development and production) for this blog...

## Server Setup

I usually use [Ubuntu](https://ubuntu.com/download/server) and just pick the latest LTS. Feel free to pick something else. Any Debian based distro is likely to work pretty similarly for the purposes of this guide.

To make editing text file extra pleasant, install a nice text editor like [Micro](https://micro-editor.github.io/)

    snap install micro --classic

Be sure to configure [auto-updates](https://help.ubuntu.com/community/AutomaticSecurityUpdates). I even recommend automatically rebooting. I'm not hiring an IT guy to keep an eye on my infrastructure. Everything will lean toward automatically updating itself in this guide even if that means occaisional breakage.

Next we'll set up SSH access to server.


I configure them to work by SSH only and turn off password auth

generate an ssh key if you don't already have one

ssh-copy-id yourUsername@yourMasterNodeHostName

ssh back to box
turn off password auth

restart ssh service

if possible try to ssh to the box from a machine that doesn't have your ssh-key

You should see the "<public key error here>"

Now ssh from the machine where you ran ssh-copy-id

Once you've confirmed your SSH setup is all-good, you can stash your server where-ever your router and never look at it again.

## Install Kubernetes

I'll be using a Kubernetes distro called [k3s](https://k3s.io/) which is lightweight and easy for small self-hosted scenarios like this. Ubuntu has their own [MicroK8s](https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s#1-overview) which is also easy.


If you run into firewall issues

ufw allow from 6443/tcp

sudo ufw allow in on cni0 && sudo ufw allow out on cni0
sudo ufw default allow routed





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

