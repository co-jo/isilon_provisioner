# k8s_isi_provisioner
[![Docker Pulls](https://img.shields.io/docker/pulls/hrynic/isilon_provisioner.svg)](https://hub.docker.com/r/hrynic/isilon_provisioner/)

Kubernetes external storage provisioner for Dell Isilon

Based on the following:
https://github.com/kubernetes-incubator/external-storage
https://github.com/codedellemc/goisilon

Instructions:
In order to use this external provisioner, you can use the image pushed to docker hub "hrynic/isilon_provisioner", or build it yourself.

Building
--------
To build this provisioner, ensure you have go, and glide installed.  This code has been tested with Go 1.8 and higher.
To build the software, run make.

The provisioner requires permissions if you are running it in OpenShift.
```
oc adm policy add-cluster-role-to-user system:persistent-volume-provisioner system:serviceaccount:k8s-isi-provisioner:default
```
It also requires pemissions if you're running in pure Kubernetes:
```
kubectl create -f auth.yaml
```

To deploy the provisioner, run
```
oc create -f pod.yaml
```
Create a storage class using the class.yaml file
```
oc create -f class.yaml
```

Or in Kubernetes, run:
```
kubectl create -f pod.yaml
kubectl create -f class.yaml
```

Some versions of Isilon may require the use of NFSv3. In that case, run:
```
kubectl create -f class-with-mount-options.yaml
```


To create a persistent volume, create a pvc and add an annotation:
volume.beta.kubernetes.io/storage-class: "k8s-isilon"
This will enable the automatic creation of a persistent volume.

Tested against: 
https://www.emc.com/products-solutions/trial-software-download/isilon.htm

This provisioner has support for Isilon Storage Quotas, however they have not been tested due to not having a license.

## Parameters
**Param**|**Description**|**Example**
:-----:|:-----:|:-----:
SERVER|The DNS name (or IP address) of the Isilon to use | isilon.somedomain.com
PATH|The root path for all exports to be created in| \/ifs\/ose\_exports 
USER|The user to connect to the isilon as|admin
PASS|Password for the user account|password
GROUP|The default group to assign to the share|users
QUOTA\_ENABLE|Enable the use of quotas.  Defaults to disabled. | FALSE or TRUE

## Thanks

Thanks to the developers of the Kubernetes external storage provisioner code and the docs that are making this possible to do.
Thanks to Dell EMC {code} for the great Isilon library.