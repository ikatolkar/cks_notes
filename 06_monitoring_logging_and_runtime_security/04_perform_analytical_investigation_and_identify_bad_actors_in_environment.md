# Perform deep analytical investigation and identification of bad actors within environment

## Sysdig
Reference: https://github.com/draios/sysdig/wiki/Sysdig-User-Guide

1. What is sysdig?
	- Generic linux system visibility tool
	- has good support for contaienr native exploration
2. Installed on host, or run as privileged container
3. can trace syscalls
4. Install
	```bash
	apt install sysdig
	```
5. Commands
	```bash
	sysdig
	sysdig proc.name=vi
	sysdig proc.name=curl or proc.name=vi

	sysdig -l
	sysdig proc.name=curl and container.name!=host


	sysdig -l
	sysdig proc.name=curl and container.name!=host

	```
6. Sysdig chisels, these are inbuilt scripts that analyse event stream to perform useful actions.
	```bash

	sysdig -c lscontainers
	sysdig -cl
	sysdig -c topprocs_cpu
	sysdig -c spectrogram
	sysdig -c spy_logs
	sysdig -c spy_users
	sysdig -c ps

	```

## Monitoring using sysdig
https://kubernetes.io/blog/2015/11/monitoring-kubernetes-with-sysdig/

