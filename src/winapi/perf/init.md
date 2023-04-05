# Kubernetes Services on Windows

### Windows container Webserver

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

## Performance testing for webserver

Vegeta is another project capable to provide load testing with tunning parameters
Download the project into the netshoot and run

```
echo "GET http://whoami-windows/" | vegeta attack -duration=300s -rate=1000 | vegeta report --type=text

Requests      [total, rate, throughput]         300000, 1000.00, 960.94
Duration      [total, attack, wait]             5m5s, 5m0s, 5.226s
Latencies     [min, mean, 50, 90, 95, 99, max]  721.294µs, 900.551ms, 249.588ms, 1.852s, 3.034s, 16.473s, 30.034s
Bytes In      [total, mean]                     671557936, 2238.53
Bytes Out     [total, mean]                     0, 0.00
Success       [ratio]                           97.77%
Status Codes  [code:count]                      0:6697  200:293303
```

## Database connection testing

C# and MSSQL server app validation
