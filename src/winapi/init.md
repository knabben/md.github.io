# Windows notebook

Notes from Windows Internal 7th Edition and general debugging rules

[Chapter 1](./1.md)

## Changing containerd service

```shell
sc.exe config containerd \
  binPath="c:\Program Files\containerd\containerd.exe \
  --run-service --log-file=c:\tmp\containerd.log"
```

## References 

[Debugger tools PS1](https://gist.github.com/knabben/2482cb672aeb3fbfe32d14fe3cbd70a0)

