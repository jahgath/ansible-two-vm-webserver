# Ansible Two-VM Web Server Deployment

This project demonstrates automated deployment and removal of web servers across two Google Cloud Platform (GCP) virtual machines using Ansible.

The project was created as part of an Enterprise Software Platforms assignment at San Jose State University.

## Architecture

Ansible runs from a local control machine and manages two Ubuntu virtual machines hosted on Google Cloud Platform.

```text
                    Ansible Controller
                         (Local Mac)
                             |
                       SSH / Ansible
                        /           \
                       /             \
                    VM1               VM2
               Ubuntu + Nginx    Ubuntu + Nginx
                  Port 8080         Port 8080
                       |               |
                       v               v
               Hello World       Hello World
              from SJSU-1       from SJSU-2
```

## Technologies Used

- Ansible
- Google Cloud Platform (GCP)
- Google Compute Engine
- Ubuntu Linux
- Nginx
- SSH
- Jinja2
- YAML

## Project Structure

```text
ansible-two-vm-webserver/
├── README.md
├── inventory.ini
├── deploy.yml
├── undeploy.yml
└── templates/
    └── index.html.j2
```

## Virtual Machines

Two Ubuntu virtual machines are configured as Ansible-managed hosts:

- `vm1`
- `vm2`

Ansible connects to both machines over SSH.

The inventory assigns a unique `sjsu_number` variable to each VM. This value is used by the Jinja2 template to generate a different web page for each server.

## Test Ansible Connectivity

Before deployment, connectivity to both VMs can be verified with:

```bash
ansible all -i inventory.ini -m ping
```

A successful configuration returns `pong` from both VM1 and VM2.

## Deploy

The `deploy.yml` playbook automatically:

1. Updates the Ubuntu package cache.
2. Installs Nginx.
3. Configures Nginx to listen on TCP port `8080`.
4. Generates a custom web page using a Jinja2 template.
5. Starts and enables the Nginx service.

Run the deployment with:

```bash
ansible-playbook -i inventory.ini deploy.yml
```

After successful deployment, the two servers display:

### VM1

```text
Hello World from SJSU-1
```

### VM2

```text
Hello World from SJSU-2
```

Both web servers are accessible through port `8080` of their respective VM addresses.

## Jinja2 Template

The web page is generated from:

```text
templates/index.html.j2
```

The template uses the host-specific variable:

```html
<h1>Hello World from SJSU-{{ sjsu_number }}</h1>
```

This allows the same Ansible playbook and template to generate different content for each VM.

## Undeploy

The `undeploy.yml` playbook removes the deployed web server resources.

It:

1. Stops Nginx.
2. Removes the Nginx package.
3. Removes the custom web page.
4. Removes unused packages.

Run:

```bash
ansible-playbook -i inventory.ini undeploy.yml
```

After undeployment, the web servers are no longer accessible on port `8080`.

## Security

SSH private keys and cloud credentials are not stored in this repository.

The Ansible controller uses SSH key authentication to connect securely to the remote virtual machines.

## Result

The project demonstrates how Ansible can consistently configure multiple remote servers from a single control machine.

A single deployment command configures both VMs while providing instance-specific web content:

```text
VM1 -> Hello World from SJSU-1
VM2 -> Hello World from SJSU-2
```

The corresponding undeployment playbook reverses the deployment and removes the web server resources.
