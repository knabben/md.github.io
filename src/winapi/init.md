# Windows notebook

Notes from Windows Internal 7th Edition and general debugging rules

[Chapter 1](./1.md)

## Lab Env


### Windows container webserver

Simpler containers are available to provide a webservice for validation, the first
is a `whoami` provides a predefined content with some information about the connection:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: whoami-windows
spec:
  replicas: 1
  selector:
    matchLabels:
      app: whoami-windows
  template:
    metadata:
      labels:
        app: whoami-windows
    spec:
      containers:
      - image: stefanscherer/whoami:windows-amd64-2.0.1
        name: whoami-windows
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  name: whoami-windows
  labels:
    app: whoami-windows
spec:
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: whoami-windows
```

The second is agnhost, on HTTP handler mode offering a fake server on port 80.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-agnhost
  labels:
    name: agnhost
spec:
  containers:
  - args:
    - test-webserver
    image: registry.k8s.io/e2e-test-images/agnhost:2.43
    name: agnhost
  dnsConfig:
    nameservers:
    - 1.1.1.1
    searches:
    - resolv.conf.local
  dnsPolicy: None
  nodeSelector:
    kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  name: agnhost
  labels:
    name: agnhost
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    name: agnhost

```

For the client the netshoot container can run from a Linux and have all the required
tools for networking testing and debugging:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: netshoot
  name: netshoot
spec:
  containers:
  - command:
    - sleep
    - "360000"
    image: nicolaka/netshoot:latest
    name: netshoot
  nodeSelector:
    kubernetes.io/os: linux
```

Enter the netshoot pod with: `kubectl exec -it netshoot -- zsh`

## Performance testing

Vegeta is another project capable to provide load testing with tunning parameters
Download the project into the netshoot and run

```
echo "GET http://whoami-windows/" | vegeta attack -duration=300s -rate=1000 | vegeta report --type=text
```

## References 

[Debugger tools PS1](https://gist.github.com/knabben/2482cb672aeb3fbfe32d14fe3cbd70a0)

