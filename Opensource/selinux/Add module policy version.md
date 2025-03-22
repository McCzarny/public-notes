Adding new flag to allow defining module version

Example file: mymodule.te

```shell
module mymodule 1.0;

require {
	class file {open read write};
	type httpd_t;
	attribute non_security_file_type;
};

allow httpd_t non_security_file_type:file { open read write };
```

To build module:

```shell
make -f /workspaces/refpolicy/support/Makefile.devel mymodule.pp
```

To check module version:

```shell
sedismod mymodule.pp
```

Testing with docker:

```shell
docker run -it -v .:/workdir --name selinux-test rockylinux:9.3.20231119
```

Current policy vers:

[https://github.com/SELinuxProject/selinux/blob/main/libsepol/include/sepol/policydb/policydb.h](https://github.com/SELinuxProject/selinux/blob/main/libsepol/include/sepol/policydb/policydb.h)