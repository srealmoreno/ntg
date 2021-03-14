% ntg(1) ntg User Manuals
% Srealmoreno - Salvador Real
% October 30, 2020

[no-Justify, nowrap]: <> (%.nh %.ad l)

# NAME

**ntg** - Convert Topology Netgui to GNS3 with Docker

# SYNOPSIS

**ntg** {all,topology,config} ...

# DESCRIPTION

Convert full Netgui projects to GNS3 projects with Docker, using a container appropriate for the network. With this tool you can get over the old and obsolete netgui. GNS3 with docker is more stable, more optimal, lighter, faster and more modern.

**all** (**ntg** all)

> Convert topology and config files.  
> This option converts the topology and configuration files.

**topology** (**ntg** topology)

> Convert only topology.  
> This option converts topology whitout configuration files.

**config** (**ntg** config)

> Convert only config files.  
> This option converts configuration files. A similar GNS3 topology created previously is required.  
> for example: if there is a node named "pc1" in the netgui project, there should be a node named "pc1" in the GNS3 project.
> If the node exists, the configuration files from the Netgui node will be copied to the GNS3 node.

# SUBCOMMAND "all" and "topology" OPTIONS

## OPTIONALS

**-i** DOCKER_IMAGE, **\--image** DOCKER_IMAGE

> Specifies the name of the docker image.  
> This will be used for the 3 types of nodes (Router, Switch, and Computer), therefore the image must support all 3 types.  
> default: srealmoreno/rae:latest

**-n** OUTPUT_NAME, **\--name** OUTPUT_NAME

> Specifies the name that the output project will have.  
> If not specified, the name of the Netgui project will be taken.

**-t** TEMPLATE_ID, **\--template** TEMPLATE_ID

> Specifies the ID of the Docker template.  
> Each GNS3 node comes from a template, this template has an ID.  
> If not specified, the template ID will be searched in file "gns3_controller"

**-r** GNS3_CONTROLLER, **\--read** GNS3_CONTROLLER

> Specifies path of gns3_controller.  
> The template ID will be searched here in case it is not specified (-t)  
> default: ~/.config/GNS3/GNS3_VERSION/gns3_controller.conf

**-o** OUTPUT_FOLDER, **\--output** OUTPUT_FOLDER

> Specifies the folder where the output project will be saved.  
> default: ~/GNS3/projects/

**-h**, **\--help**

> Show subcommand "all" or "topology" help message and exit

## REQUIRED

netgui_project

> Project netgui (folder or file \*.nkp) to convert

# SUBCOMMAND "config" OPTIONS

## OPTIONAL

**-h**, **\--help**

> Show subcommand "config" help message and exit

## REQUIREDS

netgui_project

> Project netgui (folder or file \*.nkp)

gns3_project

> Project GNS3 (filder or file \*.gns3)

# GENERAL ARGUMENTS OPTIONAL

**-h**, **\--help**

> Show help message and exit

**-v**, **\--version**

> Show program version and exit

# EXAMPLES

**ntg** \--help

> This will show the general help

**ntg** config \--help

> This will show help for the "config" subcommand

**ntg** all ~/Download/OSPF/netgui.nkp

> This will convert the full project named "OSPF" with the docker image
> "srealmoreno:rae/latest" and save it in the folder '~/GNS3/Projects/OSPF'

**ntg** all -i ubuntu:bionic ~/Download/TCP/netgui.nkp

> This will convert the full project named "TCP" with the docker image
> "ubuntu:bionic" and save it in the folder '~/GNS3/Projects/OSPF'

**ntg** topology -i srealmoreno/rae:bionic -t 5ea184b7-dd83-47c3-be2f-095e78c0b3a3 -o ~/Desktop -n lab-bgp ~/Download/BGP/

> This will convert only topology the project named "BGP" with the docker image
> "srealmoreno/rae:bionic", template id "5ea184b7-dd83-47c3-be2f-095e78c0b3a3" and save it in the folder '~/Desktop/lab-bgp'

**ntg** config ~/Download/BGP/netgui.nkp ~/GNS3/Projects/Lab-BGP/Lab-BGP.gns3

> This will convert only the configuration files from the project named "BGP" (type Netgui) to the project named "Lab-BGP" (type GNS3)

# ABOUT THE DOCKER IMAGE

If you want to use a custom docker image, there are a few points to consider. I assume that the reader should know the following topics.

1. Docker
2. DockerFile
3. Persistent directories
4. Bash scripting and bashrc
5. Understand about docker templates in GNS3

Points to consider:

- There are no persistent files:

> In GNS3 with docker there are no persistent files, only persistent directories. (You can see it by right clicking the node -> configure -> advanced -> persistent directories), it means that if you want to keep a file even if you turn off the node, you will have to add the absolute path of its folder in the persistent directories.
>
> Don't worry about **ntg**, it detects and creates persistent directories automatically when needed.
>
> **ntg** has a blacklist of directories that should NOT be added to persistent directories:
>
> > "/"  
> > "/dev/.\*"  
> > "/sys/.\*"  
> > "/run/.\*"  
> > "/tmp/.\*"  
> > "/lib/.\*"  
> > "/proc/.\*"  
> > "/usr/"  
> > "/etc/"  
> > "/bin/"  
> > "/opt/"  
> > "/srv/"  
> > "/var/"  
> > "/sbin/"  
> > "/home/"  
> > "/lib64/"  
> > "/etc/(?:alternatives|init.d|kernel|)/  
> > "/usr/(?:local|include|sbin|bin|share|lib)/"  
> > "/etc/(?:rc\\d\\.d+|systemd|sudoers.d|X11)/.\*"
>
> These directories should not be added as they could break the container's filesystem. If a persistent directory is detected and it is blacklisted, **ntg** will copy that folder to '/root/blackListDir/\<Dirname>'
>
> For example if ntg detects this file:  
> '/etc/network/interfaces'  
> **ntg** will add the persistent directory to '/etc/network/' (no blacklisted)
>
> '/etc/hosts'  
> **ntg** will add the persistent directory to '/root/blackListDir/etc/' (blacklisted)  
> Every time the container is initialized, the files must be copied to their original location (see initialization script)

- Script hosthome.startup:

> In Netgui projects there are files called "hosthome.startup" ("hosthome" is the name of the node). When a node is initialized if this script exists, it runs. **ntg** copies this script in the directory '/root/' each node.  
> You can add these lines to the file ".bashrc" to have the same behavior of netgui.
>
> > if [ -f ~/$(hostname).startup ]; then
> >
> > > bash ~/\$(hostname).startup
> >
> > fi
>
> for example, if there is a node named "r1" and there is also a file "r1.startup", **ntg** will copy this file to 'pc1/root/' of the GNS3 project.

- GNS3 initialization script does not support IPv6:

> Every time a container is initialized, GNS3 calls a script that initializes the network interfaces and persistent directories. You can see it in '/gns3/init.sh' this script uses BusyBox v1.24.2, BusyBox is a software suite that provides several Unix utilities in a single executable file. So if you have an IPv6 address in the '/etc/network/interfaces' file, this will fail.
>
> Therefore, if you want your container to support IPv6, you should create your own initialization script, using the packages installed in the container. For this, you must comment the lines of the GNS3 initialization script
>
> sed -i '81,87s/^#\*/#/' /usr/share/gns3/gns3-server/lib/python\*/\\  
> site-packages/gns3server/compute/docker/resources/init.sh
>
> From:
>
> > 81 # activate eth interfaces  
> > 82 sed -n 's/^ \_\(eth[0-9]\_\):.\*/\1/p' < /proc/net/dev | while read dev; do  
> > 83 ip link set dev \$dev up  
> > 84 done  
> > 85  
> > 86 # configure network interfaces  
> > 87 ifup -a -f
>
> To:
>
> > 81 # activate eth interfaces  
> > 82 #sed -n 's/^ \_\(eth[0-9]\_\):.\*/\1/p' < /proc/net/dev | while read dev; do  
> > 83 # ip link set dev \$dev up  
> > 84 #done  
> > 85 #  
> > 86 # configure network interfaces  
> > 87 #ifup -a -f

Taking these points into account, the initialization script must comply with the following parameters:

1. Copy files from a blacklisted directory to their original location
2. Run the .startup file
3. Start the network interfaces (Supporting IPv6)

# DIAGNOSTICS

**ntg** returns zero on normal operation, different 0 on error.

# BUGS

Reports bugs in <https://github.com/srealmoreno/ntg/issues>

# WIKI

<https://github.com/srealmoreno/ntg/wiki>

# LICENCE

Copyright (C) 2020 Salvador Real.

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <http://www.gnu.org/licenses/>.
