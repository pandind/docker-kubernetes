## About This Project
Docker and Kubernetes with Swarm. My personal notes, projects and configurations.

## Author
Aditya Hajare ([Linkedin](https://in.linkedin.com/in/aditya-hajare)).

## Current Status
WIP (Work In Progress)!

## License
Open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT).

----------------------------------------

## Important Notes
- [Must Check Links](#must-check-links)
- [Docker Installation Tips](#docker-installation-tips)
- [Theory](#theory)
- [Generic Examples](#generic-examples)

----------------------------------------

## Must Check Links
- The Cloud Native Trail Map is CNCF's recommended path through the cloud native landscape. The cloud native landscape, serverless landscape, and member landscape are dynamically generated on this website:
    * [https://landscape.cncf.io](https://landscape.cncf.io)
- MacOS shell tweaking:
    * [https://www.bretfisher.com/shell](https://www.bretfisher.com/shell)
- MacOS - Commands for getting into local Docker VM:
    * [https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm](https://www.bretfisher.com/docker-for-mac-commands-for-getting-into-local-docker-vm)
- Windows - Commands for getting into local Docker Moby VM:
    * [https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm](https://www.bretfisher.com/getting-a-shell-in-the-docker-for-windows-vm)
- Docker Internals - Cgroups, namespaces, and beyond: what are containers made from?:
    * [https://www.youtube.com/watch?v=sK5i-N34im8&feature=youtu.be&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl](https://www.youtube.com/watch?v=sK5i-N34im8&feature=youtu.be&list=PLBmVKD7o3L8v7Kl_XXh3KaJl9Qw2lyuFl)
- Windows Containers and Docker: 101:
    * [https://www.youtube.com/watch?v=066-9yw8-7c](https://www.youtube.com/watch?v=066-9yw8-7c)

----------------------------------------

## Docker Installation Tips

```diff
+ Installing on Windows 10 (Pro or Enterprise)
```
- This is the best experience on Windows, but due to OS feature requirements, it only works on the Pro and Enterprise editions of Windows 10 (with latest update rollups). We need to install [Docker for Windows](https://www.docker.com/docker-windows) from the Docker Store.
- With this Edition we should use PowerShell for the best CLI experience.
- Install [Docker Tab Completions For PowerShell Plugin](https://github.com/matt9ucci/DockerCompletion).
- Useful commands:
    ```sh
    docker version
    docker ps
    docker info
    ```

```diff
+ Installing on Windows 7, 8, or 10 Home Edition
```
- Unfortunately, Microsoft's OS features for Docker and Hyper-V don't work in these older versions, and `Windows 10 Home` edition doesn't have Hyper-V, so we'll need to install the [Docker Toolbox](https://docs.docker.com/toolbox/overview/), which is a slightly different approach to using Docker with a VirtualBox VM. This means Docker will be running in a Virtual Machine that sits behind the IP of our OS, and uses NAT to access the internet.
- **NOTE FOR TOOLBOX USERS**: On localhost, all urls that use `http://localhost` , we'll need to replace with `http://192.168.99.100`
- Useful commands:
    ```sh
    docker version
    docker-machine ls
    docker-machine start
    docker-machine help
    docker-machine env default
    ```

```diff
+ Installing on Mac
```
- We'll want to install [Docker for Mac](https://www.docker.com/docker-mac), which is great. If we're on an older Mac with less than `OSX Yosemite 10.10.3`, we'll need to install the [Docker Toolbox](https://docs.docker.com/toolbox/overview/) instead.
- Useful commands:
    ```sh
    docker version
    docker container
    docker container run --
    docker
    docker pause
    ```

```diff
+ Installing on Linux
```
- **Do not use built in default packages like `apt/yum install docker.io`** because those packages are old and not the Official Docker-Built packages.
- Prefer to use the Docker's automated script to add their repository and install all dependencies: `curl -sSL https://get.docker.com/ | sh` but we can also install in a more manual method by following specific instructions on the Docker Store for our distribution, like [this one for Ubuntu](https://store.docker.com/editions/community/docker-ce-server-ubuntu).
- Useful commands:
    ```sh
    # http://get.docker.com
    curl -fsSL get.docker.com -o get-docker.sh
    sh get-docker.sh
    sudo usermod -aG docker bret
    sudo docker version
    docker version
    sudo docker version
    docker-machine version
    # http://github.com/docker/compose
    # http://github.com/docker/compose/releases
    curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose- `uname -s `- `uname -m` >/usr/local/bin/docker-compose
    docker-compose version
    # http://github.com/docker/machine/releases
    docker image
    docker image ls --
    ```

```diff
+ Play With Docker (PWD) Online
```
- The best free online option is to use [play-with-docker.com](http://play-with-docker.com/), which will run one or more Docker instances inside our browser, and give us a terminal to use it with.
- We can actually create multiple machines on it, and even use the URL to share the session with others in a sort of collaborative experience.
- **It's only limitation really is it's time bombed to 4 hours, at which time it'll delete our servers.**

----------------------------------------

## Theory

```diff
- Difference between Containers and Virtual Machines (VMs)
```
- **Containers:**
    * Containers aren't Mini-VM's.
    * Containers are just processes. They are processes running in our host OS.
    * Containers are limited to what resources they can access.
    * Containers exit when process stops.
- A VM provides an abstract machine that uses device drivers targeting the abstract machine, while a container provides an abstract OS.
- A para-virtualized VM environment provides an abstract hardware abstraction layer (HAL) that requires HAL-specific device drivers.
- Typically a VM will host multiple applications whose mix may change over time versus a container that will normally have a single application. However, it’s possible to have a fixed set of applications in a single container.
- Containers provide a way to virtualize an OS so that multiple workloads can run on a single OS instance.
- With VMs, the hardware is being virtualized to run multiple OS instances.
- Containers’ speed, agility, and portability make them yet another tool to help streamline software development.

```diff
- To see what's going on in containers
```
- List all processes in one container:
    ```sh
    docker container top
    ```
- To see details of specific container config:
    ```sh
    docker container inspect
    ```
- To see live performance stats for all containers:
    ```sh
    docker container stats
    ```

----------------------------------------

## Generic Examples

```diff
+ Running 3 Containers: nginx (80:80), mysql (3306:3306), httpd (Apache Server - 8080:80)
```
- **MySQL**:
    * To run `MySQL`:
        ```sh
        docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
        ```
    * View logs to see generated random password for the `root` user. To view logs:
        ```sh
        docker container logs db # 'db' is the name we have given in above command.
        ```
    * Look for something like below line for the generated random password:
        ```
        2020-03-14 12:20:33+00:00 [Note] [Entrypoint]: GENERATED ROOT PASSWORD: ChooHxafdsasd2dsx1ouovo7aegha
        ```
- **httpd (Apache Server)**:
    * To run `httpd`:
        ```sh
        docker container run -d -p 8080:80 --name webserver httpd
        ```
- **nginx**:
    * To run `nginx`:
        ```sh
        docker container run -d -p 80:80 --name proxy nginx
        ```
- All containers are running by now. To list all running containers:
    ```sh
    docker container ls
    # OR
    docker ps   # Old command
    ```
- To clean these containers up:
    ```sh
    docker container stop   # Press TAB to get a list of all running containers
    # OR
    docker container stop proxy webserver db
    ```
- To remove these containers:
    ```sh
    docker container ls -a  # This will give list of all containers, even stopped ones.

    # To remove containers, specify their ids like below:
    docker container rm b520f9b00f89 5eaa2a2b09c6 c782914b7c66
    ```
- To remove `Images` as well:
    ```sh
    # To remove images, specify their ids like below:
    docker image rm b520f4389 5eaa22b09c6 c782432b7c66
    ```

```diff
+ To get a Shell inside Container
```
- To start new Container interactively:
    ```sh
    docker container run -it
    ```
- To run additional command in existing container:
    ```sh
    docker container exec -it
    ```
- For e.g. To start a `httpd` container interactively and get a `bash` shell inside it:
    ```sh
    docker container run -it --name webserver httpd bash
    ```
- For e.g. To get a `bash` shell inside already running `nginx` Container named as `proxy`:
    ```sh
    docker container exec -it proxy bash
    ```

----------------------------------------
