# Wetty and SSH-Server Docker Compose Configuration with Caddy Reverse Proxy

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
    AUTH_PASSWORD=<Generated Base64-encoded Password>
    ```

    - To generate the `AUTH_PASSWORD`, run the following Docker command to create the hashed password:

      ```bash
      docker run --rm caddy caddy hash-password --plaintext mypassword
      ```

    - Take the output from the command and encode it in Base64. You can use the following command:

      ```bash
      echo -n '<output>' | base64
      ```

      Replace `<output>` with the result obtained from the previous command.

3. Run the following command to start the services:

    ```bash
    docker-compose up -d
    ```

4. **Important:** Before accessing Wetty, make sure you have a domain configured and pointed to the server's IP address. Update your DNS settings accordingly.

5. Access Wetty securely in your web browser by navigating to `https://<your-domain>/wetty`.

6. Use the provided SSH credentials to log in and start using the web-based terminal.

Note: If you don't have a domain, you can use services like [DynDNS](https://dyn.com/dns/) or [No-IP](https://www.noip.com/) to get a free domain name that dynamically updates to your server's IP address. Ensure your router/firewall forwards the necessary ports (e.g., 80, 443) to your server for external access.

## Configuration

- The Wetty service is configured to run on the specified port, with SSH details passed through environment variables.
- The SSH-Server service uses LinuxServer's OpenSSH Server image and allows customization through environment variables.
- Caddy is employed as a reverse proxy to ensure secure access to the services.

## Cleanup

To stop and remove the containers, run:

```bash
docker-compose down
```

## Notes

- Make sure to secure your SSH credentials and consider using SSH key pairs for enhanced security.
- Customize the environment variables in the `.env` file to suit your preferences.

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



## Caddy Reverse Proxy Configuration

Additionally, this configuration includes Caddy as a reverse proxy to ensure secure access to the services. Below are the details for the Caddy configuration:

- Caddy listens on ports 80 and 443, providing HTTP and HTTPS access to the services.
- It is part of the `ssh-network` network for seamless communication with other services.
- Docker socket is mounted to facilitate dynamic configuration based on running containers.
- Data persistence is ensured through the `caddy_data` volume.
- Caddy automatically manages SSL certificates using Let's Encrypt.

 Refer to the Caddy documentation for more details: [Caddy Documentation](https://caddyserver.com/docs).


