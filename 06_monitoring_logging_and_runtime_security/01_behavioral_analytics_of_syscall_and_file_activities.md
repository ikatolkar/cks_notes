# Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities

## Falco
Reference:
https://falco.org/docs/
https://learn.sysdig.com/falco-101
https://github.com/falcosecurity/charts

1. What is falco?
	- Real-time
	- Cloud native
	- Runtime Security Tool
	- for linux operating system
2. What does it do?
	- detects and alerts 
	- on abnormal behavior
	- and potential security threats in real-time
3. How does it work?
	- it is a kernel monitoring agent
	- it observes events like syscalls, based on custom rules
	- it enhances these events by integrating metadata from container runtime and kubernetes.
4. Components
	- Rules
	- Plugins
5. What can be found using Falco?
	- Privilege escalation using privileged containers
	- Namespace changes using tools like "setns"
	- Read/Writes to well-known directories like "/etc/", "/usr/bin", "/usr/sbin"
	- Creating symlinks
	- Ownership and Mode changes
	- Unexpected network connections or socket mutations
	- Spawned processes using "execve"
	- Executing shell binaries like "sh", "bash" etc
	- Executing SSH binaries like "ssh", "scp", "sftp"
	- Mutating linux coreutils executables
	- Mutating login binaries
	- Mutating "shadowutil" or "passwd" executables like pwck, chpasswd, getpasswd, change, useradd, etc

### Falco Rules
Falco Rules contain 3 elements:
1. Rules
	- Conditions, under which an alert should be generated
	- Contains, an "output string" which must be sent with alert
2. Macros
	- Rule condition snippets that can be re-used inside rules or other macros
3. Lists
	- Collection of items that can be included in rules, macros, lists

Official Falco Rules:
https://github.com/falcosecurity/rules

### Supported Events
1. Syscall Events
	- open, close, read, write, exec, execve
2. Tracepoint Events
	- internal kernel events
	- that may or may not translate directly to a syscall
3. Meta Events
	- generated from supplementary data sources
	- may be generated during data enrichment process, or due to some asynchronous action
	- contains the falco "drop" events
4. Plugin Events
	- plugin event data

### Example Rules
Example rule
```yaml
- rule: shell_in_container
  desc: notice shell activity within a container
  condition: >
    evt.type = execve and 
    evt.dir = < and 
    container.id != host and 
    (proc.name = bash or
     proc.name = ksh)    
  output: >
    shell in a container
    (user=%user.name container_id=%container.id container_name=%container.name 
    shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline)    
  priority: WARNING

```

### Conditions
Condition Syntax
1. Direction
- Entry Event >
- Exit Event  <

2. Example
```bash
evt.type = execve and evt.dir = < and container.id != host and proc.name = bash
```

### Falco Outputs
Reference: https://falco.org/docs/outputs/
1. Output Channels
	- Standard output
		- Enabled in configuration
			```yaml
			stdout_output:
				enabled: true
			```
		- Visible via "kubectl logs falco-pod"
	- file
		- Enabled in config as
			```yaml
			file_output:
				enabled: true
				keep_alive: false
				filename: ./events.txt
			```
	- syslog
		- configuration
			```yaml
			syslog_output:
				enabled: true
			```
	- to a spawned program
		- configuration
			```yaml
			program_output:
				enabled: true
				keep_alive: false
				program: mail -s "falco notification" someone@example.com
			```
		- example with input
			```yaml
			program_output:
				enabled: true
				keep_alive: false
				program: "xargs -I {} aws --region ${region} sns publish"
			```
		- example post to slack channel
			```yaml
			# Whether to output events in json or text
			json_output: true
			…
			program_output:
			  enabled: true
			    program: "jq '{text: .output}' | curl -d @- -X POST https://hooks.slack.com/services/XXX"

			```
	- http/https endpoint
		```yaml
		json_output: true
		...
		http_output:
		  enabled: true
		    url: http://some.url/some/path/
			json_output: true
			...
			http_output:
			  enabled: true
			    url: http://some.url/some/path/

		```
	- grpc client

2. Output Formatting
3. Forwarding Alerts

### Install and run
1. Install
	- Falco is installed directly on host
	- can be installed as deb/rpm
	- contains a few systemd services
	- Check installation
		```bash
		sudo systemctl list-unit-files "falco*"

		sudo systemctl enable falco-modern-bpf.service

		sudo systemctl list-unit-files "falco*"
		```
	- Can also be launched as a "privileged/unprivileged docker container"
	- Can also be installed as a "helm release"
2. Running
	- Reloading and forcing config
	```bash
	kill -1 $(cat /var/run/falco.pid)
	```
	- start falco
	```bash
	falco
	```

### Falco Example Rule
Simple Rule
```yaml

- rule: The program "curl" is run in a container
  desc: An event will trigger every time you run cat in a container
  condition: evt.type = execve and container.id != host and proc.name = cat
  output: "curl was run inside a container(user=%username container=%container.name image=%container.image proc=%proc.cmdline)"
  priority: INFO

  priority: INFO
```

### Detect CVE-2020-8557 using Falco
Reference: https://falco.org/blog/detect-cve-2020-8557/

### Container runtime security using Falco
Refernce: https://youtu.be/VEFaGjfjfyc
