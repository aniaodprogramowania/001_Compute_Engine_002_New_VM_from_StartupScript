<p align="center">
  Google Cloud Create Simple GCP GCE VM Instance from Startup Script
</p>

## Step-01: Introduction
In this section, we will cover:
1.  Provision a Virtual Machine (VM) instance with a startup script.
2.  Verify that all steps defined in the startup script are executed during VM creation.
3.  Implement a MY-first-Startup-Script and test its execution.

# Step-02: Create VM Instance
- Enable `Google Compute Engine`
- Go to Virtual Machines -> VM Instances -> Create Instance -> New VM Instance -> Provide required details
- **Name:** my-first-startup-script
- **Region:** us-central1
- **Zone:** Any 
- **Machine Configuration:** 
  - **Series:** E2
  - **Machine Type:** e2-micro
- **Firewall**
  - Allow HTTP Traffic
- **Advanced Options**
      - **Startup Script:** Copy paste content from `wgrywka.sh` file
```t
#!/bin/bash
sudo apt install -y telnet
sudo apt install -y nginx
sudo systemctl enable nginx
sudo chmod -R 755 /var/www/html
HOSTNAME=$(hostname)
sudo echo "<!DOCTYPE html> <html> <body style='background-color:rgb(211, 211, 211);'> <h1>Super, ze mozemy sie razem uczyc! </h1> <p><strong>VM Hostname:</strong> $HOSTNAME</p> <p><strong>VM IP Address:</strong> $(hostname -I)</p> <p><strong>Application Version:</strong> V1</p> <p>Pozdrawiam, Ania</p> </body></html>" | sudo tee /var/www/html/index.html
```
- Click on **Create**

## Step-03: View Logs in Logs Explorer
- Navigate to VM Instances → my-first-startup-script → View Logs.
- Allow a few moments for the VM to complete provisioning the web server as defined in the startup script.

## Step-04: Define my Fisrt Startup Script
- In the Google Cloud Console, navigate to Compute Engine → Settings → Metadata.
- Click on **Edit**
```t
# Add Metadata Key
startup-script

# Add Metadata Value
#!/bin/bash
udo apt install -y telnet
sudo apt install -y nginx
sudo systemctl enable nginx
sudo chmod -R 755 /var/www/html
HOSTNAME=$(hostname)
sudo echo "<!DOCTYPE html> <html> <body style='background-color:rgb(250, 210, 210);'> <h1>Super, ze mozemy sie razem uczyc! </h1> <p><strong>VM Hostname:</strong> $HOSTNAME</p> <p><strong>VM IP Address:</strong> $(hostname -I)</p> <p><strong>Application Version:</strong> V1</p> <p>Pozdrawiam, Ania</p> </body></html>" | sudo tee /var/www/html/index.html
```

## Step-05: Create New VM
Project-level startup script – configured globally for the entire GCP project.
In this case, every new VM launched within the project will automatically execute this script on startup (unless it is overridden at the instance level).
If you configure both a project-level and an instance-level startup script both will be executed,the project-level script runs first, followed by the instance-level script.

- Go to Virtual Machines -> VM Instances -> Create Instance -> New VM Instance -> Provide required details
- **Name:** my-first-project-level-startupscript
- **Machine Configuration:** 
  - **Series:** E2
  - **Machine Type:** e2-micro
- **Firewall**
  - Allow HTTP Traffic
- **REST ALL LEAVE TO DEFAULTS**  
- Click on **Create**

## Step-06: Verify by accessing webserver pages
- Go to Compute Engine -> VM Instances -> **my-first-startup-script**
- If required, you can do SSH connect and verify
```t
# Access Webserver Pages
http://<external-ip-of-vm>
```

## Step-07: How to override project level Startup Script?
The override of a project-level startup script allows you to define a custom instance-level startup script for a specific VM. This script executes after the project-level script and can modify or replace its behavior (e.g., uninstall services, change configuration, install different packages). It provides flexibility to customize individual VMs without changing the global project configuration.

- Providing a startup script at the VM instance level overrides the project-level startup script metadata.
- Create a new VM
- **Name:** override-projectlevel-startup-script
- **Machine Configuration:** 
  - **Series:** E2
  - **Machine Type:** e2-micro
- **Firewall**
  - Allow HTTP Traffic
- **Management**
  - **Automation:**
    - **Startup Script:** Copy paste content from `override-wgrywka.sh` file  
- **REST ALL LEAVE TO DEFAULTS**  
- Click on **Create**

  ## Step-08: Verify by accessing webserver pages
- Go to Compute Engine -> VM Instances -> **override-projectlevel-startup-script**
- If needed, you can connect via SSH to verify
```t
# Access Webserver Pages
http://<external-ip-of-vm>
```

## Step-09: Delete VMs
```t
# Delete VMs
my-first-project-level-startupscript
override-projectlevel-startup-script
```

## Step-10: To prevent incurring additional charges, stop the VM when it is not in use
- **Additional Observation:** When VM is stopped, Public IP or External IP is released


## Step-11: Delete the project-level metadata entry that was configured for the startup scripts
- Go to Compute Engine -> Settings -> Metadata -> Edit
- Delete `startup-script` Metadata
- Click on **SAVE**
