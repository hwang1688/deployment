# Deployment Project
This project explores different ways to deploy software builds to target machines using Ansible and Jenkins.

## Deployment Projects
### Spring Boot Application (spring-boot-sample)
This deployment uses Ansible to deploy the sample Spring Boot application to a JALP (Java Apache Linux Postgres) VM. 

The project directory is `spring-boot-sample`.

The Ansible main playbook file is `playbook.yml`. 

The playbook uses Ansible Vault file `vars/vault.yml` to store the sensitive data:
* `ansible_user`, `ansible_password`: the username and password used to connect to the target VM
* `ansible_become_pass`: the password for `ansible_user` to become the super user
* `app_download_url_base`: the Jenkins server that hosts the build artifacts and the app can be downloaded
* `app_download_url_user`: the Jenkins server user used to download the build artifacts
* `app_download_url_password`: The Jenkins API key for the Jenkins user

The playbook deploys the role `spring-boot-sample`.

The `spring-boot-sample` role's main task file does the following:
* Create a group and a user with the name `{{ app_user }}` for the Spring Boot App to run under. 
* Check if the systemd service file for the app exists.
* If the systemd service exists, shut it down.
* Deploy the Spring Boot executable jar in the `/opt` directory under the user `{{ app_user }}` by downloading
  it from the Jenkins server that holds the last stable build.
* Deploy the systemd service file from a j2 template.
* Reload the systemd service and enable it for start at boot time.
