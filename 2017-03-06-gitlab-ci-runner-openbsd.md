# Running the gitlab CI multi runner in OpenBSD

Wanted my OpenBSD machine to run gitlab-ci jobs, but building gitlab-ci-multi-runner requires
docker, and docker does not work in OpenBSD - which is fine since I don't want to run docker,
just the shell executor.

Turns out a quick patch to the gitlab-ci-multi-runner to remove all the uses of docker allows it to
build. The binary is statically linked so you can just place it in the path.

## Quick build instruction

Running this on ca7707b0c622c8218ffcddbdaa915cf85455d9f2 (Mar 6 2017).

	$ export GOPATH=~/Code/gopath
	$ go get gitlab.com/gitlab-org/gitlab-runner
	gopath/src/gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/docker/executor_docker.go:219: undefined: Asset

The error is normal given the circunstances. Lets start by removing the docker executor and try to build

	$ cd $GOPATH/src/gitlab.com/gitlab-org/gitlab-runner
	$ rm -rf executors/docker
	$ go build -o $GOPATH/bin/gitlab-ci-multi-runner ./main.go

This will fail with some package import errors. The following patch should fix those errors.


	diff --git a/commands/exec.go b/commands/exec.go
	index 5ae54915..a14dddfa 100644
	--- a/commands/exec.go
	+++ b/commands/exec.go
	@@ -15,7 +15,6 @@ import (
	 	"gopkg.in/yaml.v2"
	 
	 	// Force to load all executors, executes init() on them
	-	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/docker"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/parallels"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/shell"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/ssh"
	diff --git a/main.go b/main.go
	index c97db8fd..7863aa6b 100644
	--- a/main.go
	+++ b/main.go
	@@ -12,8 +12,6 @@ import (
	 
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/commands"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/commands/helpers"
	-	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/docker"
	-	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/docker/machine"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/kubernetes"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/parallels"
	 	_ "gitlab.com/gitlab-org/gitlab-ci-multi-runner/executors/shell"

Trying to build again should succeed

	$ go build -o $GOPATH/bin/gitlab-ci-multi-runner ./main.go

## rc.d service

Create a user (gitlab-ci) to run the service and create a rc.d service script as follows:

	#!/bin/sh
	daemon="/usr/local/bin/gitlab-ci-multi-runner"
	daemon_user="gitlab-ci"
	daemon_flags="run --syslog"
	
	. /etc/rc.d/rc.subr
	
	rc_reload=NO
	rc_bg=YES
	
	rc_cmd $1

## References

- http://frankgroeneveld.nl/2016/04/06/using-gitlab-ci-multi-runner-on-openbsd/

