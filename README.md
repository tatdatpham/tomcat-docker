# tomcat-docker
The tomcat docker that built with custom OpenJDK version (included beta version)

# 1. Prepare virual machine to build OpenJDK
To setup them machine, please follow the instructions from [Adotium](https://github.com/adoptium/infrastructure/tree/master/ansible)

You can drop all machine in `inventory.yml` and change to `localhost` look like this:
```
hosts:

  - infrastructure:
      - localhost:
          ubuntu1804-x64-1: {ip: 127.0.0.1, user: root, description: localhost:}

  - build:

      - localhost:
          ubuntu1804-x64-1: {ip: 127.0.0.1, user: root, description: localhost:}

  - test:

      - localhost:
          ubuntu1804-x64-1: {ip: 127.0.0.1, user: root, description: localhost:}
```

## How do I run the playbooks?

1) Install Ansible 2.4 or later (

    - On RHEL 7.x
    ```bash
    yum install epel-release
    yum install ansible
    ```

    - For Ubuntu
    ```bash
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt update
    sudo apt install ansible
    ```

    - On another system with `pip` available:
    ```bash
    sudo pip install ansible
    ```

2) Ensure that you have edited the `hosts` in `/etc/ansible/` or in the project root directory. For running locally `hosts` file should contain something as simple as `localhost ansible_connection=local`.

3) Run a playbook to install dependencies, e.g. for Ubuntu 14.x on x86:
```bash
ansible-playbook -s playbooks/AdoptOpenJDK_Unix_Playbook/main.yml --skip-tags=adoptopenjdk,jenkins
```
4) The Ansible playbook will download and install any dependencies needed to build OpenJDK

Run command to build image

# 2. Build the OpenJDK
From source: [temurin-build](https://github.com/adoptium/temurin-build)

Clone repo:
```bash
git clone https://github.com/adoptium/temurin-build
```

Run command to build OpenJDK
```
./makejdk-any-platform.sh -J /usr/lib/jvm/jdk8 -r https://github.com/openjdk/jdk8u -t jdk8u372-b06 --build-variant hotspot jdk8u -T OpenJDK8U-jdk_x64_linux_hotspot_8u372b06.tar.gz -u 376-b06 -v jdk8u
```

In the above command, I want build OpenJDK 8 version `372-b06` from source `https://github.com/openjdk/jdk8u`

To know more about option for `/makejdk-any-platform.sh`, please check the documentation [The makejdk-any-platform.sh script](https://github.com/adoptium/temurin-build#the-makejdk-any-platformsh-script)

> Noted:
> During the build process, may be you can got the error that related to sha1 check sum of JDK package from temurin, you can change to the corrected SHA1

The binary will be saved in the following directory
`/opt/temurin-build/workspace/target`

# 3. Build Eclipse-temutin image
Next step, we need to build base image that called `eclipse-temutin`, the image will be used for `tomcat` image in next section

#### For Ubuntu Focal
```
cd ./eclipse-temurin/focal
docker build -t tatdat171/eclipse-temurin:8-jdk-focal .
```

#### For Ubuntu Jammy

```
cd ./eclipse-temurin/focal
docker build -t tatdat171/eclipse-temurin:8-jdk-jammp .
```

> For the Dockerfile, you can check from origin source [Eclipse Github](https://raw.githubusercontent.com/adoptium/containers/faa5fb7621fd9b0672b83f398d3c6ee579534015/8/jdk/ubuntu/jammy/Dockerfile.releases.full)

## 2. Build Tomcat image from eclipse-temurin

Run command to build image

#### For Ubuntu Focal
```
cd ./tomcat/9/focal
docker build -t tatdat171/tomcat:9.0.73-jdk8-temurin-focal .
```

#### For Ubuntu Jammy

```
cd ./tomcat/9/jammy
docker build -t tatdat171/tomcat:9.0.73-jdk8-temurin-jammy .
```
> For the Dockerfile, you can check from origin source [Tomcat Github](https://raw.githubusercontent.com/docker-library/tomcat/9fd0c865c16751a144216186720dae7cfc9113bd/9.0/jdk8/temurin-focal/Dockerfile)

