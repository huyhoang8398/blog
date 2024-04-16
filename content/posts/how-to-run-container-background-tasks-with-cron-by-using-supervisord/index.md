+++
title = "How To Run Container Background Tasks With Cron By Using Supervisord"
date = "2024-04-16T14:07:11+0000"
authors = ["kn"]
cover = "posts/how-to-run-container-background-tasks-with-cron-by-using-supervisord/cover.png"
tags = ["docker", "container", "docker-image"]
keywords = ["devops", "Linux"]
description = "Have you ever faced this scenario where you want to run two or more lightweight services or multiple executables within the same container when the image starts running?"
showFullContent = false
+++

Have you ever faced this scenario where you want to run two or more lightweight services or multiple executables within the same container when the image starts running?

Recently I worked on a PHP application that used `cron` for background processing. Since it took some time to get it right, I'm sharing the solution.

This solution runs `Cron` as a secondary process in a PHP webserver container. `Supervisor` controls these processes, and ensures that the container fails as soon as any process fails. Although SupervisorD adds another layer of complexity ,it is indeed a very simple and useful way to manage multiple services .


The Dockerfile sets the required crontab file permissions and starts Supervisor.

```yaml
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update -y && 
    apt-get install -y --no-install-recommends cron supervisor

COPY supervisord.conf /etc/supervisor/
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
```

Supervisor starts Cron with the following configuration.

```conf
[program:cron]
command = /bin/bash -c "declare -p | grep -Ev '^declare -[[:alpha:]]*r' > /run/supervisord.env && /usr/sbin/cron -f -L 15"
stdout_logfile = /dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile = /dev/stderr
stderr_logfile_maxbytes=0
user = root
autostart = true
autorestart = true

[program:php-apache]
command = apache2-foreground
stdout_logfile = /dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile = /dev/stderr
stderr_logfile_maxbytes=0
user = root
autostart = true
autorestart = true
priority = 5
```

Cron runs in foreground mode (`cron -f`) to allow supervisor to control it.

Supervisors process environment is exported to `/run/supervisord.env`. This allows Cron jobs to import the Docker/Kubernetes environment variables. The environment is imported by the following Cron job as an example.

```bash
* * * * * www-data exec /bin/bash -c ". /run/supervisord.env; /app/script.sh >> /app/cron.log"
```

# Extras, Dynamic add cron task - Command not found?

Our PHP application is dynamically add crontask and sometime it does not provide full `PATH` of the command and sometime we might want to use `ENV` variable. Cron runs commands on a time-based schedule with a minimal environment.

Cron passes a minimal set of environment variables to your jobs. To see the difference, add a dummy job like this:

```bash
* * * * * env > /tmp/env.output
```

Wait for /tmp/env.output to be created, then remove the job again. Now compare the contents of /tmp/env.output with the output of env run in your regular terminal.

A common "gotcha" here is the PATH environment variable being different. Maybe your cron script uses the command somecommand found in `/opt/someApp/bin`, which you've added to PATH in `/etc/environment`? cron ignores PATH from that file, so runnning somecommand from your script will fail when run with cron, but work when run in a terminal. It's worth noting that variables from `/etc/environment` will be passed on to cron jobs, just not the variables cron specifically sets itself, such as PATH.

To get around that, just set your own PATH variable at the top of the script. E.g.

```bash
#!/bin/bash
PATH=/opt/someApp/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# rest of script follows
```

Some prefer to just use absolute paths to all the commands instead. I recommend against that. Consider what happens if you want to run your script on a different system, and on that system, the command is in `/opt/someAppv2.2/bin` instead. You'd have to go through the whole script replacing `/opt/someApp/bin` with `/opt/someAppv2.2/bin` instead of just doing a small edit on the first line of the script.

You can also set the PATH variable in the crontab file, which will apply to all cron jobs. E.g.

```bash
PATH=/opt/someApp/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

15 1 * * * backupscript --incremental /home /root
```

So we might want to use the last option and put in our `dockerfile`

```bash
. . .
function add_shell_path_to_crontab {
	#print info about what's being added
	print_notification "Current SHELL: ${SHELL}"
	print_notification "Current PATH: ${PATH}"

	#Add current shell and path to crontab
	print_status "Adding current SHELL and PATH to crontab \nold crontab:"

	printline; crontab -u www-data -l; printline

	#keep old comments but start new crontab file
	crontab -l | grep "^#" > tmp.cron

	#Add our current shell and path to the new crontab file
	echo -e "SHELL=${SHELL}\nPATH=${PATH}\n" >> tmp.cron 

	#Add old crontab entries but ignore comments or any shell or path statements
	crontab -u www-data -l | grep -v "^#" | grep -v "SHELL" | grep -v "PATH" >> tmp.cron

	#load up the new crontab we just created
	crontab -u www-data tmp.cron

	#Display new crontab
	print_good "New crontab:"
	printline; crontab -u www-data -l; printline
}
. . .
```

Run this function script when building your docker image and you will pre-append the PATH to your cron file.
