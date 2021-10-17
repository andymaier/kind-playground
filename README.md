# Use confluent stack with kind #

This repo contains a setup to create kind cluster and execute the confluent stack in that cluster.
Further there are descriptors available to expose the control center.

## Prequisites ##

- Docker
- kubectl
- helm
- kind tool (https://kind.sigs.k8s.io/)

In my case I am using sysbox/nestybox (https://www.nestybox.com/) engine and all commands are adjusted to that setup.

## kind commands ##

### Create cluster ###
`kind create cluster --config kind-cluster.yml`

This command creates a kind cluser with one control-pane and 3 workers.
The name of the cluster is "confluent-cluster". 

The following TCP ports will be exposed after creation. Make sure this ports are not bind by another process.

80 http
443 https
9092 exposed as -> 49092 kafka
8082 exposed as -> 48082 rest-proxy
8088 exposed as -> 48088 ksql

### Delete ###
To delete this cluster execute this command:

`kind delete clusters confluent-cluster`

### Install ingress ###
To have access to the cluster you have to install nginx.

`kubectl apply -f ingress-deployment-adjusted.yaml`

After that an nginx ingress installed. If you wish to test a successful cluster and nginx execute this sample:

`kubectl apply -f ingress-sample-test.yaml`

After that you have two services and pods installed where a http echo can be executed.
You are able now to execute:

`curl localhost/foo`

The result is a "foo".

## confluent with helm ##

To install a cp-all-in-one stack you have to execute this commands with helm:

`helm repo add confluentinc https://confluentinc.github.io/cp-helm-charts/`

`helm repo update`

`helm install my-confluent confluentinc/cp-helm-charts --version 0.6.0`

After that you will have the stack installed with pods, services and needed objects.

## install control-center ingress ##

To have an access to the control center in the created cluster an ingress have to be created.
This can be done by executing:

`kubectl apply -f ingress-control-center.yml`

This ingress lets you access to the control center over the subdomain control-center.ocp4.de
If you wish to change it you have to change the host definition.