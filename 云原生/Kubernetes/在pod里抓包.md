1. 运行以root权限运行

```
securityContext:
  allowPrivilegeEscalation: false
  capabilities:
    drop:
    - ALL
  privileged: false
  readOnlyRootFilesystem: true
  runAsNonRoot: true
```

```
securityContext:
  runAsNonRoot: false
  runAsUser: 0
```
2. pod内部

```
zypper addrepo -C -G -f https://arm.rnd.ki.sw.ericsson.se/artifactory/proj-ldc-repo-rpm-local/common_base_os/sles/5.8.0-21?ssl_verify=no COMMON_BASE_OS_SLES_REPO
zypper addrepo -f https://arm.sero.gic.ericsson.se/artifactory/proj-suse-repos-rpm-local/SLE15/SLE-15-SP3-Module-Basesystem/ BASE_SYS_REPO
zypper addrepo -f https://arm.sero.gic.ericsson.se/artifactory/proj-suse-repos-rpm-local/SLE15/SLE-15-SP3-Module-Basesystem-Updates/ BASE_SYS_REPO_UPDATES

zypper install tcpdump

tcpdump -i any port 8282 -s0 -w 8282.cap -vvv
```
3. 拷贝到本地使用wireshark分析

`kubectl cp <pod>:<filepath.cap> <localTargetPath> -n <namespace>`

4. protocal 里选择对应协议

