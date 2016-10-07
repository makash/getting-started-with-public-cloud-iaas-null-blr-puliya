# Getting Started with Cloud IAAS

![](docs/images/Selection_009.png)

## Create a new free-tier account
![](docs/images/Selection_011.png)

![](docs/images/Selection_012.png)

![](docs/images/Selection_014.png)

![](docs/images/Selection_015.png)

![](docs/images/Selection_016.png)

## Log in
![](docs/images/Selection_010.png)

![](docs/images/Selection_019.png)

## Setup a new `Virtual Machine`

### Using the AWS Console

![](docs/images/Selection_020.png)
![](docs/images/Selection_022.png)

![](docs/images/Selection_023.png)
![](docs/images/Selection_024.png)

#### Keep in mind

![](docs/images/Selection_025.png)
![](docs/images/Selection_026.png)

![](docs/images/Selection_028.png)

### Configuring Security Groups (Network Firewall?)
![](docs/images/Selection_030.png)
![](docs/images/Selection_031.png)
![](docs/images/Selection_032.png)
![](docs/images/Selection_033.png)
![](docs/images/Selection_034.png)
![](docs/images/Selection_035.png)


Meanwhile 
![](docs/images/Selection_036.png)


### Connecting to the machine

![](docs/images/Selection_037.png)
![](docs/images/Selection_038.png)

### Windows User please pay attention 
You can't use the `pem` files directly in `Putty`. 

What are your options? 
You can install something like `gitbash` and get the SSH command like experience. Or you can convert the key to the `.ppk` format. 

![](docs/images/Selection_039.png)

Finally Shell!!

![](docs/images/Selection_040.png)

### Using the CLI (Command Line)

#### Setup required for CLI usage

##### Installation


##### AWS Configuration

Before we can use the CLI, we need to do some more work in AWS.

![](docs/images/Selection_041.png)

![](docs/images/Selection_042.png)

![](docs/images/Selection_043.png)

![](docs/images/Selection_045.png)

![](docs/images/Selection_046.png)

**Please note, right now we are focussing on getting productive as soon as possible, so not thinking about security**

![](docs/images/Selection_048.png)

![](docs/images/Selection_049.png)

![](docs/images/Selection_050.png)

Configure AWS CLI client for usage 

    aws configure

![](docs/images/Selection_047.png)


Setup command completion

    complete -C /usr/local/bin/aws_completer aws


Create new security group 
    
    aws ec2 create-security-group --group-name 'null-puliya' --description 'for null puliya'

    {
    "GroupId": "sg-babf16c3"
    }

Allow connection to TCP Port 22 from anywhere
    
    aws ec2 authorize-security-group-ingress --group-name null-puliya --protocol tcp --port 22 --cidr 0.0.0.0/0 

Generate a new key pair

    aws ec2 create-key-pair --key-name nullpuliya2 --query 'Key' --output text > nullpuliya2.pem

Start a new instance

    aws ec2 run-instances --image-id ami-a24598c2 --security-group-ids sg-babf16c3 --count 1 --instance-type t2.micro --key-name nullpuliya2 --query 'Instances[0].InstanceId' 

- `ami-a24598c2` is an official Ubuntu 14.04 image
- `sgbabf16c3` is what we created
- `nullpuliya2` is the key pair name
- `--query` `'Instances[0].InstanceId` gives us an output "i-0a0a6e883ba09caf4"

Get the public IP

    aws ec2 describe-instances --instance-ids i-0a0a6e883ba09caf4 --query 'Reservations[0].Instances[0].PublicIpAddress'

    "54.149.235.153"

Log In

    ssh -i keys/nullpuliya2.pem -l ubuntu 54.149.235.153

Start a SOCKS Proxy so that you can send data through the newly setup Linux server 

    ssh -i keys/nullpuliya2.pem -l ubuntu 54.149.235.153 -D 12345

Now in Burp Suite or any other tool give the following for `SOCKS` proxy

    `SOCKS Proxy Host` 127.0.0.1
    `SOCKS Proxy Port`  12345


### Using Kali Sources 

Run the following commands

    gpg --keyserver pgpkeys.mit.edu --recv-key  ED444FF07D8D0BF6
    gpg -a --export ED444FF07D8D0BF6 | sudo apt-key add -

Add the following line to a new file in `/etc/apt/sources.list.d/kali.list`

    deb http://http.kali.org/kali kali-rolling main contrib non-free

Now update

    apt-get update

    apt-get install kali-linux-forensic


## Installing OpenVPN
    wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh

Now just download the `.ovpn` file and start `openvpn` like this

    openvpn --config /path/to/.ovpn/file

## Installing Packer (Demo)

Download Packer and add it to your path

    packer validate templates/example.json

    packer build \
    -var 'aws_access_key=AKIAI6WLFAA5IMID6MAA' \
    -var 'aws_secret_key=6dQm1RmrtiefJqIy9td6ZgJWAw16C1C2hgmYeeGA'  \
    templates/example.json 


How about we install a proper Kali 2.0 rolling as an AMI?

    wget https://github.com/chrisanthropic/packer-debian2kali-ec2/archive/master.zip
    unzip master.zip

After carefully adding variables
    
    packer build kali.json


    ==> Builds finished. The artifacts of successful builds are:
    --> amazon-ebs: AMIs were created:

    us-west-2: ami-e22bf582


## RDS as a database option

Setting up 

![](docs/images/Selection_051.png)

![](docs/images/Selection_052.png)

![](docs/images/Selection_053.png)

![](docs/images/Selection_054.png)

![](docs/images/Selection_055.png)

![](docs/images/Selection_057.png)

![](docs/images/Selection_058.png)

Connecting

    aws ec2 run-instances --image-id ami-a24598c2 --count 1 --instance-type t2.micro --key-name devenv-key --security-group-ids sg-ffe54c86 --subnet-id subnet-210d9979

    ssh -l ubuntu 54.149.198.155 -i keys/nullpuliya2.pem -o ServerAliveInterval=30

    mysql -u nullpuliyadba -h  -p 

![](docs/images/Selection_059.png)

![](docs/images/Selection_060.png)

### Adding extra Hard Disks

![](docs/images/Selection_064.png)
![](docs/images/Selection_065.png)
![](docs/images/Selection_066.png)

## Google Cloud

### Register

![](docs/images/Selection_068.png)
![](docs/images/Selection_070.png)
![](docs/images/Selection_071.png)
![](docs/images/Selection_072.png)
![](docs/images/Selection_073.png)
![](docs/images/Selection_074.png)

#### Google Init

![](docs/images/Selection_075.png)
![](docs/images/Selection_076.png)
![](docs/images/Selection_082.png)


    echo 'source ~/azure.completion.sh' >> ~/.bash_profile


    gcloud compute --project "voltaic-range-145017" instances create "instance-1" --zone "us-central1-b" --machine-type "n1-standard-1" --subnet "default" --maintenance-policy "MIGRATE" --scopes default="https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring.write","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly" --image "/debian-cloud/debian-8-jessie-v20160923" --boot-disk-size "10" --boot-disk-type "pd-standard" --boot-disk-device-name "instance-1"

SSH

    gcloud compute --project "voltaic-range-145017" ssh --zone "us-central1-b" "instance-1"

![](docs/images/Selection_083.png)


## Azure

    npm -g install azure

    azure

    azure login

Installing Linux

azure vm quick-create \
-g exampleResourceGroup \
-n exampleVMName \
-l westus \
-y Linux \
-u exampleAdminUser \
-M keys/nullpuliya2.pem \
-Q UbuntuLTS

*Basically Azure has no free things that I could try, so not covering it. But shared the command above.*

### Some screenshots for Azure

![](docs/images/Selection_085.png)
![](docs/images/Selection_086.png)
![](docs/images/Selection_088.png)


### Fin
