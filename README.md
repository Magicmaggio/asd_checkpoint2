# Auto Deploy Webapp

This Ansible project aims to automate the deployment of a web project developed in PHP using the WordPress CMS.

## Project Structure

The structure of the project is as follows:
```
- roles/
    - prepare_db_server/
        - defaults/main.yml
        - handlers/main.yml
        - tasks/main.yml
    - prepare_docker/
        - defaults/main.yml
        - handlers/main.yml
        - tasks/main.yml
        - templates/
    - prepare_http_server/
        - defaults/main.yml
        - handlers/main.yml
        - tasks/main.yml
    - prepare_php/
        - defaults/main.yml
        - handlers/main.yml
        - tasks/main.yml
    - prepare_wordpress/
        - defaults/main.yml
        - files/wordpress-6.4.3-fr_FR.tar.gz
        - tasks/main.yml
        - templates/wordpress.conf.j2 & templates/wp-config.php.j2
        - handlers/main.yml
    - setup_tls_certbot/
        - defaults/main.yml
        - tasks/main.yml
        - handlers/main.yml
- inventory.yml
- playbook.yml
- requirements.txt
- readme.md
```

### Roles

This project contains the following Ansible roles:
```
- **prepare_db_server**: Installs a MariaDB database server running on a Docker container, using the official MariaDB image (version 11.2.2).
- **prepare_docker**: Installs Docker on the server and ensures that Docker containers can be managed properly.
- **prepare_http_server**: Installs and configures Apache2 as the HTTP server. It also installs the necessary modules like `rewrite` and `http2`.
- **prepare_php**: Installs PHP 8.2 and its necessary extensions to run WordPress. These extensions include `bcmath`, `mbstring`, `mysql`, and others.
- **prepare_wordpress**: Installs and configures WordPress, downloading the latest version and setting up the necessary configurations for a WordPress site.
- **setup_tls_certbot**: Installs and configures SSL certificates using Certbot. This role will automatically obtain and install a valid certificate for your domain and configure Apache to redirect HTTP traffic to HTTPS.
```
## Control Node Setup

Before starting, ensure you have the following installed on the control node:

- Python 3 (>=3.11)
- pip (>=23.2.1)
- virtualenv

### Set up the environment:

Clone this Ansible project and create a virtual environment at the root of the cloned project:

```bash
python3 -m venv .venv/
```
Activate the virtual environment:

```bash
source .venv/bin/activate
```
Install the required Python modules into the virtual environment:

```bash
python3 -m pip install -r requirements.txt
```

## Usage

### Running the Playbook
1. Run the playbook to deploy the entire stack, including Docker, Apache, PHP, MariaDB, WordPress, and SSL:

```bash
ansible-playbook -i inventory.yml playbook.yml
```

2. Check the status:

Once the playbook completes, check that your web app is live by navigating to:

https://checkpoint2.casamaggio.fr

The certificate should be valid, and the site should be served over HTTPS.

# Notes (for me :D)
- **Cloudflare**: If you're using Cloudflare as a proxy, ensure that the proxy is turned off for the domain during the SSL certificate issuance process. You can temporarily disable the Cloudflare proxy or use DNS verification.  
- **Firewall**: Ensure that the necessary ports (80 and 443) are open on the server's firewall and accessible from the internet.  
- **Custom Domain**: Ensure that your domain's DNS settings are configured correctly to point to your server’s public IP address in both your DNS provider and Cloudflare settings.  


# Pourquoi j'ai fait autrement
L'énoncé demandait de créer un sous-domaine via DynV6, de lier l'IPv6 de mon conteneur et de configurer un certificat TLS avec Let's Encrypt pour rendre mon WordPress accessible.  
Le problème, c'est que ma box ne gère pas l'IPv6, donc je ne pouvais pas suivre cette méthode telle qu'indiquée.   
Du coup, j'ai décidé de passer par une solution alternative avec AWS et Cloudflare. J'ai utilisé Cloudflare pour gérer le DNS et rediriger tout le trafic vers l'adresse IPv4 publique de ma box. Puis, j'ai sécurisé le tout avec un certificat TLS via Let's Encrypt, pour respecter les critères de sécurité. Tout ça derrière le domaine enregistré premièrement chez AWS Route53.  
J'aurais pu faire tout ça en utilisant un contrôleur Ansible sur le serveur de la Wild, mais j'ai préféré configurer tout ça localement. Ça m'a permis de contourner les soucis liés à l'IPv6 et, en même temps, de bosser sur un projet perso que je voulais développer en parallèle.  
En gros, une solution un peu plus "perso" pour gérer l'accès à l'application WordPress.
