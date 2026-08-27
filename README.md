# Setting things up

## Host prerequisites
- Docker
- Docker Compose
- Git
- Bash

## Cloning
`git@github.com:MrMoleg/sampler_ws.git`

## Get the submodules
`git submodule update --init`

## Build the container
`docker compose build`
> To enable passwordless ssh into rex:
> Set $REX_CONTAINER_AUTHORIZED_KEYS to the path of a file with your public keys
> (or set it to ~/.ssh/authorized_keys to use the same keys as the host)
> **Note!** If you want to change the filepath used by the container, you have to recreate it.


## Start the ROS container
`docker compose up -d`


### To stop the container
`docker compose stop`

### Environment variables
- `ROS_ENABLE_AUTOSTART` *(somewhat works)*
    - **`0`** (default) - ROS autostart disabled
    - `1` - ROS autostart enabled
- `ROS_BUILD_ON_STARTUP` *(to be implemented)*
    - `never` - No build on startup. \
    No boot performance impact.
    - **`on-failure`** (default) - Try build only once when *ROS_ENABLE_AUTOSTART* is enabled and the startup failed (any error). 
    On successful build, startup will be retried. \
    High boot performance impact, but only when rebuild is needed (typically only once).
    - `always` - Always build on startup. \
    Very high boot performance impact.

# Container features
## SSH into the container
`ssh rex@[ip-here] -p 2822`
e.g. `ssh rex@localhost -p 2822`

> default password: `changeme`

## Build the repo
> The commands below should be executed inside the ROS container.

`cd raptor_ws`

`colcon build --symlink-install`
> If colcon fails with permission denied run:
`chmod g+rw -R /home/rex/raptor_ws`

### Source the workspace

`source install/setup.bash`


## Using the rex service
### Stop the rex main ros program:
`sudo service rex stop`

### Start the rex main ros program:
`sudo service rex start`

### Tail logfile
`sudo service rex logs`

## Setting up CAN interface to autostart properly
Example for NixOS, using systemd-networkd:
```
  systemd.network.enable = true;

  systemd.network.networks."80-can" = {
    matchConfig.Name = "can0";
    networkConfig = { };
    extraConfig = ''
      [Link]
      RequiredForOnline=no

      [CAN]
      BitRate=500000
      RestartSec=100ms
    '';
  };
```

# Build using GH actions runner
Make `.secrets` file in project directory with contents:
```
DOCKERHUB_TOKEN=<docker-hub-token>
DOCKERHUB_USERNAME=<username>
GITHUB_TOKEN=<github-token>
```
then run
```
act
```
> Note! This will build the container locally and push it to dockerhub!