# Zabbix-Installation
Voici les étapes générales pour installer un serveur Zabbix sur une machine Debian :

Étape 1 : Prérequis
Assurez-vous que votre machine Debian dispose d'une connexion Internet active et que vous avez les privilèges administratifs (ou exécutez les commandes avec `sudo`).

Étape 2 : Mise à jour du système
Commencez par mettre à jour votre système en exécutant les commandes suivantes :

```
sudo apt update
sudo apt upgrade
```

Étape 3 : Installation des dépendances
Installez les dépendances nécessaires à l'exécution de Zabbix en exécutant la commande suivante :

```
sudo apt install apache2 mysql-server mysql-client php php-mysql php-gd php-ldap php-xml php-mbstring php-bcmath
```

Lors de l'installation de MySQL, vous serez invité à définir un mot de passe pour l'utilisateur root de MySQL. Assurez-vous de le noter, car vous en aurez besoin plus tard.

Étape 4 : Configuration de la base de données MySQL
Connectez-vous à votre serveur MySQL en utilisant la commande suivante :

```
sudo mysql -u root -p
```

Une fois connecté à MySQL, créez une nouvelle base de données pour Zabbix en utilisant la commande suivante (remplacez `<nom_de_la_base_de_données>` par le nom de votre choix) :

```
CREATE DATABASE <nom_de_la_base_de_données> CHARACTER SET utf8 COLLATE utf8_bin;
```

Créez également un nouvel utilisateur MySQL pour Zabbix en utilisant la commande suivante (remplacez `<nom_utilisateur>` et `<mot_de_passe>` par les informations de votre choix) :

```
CREATE USER '<nom_utilisateur>'@'localhost' IDENTIFIED BY '<mot_de_passe>';
GRANT ALL PRIVILEGES ON <nom_de_la_base_de_données>.* TO '<nom_utilisateur>'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Étape 5 : Téléchargement et installation de Zabbix
Téléchargez la dernière version de Zabbix à partir du site officiel (https://www.zabbix.com/download) en utilisant la commande `wget`. Par exemple :

```
wget https://cdn.zabbix.com/zabbix/sources/stable/5.4/zabbix-5.4.3.tar.gz
```

Décompressez l'archive téléchargée en utilisant la commande suivante :

```
tar xvfz zabbix-5.4.3.tar.gz
```

Accédez au répertoire extrait :

```
cd zabbix-5.4.3
```

Compilez et installez Zabbix en utilisant les commandes suivantes :

```
./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-libxml2
make
sudo make install
```

Étape 6 : Configuration de Zabbix Server
Copiez le fichier de configuration de l'exemple fourni :

```
sudo cp misc/conf/zabbix_server.conf /etc/zabbix/
```

Éditez le fichier de configuration :

```
sudo nano /etc/zabbix/zabbix_server.conf
```

Modifiez les paramètres de configuration selon vos besoins, notamment :

```
DBName=<nom_de_la_base_de_données>
DBUser=<nom_utilisateur>
DBPassword=<mot_de_passe>
```

Sauvegardez et fer

mez le fichier.

Étape 7 : Configuration de Zabbix Frontend
Configurez le frontend Apache en créant un lien symbolique vers le répertoire de Zabbix :

```
sudo ln -s /usr/share/zabbix /var/www/html/zabbix
```

Éditez le fichier de configuration Apache :

```
sudo nano /etc/apache2/conf-available/zabbix.conf
```

Ajoutez les lignes suivantes :

```
Alias /zabbix /var/www/html/zabbix
<Directory /var/www/html/zabbix/>
  Require all granted
  AllowOverride All
  Options FollowSymLinks
</Directory>
```

Sauvegardez et fermez le fichier.

Activez la configuration Apache :

```
sudo a2enconf zabbix
sudo systemctl restart apache2
```

Étape 8 : Démarrage du serveur Zabbix
Démarrez le serveur Zabbix :

```
sudo zabbix_server
```

Vérifiez que le serveur démarre correctement et qu'il n'y a pas d'erreurs.

Étape 9 : Configuration de Zabbix Agent
Installez également l'agent Zabbix sur les machines que vous souhaitez surveiller en exécutant les commandes suivantes :

```
sudo apt install zabbix-agent
```

Éditez le fichier de configuration de l'agent :

```
sudo nano /etc/zabbix/zabbix_agentd.conf
```

Modifiez les paramètres de configuration selon vos besoins.

Redémarrez l'agent Zabbix :

```
sudo systemctl restart zabbix-agent
```

Étape 10 : Configuration via l'interface web
Ouvrez votre navigateur et accédez à l'adresse `http://<votre_adresse_IP_ou_nom_de_domaine>/zabbix`. Vous devriez voir l'interface de configuration de Zabbix.

Suivez les étapes pour configurer Zabbix, notamment en spécifiant les informations de connexion à la base de données.

Une fois la configuration terminée, vous pourrez vous connecter à l'interface de Zabbix avec les identifiants par défaut (admin/zabbix) et commencer à configurer vos hôtes et surveiller votre infrastructure.

Ceci conclut la procédure d'installation de Zabbix Server sur une machine Debian. Assurez-vous de consulter la documentation officielle de Zabbix pour des informations plus détaillées et des options de configuration supplémentaires.
