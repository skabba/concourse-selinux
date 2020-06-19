# concourse-selinux
SELinux module (policy) for Concourse CI workers


# Create and load SELinux module
```
cat >/usr/local/concourse/concourse-ci-worker-policy.te <<-EOF

module concourse-ci-worker-policy 1.0;

require {
  type var_run_t;
  type unconfined_service_t;
  type irqbalance_t;
  class sock_file create;
  class cap_userns { kill net_admin setgid setuid sys_admin sys_ptrace dac_override dac_read_search setpcap sys_chroot };
}

#============= irqbalance_t ==============

#!!!! This avc is allowed in the current policy
allow irqbalance_t var_run_t:sock_file create;

#============= unconfined_service_t ==============
allow unconfined_service_t self:cap_userns { kill net_admin setgid setuid sys_admin sys_ptrace dac_override dac_read_search setpcap sys_chroot };
EOF

```
checkmodule -M -m -o /usr/local/concourse/concourse-ci-worker-policy.mod /usr/local/concourse/concourse-ci-worker-policy.te
semodule_package -o /usr/local/concourse/concourse-ci-worker-policy.pp -m /usr/local/concourse/concourse-ci-worker-policy.mod
semodule -i /usr/local/concourse/concourse-ci-worker-policy.pp
```

