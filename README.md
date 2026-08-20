In this project we create DO droplet as a dedicated ansible server.
We run jenkins pipeline which runs ansible playbook from DO droplet to configure EC2 instances.
Whose IPs are taken from ansible dynamic inventory script, and runs docker compose on them.


Create new DO droplet
SSH into it
    apt update
    ansible
    apt install ansible-core
    ansible --version
    apt install python3-boto3

create ~/.aws/credentials and copy from local machine ~/.aws/credentials file to it, or create new one with access_key and secret_key


launch 2 aws ec2 instances via aws ui


configure jenkins credentials for droplet and aws ec2 instances

run jenkins pipeline
