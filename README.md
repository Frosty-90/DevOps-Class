# DevOps Coursework & Lab Notes

Practical lab exercises, terminal runs, and code snippets put together for the DevOps module. Organized by topic into individual folders, each with command logs, explanations of the output, and terminal screenshots from the actual run.

## Quick Directory Overview

| Directory | What's Inside |
|---|---|
| [`Linux Fundamentals/`](./Linux%20Fundamentals/) | Hard vs symlinks, `useradd` vs `adduser`, querying `journalctl`, and everyday command cheatsheet |
| [`Shell Scripting/`](./Shell%20Scripting/) | `sysinfo.sh` demo — shell variables, `read` prompts, file/directory creation, and standard output redirection |
| [`Networking Fundamentals/`](./Networking%20Fundamentals/) | Essential network utilities: `ping`, `ip`, `ss`, `curl`, `wget`, `nslookup`, `traceroute`, and `hostname` |
| [`Git and Github/`](./Git%20and%20Github/) | Experiments with `git commit -a` vs `-m`, and selectively pulling commits with `git cherry-pick` |
| [`Docker Fundamentals/`](./Docker%20Fundamentals/) | Six containerized "Hello World" apps: Node.js, Python/Flask, Java, Apache httpd, React + Vite, and Nginx |
| [`DockerFiles and Images/`](./DockerFiles%20and%20Images/) | Multi-stage Go container build — trimming a ~365 MB build environment down to an ultra-lean ~7 MB scratch image |
| [`Docker Networks/`](./Docker%20Networks/) | Multi-network container isolation, host networking mode, live bind mounts, and overlay network architecture |

## Test Environment

- **Host Machine:** macOS running Docker Desktop
- **Linux Testing:** Linux-specific utilities (`ip`, `ss`, `journalctl`, etc.) were executed inside Ubuntu 24.04 containers.
