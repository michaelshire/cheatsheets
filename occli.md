# OC cli cheat sheet

## Patch the cluster to prevent users from creating their own projects (self-provision).
oc patch clusterrolebinding.rbac self-provisioners -p '{ "metadata": { "annotations": { "rbac.authorization.kubernetes.io/autoupdate": "false" } } }'

## In a linux server on the command line, show the Operating System
grep PRETTY_NAME /etc/os-release

## Ahow all pods with Status "Running" in column format
oc get pods --field-selector=status.phase=Running --all-namespaces --no-headers -o custom-columns=":metadata.name"

## Show all pods with their pod status and waiting reason in column format
oc get pods --all-namespaces --no-headers -o custom-columns=":metadata.name,PodStatus:status.phase,STATE:status.containerStatuses[*].state.waiting.reason"

## Force deletion of a pod
oc delete pod <podname> -n <namespace> --grace-period=0 --force

## Assigning labels to projects
oc label namespace <projectname> <Label1>=<Value1> <Label2>=<Value2> ...

## View the template for the jenkins-persistent catalog item in a yaml formant
oc get templates -n openshift jenkins-persistent -o yaml

## List the users and their roles for a <namespace>
oc get rolebinding -n <namespace> -o custom-columns=Username:subjects[].name,Role:roleRef.name

# Issue with authentication?  Try deleting the user and identity:
oc delete user <email>@<domain>
oc delete identity <Identity starting with AAD:>

## Project stuck in Terminating state
### https://success.docker.com/article/kubernetes-namespace-stuck-in-terminating
### TL;DR: find the api resource that is failing and delete the finalizers
oc api-resources --verbs=list --namespaced -o name | xargs -n 1 oc get --show-kind --ignore-not-found -n <namespace>

## Show pods running in <namespace> and the node they are running on
oc get pods -n <namespace> -o custom-columns=NAME:metadata.name,NODE:spec.nodeName

## Get the "Requester" field annotation from the namespaces for all projects (note the escape character for the dot)
oc get namespace --all-namespaces --no-headers -o custom-columns=NAME:metadata.name,REQUESTER:metadata.annotations.openshift\.io/requester
