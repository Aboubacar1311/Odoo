# Manuel d'installation d'Odoo-14 sur ubuntu 20.04
Odoo est une suite d'applications d'entreprise open source populaire. Il offre une gamme d'applications, notamment CRM, commerce électronique, création de sites Web, facturation, comptabilité, fabrication, entrepôt, gestion de projet, inventaire et bien plus encore, le tout parfaitement intégré.

Odoo peut être installé de plusieurs manières selon le cas d'utilisation et les technologies disponibles. Le moyen le plus simple et le plus rapide d'installer Odoo consiste à utiliser les référentiels officiels Odoo APT.
## Installation des prérequis

Connectez-vous à votre Ubuntu en tant qu'utilisateur sudo et mettez à jour le cache Apt :

    sudo apt update

Installation de Git , Pip , Node.js , et des tools requise pour odoo et les dependence

    sudo apt update
    sudo apt install git python3-pip build-essential wget python3-dev python3-venv \
        python3-wheel libfreetype6-dev libxml2-dev libzip-dev libldap2-dev libsasl2-dev \
        python3-setuptools node-less libjpeg-dev zlib1g-dev libpq-dev \
        libxslt1-dev libldap2-dev libtiff5-dev libjpeg8-dev libopenjp2-7-dev \
        liblcms2-dev libwebp-dev libharfbuzz-dev libfribidi-dev libxcb1-dev

## Creation des utilisateur du system
Créez un utilisateur système qui exécutera Odoo, nommé odoo13 avec le répertoire personnel /opt/odoo13:
    
    sudo useradd -m -d /opt/odoo14 -U -r -s /bin/bash odoo14

Vous pouvez définir le nom de l'utilisateur comme vous le souhaitez, tant que vous créez un utilisateur PostgreSQL avec le même nom.
Installation et configuration de PostgreSQL

Odoo utilise PostgreSQL comme back-end de la base de données. Pour installer PostgreSQL, exécutez la commande suivante:
    
    sudo apt install postgresql
Une fois l'installation terminée, créez un utilisateur PostgreSQL avec le même nom que l'utilisateur système précédemment créé, dans notre cas c'est odoo13
   
    sudo su - postgres -c "createuser -s odoo14"
Installation de Wkhtmltopdf


Le package wkhtmltox fournit un ensemble d'outils de ligne de commande open source qui peuvent rendre HTML en PDF et divers formats d'image. Pour pouvoir imprimer des rapports PDF, vous devez installer l'outil wkhtmltopdf. La version recommandée pour Odoo est 0.12.5, qui n'est pas disponible dans les référentiels Ubuntu 18.04 par défaut.

Téléchargez le package à l'aide de la commande wget suivante

    sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
    sudo apt install ./wkhtmltox_0.12.5-1.bionic_amd64.deb
## Installer et configurer Odoo 13

Comme mentionné précédemment, nous installerons Odoo à partir de la source dans un environnement virtuel Python isolé.

Tout d'abord, passez à l'utilisateur "odoo13"

    sudo su - odoo14
    git clone https://www.github.com/odoo/odoo --depth 1 --branch 14.0 /opt/odoo14/odoo
    cd /opt/odoo14
    python3 -m venv odoo-venv

ACTIVERl'environnement virtuel

    source odoo-venv/bin/activate

Installez tous les modules Python requis avec pip3:

    pip3 install wheel
    pip3 install -r odoo/requirements.txt
    deactivate

    mkdir /opt/odoo14/odoo-custom-addons
    exit
## creation des fichier de conf

    sudo nano /etc/odoo14.conf
## saisissez


    [options]
    ; This is the password that allows database operations:
    admin_passwd = my_admin_passwd
    db_host = False
    db_port = False
    db_user = odoo14
    db_password = False
    addons_path = /opt/odoo14/odoo/addons,/opt/odoo14/odoo-custom-addons

## Création d'un fichier d'unité Systemd

    sudo nano /etc/systemd/system/odoo14.service
## saisissez

    [Unit]
    Description=Odoo14
    Requires=postgresql.service
    After=network.target postgresql.service
    
    [Service]
    Type=simple
    SyslogIdentifier=odoo14
    PermissionsStartOnly=true
    User=odoo14
    Group=odoo14
    ExecStart=/opt/odoo14/odoo-venv/bin/python3 /opt/odoo14/odoo/odoo-bin -c /etc/odoo14.conf
    StandardOutput=journal+console
    
    [Install]
    WantedBy=multi-user.target


## redemerage

    sudo systemctl daemon-reload

    sudo systemctl enable --now odoo14

Verifier le status du service 

    sudo systemctl status odoo14
output

    ● odoo14.service - Odoo14
     Loaded: loaded (/etc/systemd/system/odoo14.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2020-10-16 19:05:32 UTC; 3s ago

Ouvrez votre navigateur et tapez : http://<votre_domaine_ou_adresse_IP>:8069

En supposant que l'installation soit réussie, un écran similaire au suivant apparaître
Installer Odoo 14 sur Ubuntu
![img.png](img.png)




