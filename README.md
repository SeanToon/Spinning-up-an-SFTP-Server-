# Spinning-up-an-SFTP-Server-

# Spinning-up-an-SFTP-Server-in-AWS

Steps to spin up and base level, free tier SFTP server in AWS:

Step 1 - Lauch the EC2 Instance:

    Navigate to EC2 in the search bar and then select launch instance

<img width="1696" height="607" alt="image" src="https://github.com/user-attachments/assets/ad084644-f491-49de-a2c7-393177e29259" />

    Name the instance and select the Amazon Machine Image (contains the software configuration (operating system, application server, and applications)

<img width="1230" height="910" alt="image" src="https://github.com/user-attachments/assets/65276e15-5553-4cde-b4d7-29932e765b0a" />

    Select instance type and create key or use existing key pair

<img width="1243" height="511" alt="image" src="https://github.com/user-attachments/assets/73f09174-e83f-4e92-a18a-ae508133f4c1" />

    Allow SSH (port 22) only from your IP

    Leave storage as default for free tier elegibility and then click launch instance.


Step 2 - Connect EC2 instance via SSH

    In the terminal use the following command to connect your EC2:
    The path to your .pem file should be in your downloads folder, as AWS downloaded it directly to your local computer.
    The public EC2 instance ip address is found in the instance dashboard Find your instance and the address used can be either Public IPv4 DNS or Public IPv4 address.

    Make the .pem key readable 
        command : chmod 400 your-key.pem

    SSH into server 
        command : ssh -i your-key.pem ec2-user@your-ec2-public-ip

Step 3 - Make sure that OpenSSH is installed 

    command : sudo yum update -y 
              sudo yum install -y openssh-server

Step 4 - Create user, disable shell access, and set password

    command : sudo useradd -m -g sftpusers -s /sbin/nologin sftpuser1
              sudo passwd sftpuser1

Step 5 - Create directory for root user, set ownership and permissions:

    commands : sudo mkdir -p /sftp/sftpuser1/upload
               sudo chown root:root /sftp/sftpuser1
               sudo chmod 755 /sftp/sftpuser1

               sudo chown sftpuser1:sftpusers /sftp/sftpuser1/upload

Step 6 - Configure SSHD for SFTP-Only Access

    commands : sudo vi /etc/ssh/sshd_config

               (at bottom of file)
                Match Group sftpusers (Applies the following rules to users in that group)
                    ChrootDirectory /sftp/%u (Users are jailed to their own folder)
                    ForceCommand internal-sftp (No shell access)
                    AllowTcpFowarding no (disabled for security)
                    X11Forwarding no (disabled for security)

Step 7 - Restart SSH and Test

    commands : sudo systemctl restart sshd

    Test from local machine

    command : sftp sftpuser1@hyour-ec2-ip
    
