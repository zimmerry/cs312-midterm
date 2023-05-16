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
