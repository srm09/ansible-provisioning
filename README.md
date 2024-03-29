# Server provisioning and inventory file generation
Provisioning servers and deplyoing using ansible

## Provisioning servers
To provision servers, currently two providers namely, **DigitalOcean and Windows Azure** are being used. Sepearate accounts need to be created for both the cloud providers.

To run the provisioning script, use
```
python provision.py <Cloud_Provider> <inventory_file_write_method>
```
where Cloud_Provider can have the values

		- DigitalOcean  
		- Azure
and inventory_file_write_method can have the values
		
        - 1: Append to an existing inventory file
		- 2: Override an exisitng inventory file

### Some examples
If you want to provision a Digital Ocean droplet in a new inventory file, use
```
    python provision.py DigitalOcean 2
```
To provsion an Azure VM with an existing inventory file, use
```
    python provision.py Azure 1
```
The inventory file that is generated is of the following format:
```
berserk-pike ansible_ssh_host=104.131.105.168 ansible_ssh_user=root
agitated-einstein ansible_ssh_host=agitated-einstein.cloudapp.net ansible_ssh_user=uname ansible_ssh_private_key_file=id_rsa
```

### DigitalOcean account setup
1. Create an account at [DigitalOcean](https://cloud.digitalocean.com/registrations/new "DigitalOcean Signup")
2. Move to [API menu](https://cloud.digitalocean.com/settings/applications)
3. Generate a new token and save it for further use. This token is used to access the DigitalOcean API
4. Go to `config.ini` file and change the value of key `Access_Token` under the `DigitalOcean` section

### Azure account setup
1. Create an account at [Azure](https://azure.microsoft.com/en-us/pricing/free-trial/ "Microsoft Azure Singup")
2. Follow all key and certificate generation steps mentioned at this [Github Gist](https://gist.github.com/rchakra3/b6703a9d5c66e6fc9a7d) generously shared by [rchakra3](https://gist.github.com/rchakra3). For easier use, use names of the file as mentioned in the shared link.   
3. Change the following values in the `config.ini` file under the `Azure` section
    - *Service_Certificate_Thumbprint*: Thumbprint of the `myCert.pem` file 
    - *API_Connection_Cert_Path*: Path to `everything.pem` file
    - *Public_Key_Upload_Path*: Path of the public key to be uplaoded `id_rsa.pub`
    - *Cert_Upload_Path*: Path of the certificate to be uplaoded `myCert.pem` 
   
## Deployment using ansible
The inventory file generated using the above step can be directly consumed by an ansible playbook to make the necessary installation on the newly provisioned VMs.
As an example, a simple playbook is wirtten which installs and starts the **nginx** server on the servers provided by the inventory file.

To run the playbook we use the following command,
```
    ansible-playbook playbook.yml -i inventory
```
To increase the verbosity of the output, append `-vvvv` to the above command

## Installing dependencies
The required dependencies for this project are listed in the `requirements.txt` file. To get this project running use the following command,
```
	pip install -r requirements.txt
```

To test the dependency installation, we can use the `virtualenv` python tool used to create isolated Python environments.
Install `virtualenv` using,
```
	pip install virtualenv
``` 
Test using,
```
    virtualenv env
	source env/bin/activate
	pip install -r requirements.txt
```
