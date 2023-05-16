# cs312-midterm

## Create your instance
1. Navigate to the EC2 dashboard
2. Click on "Launch Instance"
3. For Name, enter a name like "EC2 Minecraft Server"
4. Under Application and OS Image, choose the Ubuntu AMI
5. Select 64-bit (x86) as the architecture
6. Select any instance type (minimum t2.micro)
7. Under Key pair, select an existing Key pair (if you have one). Create a new key pair if you don't. Save the private key
8. Under Network settings, click Edit
  8.1. Under Security group name, enter a name like "minecraft-server"
  8.2. Change the Description to something descriptive like "Minecraft Server"
  8.3. Click on Add security group rule
  8.4. For Type, select Custom TCP
  8.5. For Source type, select Anywhere
  8.6. For Port range, enter 25565 (minecraft's default port)
  8.7. For Description, enter something descriptive like "Minecraft"
9. Leave everything else as-is and click Launch Instance

## Setup the Minecraft Server

### Log into the server
1. Select the instance that you just created
2. Click on Connect
3. Click on the SSH client tab
4. Follow the instructions in the SSH client tab
5. If you see an Ubuntu welcome screen, you're connected!

### Install dependencies
1. Install and set up [Docker Engine](https://docs.docker.com/engine/install/) - follow Docker's documentation or the steps here:
2. Use the convenience script:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
#### Set up the docker repository 
  
1. Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
```
2. Add Docker's official GPG key:
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
3. Use the following command to set up the repository:
```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
#### Install Docker Engine
1. Update the `apt` package index:
```bash
sudo apt-get update
```
2. Install Docker Engine, containerd, and Docker Compose:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
3. Verify that the Docker Engine installation is successful by running the `hello-world` image.
```bash
sudo docker run hello-world
```
#### Do the [Linux post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/) for Docker Engine:
1. Create the `docker` group:
```bash
sudo groupadd docker
```
2. Add your user to the `docker` group:
```bash
sudo usermod -aG docker $USER
```
3. Log out and log back in
4. Verify that you can run `docker` commands without `sudo`:
```bash
docker run hello-world
```
5. Configure Docker to start on boot
```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```
### Setup the Minecraft server with Docker
1. Create a new folder
```bash
mkdir minecraft
cd minecraft
```
2. Create a Docker Compose yaml file
```bash
nano compose.yml
```
3. Add the following to `compose.yml` - this configures the Minecraft server and [watchtower](https://containrrr.dev/watchtower/), which will automatically update the Minecraft docker image periodically:
```yaml
services:
  minecraft:
    image: itzg/minecraft-server
    container_name: minecraft
    environment:
      EULA: "TRUE"
    tty: true
    stdin_open: true
    volumes:
      - ./minecraft-data:/data
    ports:
      - 25565:25565
    restart: always
  watchtower:
    image: containrrr/watchtower
    container_name: watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always
```
