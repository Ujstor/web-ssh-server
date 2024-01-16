# Wetty and SSH-Server Docker Compose Configuration

This Docker Compose configuration sets up two services: Wetty and SSH-Server, allowing you to access an SSH server via a web-based terminal. Services are containerized using Docker, making it easy to deploy and manage.

## Usage

1. Clone this repository to your local machine:

    ```bash
    git clone https://github.com/ujstor/web-ssh-server.git
    cd web-ssh-server
    ```

2. Create a `.env` file in the project root and define the following variables:

    ```plaintext
    PORT=8080        # Choose the desired port for Wetty
    USER=myuser       # Set your desired username for SSH
    USER_PASSWORD=mypassword   # Set your desired password for SSH user
    ```

3. Run the following command to start the services:

    ```bash
    docker-compose up -d
    ```

4. Access Wetty in your web browser by navigating to `http://localhost:8080/wetty` (replace 8080 with the chosen port).

5. Use the provided SSH credentials to log in and start using the web-based terminal.

## Configuration

- The Wetty service is configured to run on the specified port, with SSH details passed through environment variables.
- The SSH-Server service uses LinuxServer's OpenSSH Server image and allows customization through environment variables.

## Cleanup

To stop and remove the containers, run:

```bash
docker-compose down
```

## Notes

- Make sure to secure your SSH credentials and consider using SSH key pairs for enhanced security.
- Customize the environment variables to suit your preferences.

## SSH Server env Variables


| Env                    | Function                                                                                               |
|------------------------|--------------------------------------------------------------------------------------------------------|
| PUID=1000              | User ID for the container - used to specify the user who will own the files created by the container. |
| PGID=1000              | Group ID for the container - used to specify the group who will own the files created by the container.|
| TZ=Etc/UTC             | Timezone to use for the container. See [this list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) for available timezones. |
| PUBLIC_KEY             | Optional SSH public key, automatically added to authorized_keys.                                      |
| PUBLIC_KEY_FILE        | Specify a file containing the public key (works with Docker secrets).                                   |
| PUBLIC_KEY_DIR         | Specify a directory containing only public keys (works with Docker secrets).                            |
| PUBLIC_KEY_URL         | Specify a URL containing the public key.                                                                |
| SUDO_ACCESS=false      | Set to true to allow `linuxserver.io`, the SSH user, sudo access. Without USER_PASSWORD set, this allows passwordless sudo access. |
| PASSWORD_ACCESS=false  | Set to true to allow user/password SSH access. You should set USER_PASSWORD or USER_PASSWORD_FILE as well. |
| USER_PASSWORD          | Optionally set a sudo password for `linuxserver.io`, the SSH user. If not set but SUDO_ACCESS is true, passwordless sudo access is granted. |
| USER_PASSWORD_FILE     | Specify a file containing the password. This supersedes the USER_PASSWORD option (works with Docker secrets). |
| USER_NAME=linuxserver.io | Specify a username for SSH (Default: `linuxserver.io`).                                              |
| LOG_STDOUT             | Set to true to log to stdout instead of a file.                                                       |


## Build SSH server from Dockerfile

```bash
docker build -t <image-name>
```

Environment variables:

```bash
docker run -d \
  --name=openssh-server \
  --hostname=openssh-server `#optional` \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e PUBLIC_KEY=yourpublickey `#optional` \
  -e PUBLIC_KEY_FILE=/path/to/file `#optional` \
  -e PUBLIC_KEY_DIR=/path/to/directory/containing/_only_/pubkeys `#optional` \
  -e PUBLIC_KEY_URL=https://github.com/username.keys `#optional` \
  -e SUDO_ACCESS=false `#optional` \
  -e PASSWORD_ACCESS=false `#optional` \
  -e USER_PASSWORD=password `#optional` \
  -e USER_PASSWORD_FILE=/path/to/file `#optional` \
  -e USER_NAME=linuxserver.io `#optional` \
  -e LOG_STDOUT= `#optional` \
  -p 2222:2222 \
  -v /path/to/appdata/config:/config \
  --restart unless-stopped \
  <image-name>

```

Example:

```bash
docker build -t test-ssh-server . && \
docker run \
  --name=openssh-server \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=CET \
  -e SUDO_ACCESS=true \
  -e PASSWORD_ACCESS=true \
  -e USER_PASSWORD=root \
  -e USER_NAME=ujstor \
  -p 2222:2222 \
  -v ./config:/config \
  --restart unless-stopped \
  test-ssh-server
```
