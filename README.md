# dashboard-salon-regional-svxlink
Installation d’Apache
sudo apt update
sudo apt upgrade
sudo apt update
sudo apt install apache2
Au passage, nous allons en profiter pour donner des droits au dossier d’apache qui vous permettra de facilement administrer les sites. Pour cela, lancez les commandes suivantes :
sudo chown -R pi:www-data /var/www/html/
sudo chmod -R 770 /var/www/html/
Vérifier qu’Apache fonctionne
Il vous suffit d’ouvrir le navigateur web de la Raspberry (par défaut Midori sous Raspbian), et d’aller à l’adresse « http://127.0.0.1 ». Vous devriez alors obtenir une page avec un message du genre « It works! » et plein d’autre texte.
Si jamais vous ne possédez pas d’interface graphique sur votre Raspbian, ou que vous utilisez le SSH pour vous connecter à votre Raspberry, vous pouvez utiliser la commande suivante :
wget -O verif_apache.html http://127.0.0.1
Cette commande va enregistrer le code HTML de la page dans le fichier « verif_apache.html » dans le répertoire courant.
Vous n’avez donc plus qu’à lire le fichier avec la commande
cat ./verif_apache.html
Si vous voyez marqué à un endroit dans le code « It works! », c’est qu’Apache fonctionne.
Apache utilise le répertoire /var/www/html comme racine pour votre site. Cela signifie que quand vous appelez votre Raspberry sur le port 80 (http), Apache cherche le fichier dans /var/www/html.

Installation de PHP sur la Raspberry
On va encore une fois faire appel à l’administrateur pour installer PHP avec la ligne de commande.
sudo apt install php php-mbstring
Vérifier que PHP fonctionne
Pour savoir si PHP fonctionne correctement, ce n’est pas très compliqué, et la méthode est relativement proche de celle employée pour Apache.
Vous allez en premier lieu supprimer le fichier « index.html » dans le répertoire « /var/www/html ».
sudo rm /var/www/html/index.html
Puis créez un fichier « index.php » dans ce répertoire, avec cette ligne de commande
echo "<?php phpinfo(); ?>" > /var/www/html/index.php
À partir de là, le fonctionnement est le même que pour la vérification d’Apache. Vous tentez d’accéder à votre page, et vous devriez avoir un résultat proche de cette image (si vous n’avez pas d’interface, utilisez la même méthode que précédemment, et cherchez les mots « PHP Version »).
 
Une base de données MySQL pour votre serveur
Un SGBD c’est quoi ? Pourquoi MySQL ?
Maintenant que nous avons mis en place PHP, vous allez sans doute vouloir stocker des informations pour les utiliser dans vos sites. Pour cela, on utilise le plus souvent des bases de données.
Nous allons donc mettre en place un SGBD (Système de Gestion de Bases de Données), à savoir MySQL.
MySQL est un SGBD libre, puissant, massivement utilisé (environ 56 % de parts de marché des SGBD libres). Là encore, MySQL est tellement un incontournable du développement, quelle qu’en soit la branche, que vous devez absolument l’apprendre et le maitriser.
Installer MySQL
Pour ce faire, nous allons installer mariadb-server et php-mysql (qui servira de lien entre php et mysql)
sudo apt install mariadb-server php-mysql
 
Vérifier que MySQL marche correctement
Pour vérifier le fonctionnement de MySQL, cette fois nous utiliserons uniquement la ligne de commande. Pour cela, nous allons simplement nous connecter via la commande :
sudo mysql --user=root
Nous allons maintenant supprimer l’utilisateur root et créer un nouvel utilisateur root, car celui par défaut n’est utilisable que par le compte administrateur du système, et n’est donc pas accessible aux script PHP du serveur.
Pour cela, une fois connecté à MySQL, lancez les commandes suivantes (remplacez password par le mot de passe de votre choix) :
DROP USER 'root'@'localhost';
CREATE USER 'root'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;
Vous avez donc maintenant un serveur web, relié à PHP et à MySQL. Soit tout ce qu’il faut. Vous pouvez donc vous arrêter ici.
(Lors de vos prochaine connections, vous pourrez donc utilisez la commande mysql --user=root --password=votremotdepasse).

PHPMyAdmin, gérer facilement ses bases de données
Toutefois, vous pourriez vouloir une interface un peu plus simple pour administrer vos bases de données qu’une simple console MySQL. Pour cela, vous pouvez installer PHPMyAdmin.
PHPMyAdmin, c’est quoi ?
PHPMyAdmin est une application développée en PHP, et qui vise à fournir une interface simplifiée pour MySQL.
Elle vous permet par exemple de voir rapidement et de façon lisible le contenu de votre base de données, ou de la manipuler sans avoir besoin de faire vous-mêmes vos requêtes MySQL.
Installer PHPMyAdmin
L’installation de PHPMyAdmin n’est pas du tout obligatoire. Nous ferons ici une installation sans paramètres de sécurité particuliers.
L’installation de PHPMyAdmin se fait très simplement, via le gestionnaire de paquets, en utilisant la commande suivante :
sudo apt install phpmyadmin
PHPMyAdmin vous posera plusieurs questions concernant ses réglages.
Comme nous avons déjà configurez la base de données, choisissez no à la question concernant l’utilisation de dbconfig-common. Choisissez d’utiliser PHPMyAdmin pour un serveur Apache. Pour le mot de passe root, il s’agit de celui que vous aviez utilisé pour MySQL.
Vous devez aussi activer l’extension mysqli si cela n’est pas encore fait. Pour cela, utilisez les commandes ci-dessous.
sudo phpenmod mysqli
sudo /etc/init.d/apache2 restart
Vérifier l’installation de PHPMyAdmin
Pour vérifier le bon fonctionnement de PHPMyAdmin, vous allez simple tenter d’y accéder, en utilisant l’adresse de votre Raspberry suivi de /phpmyadmin. Par exemple, en local ce sera « http://127.0.0.1/phpmyadmin ».
Si jamais vous avez une erreur, cela peut venir du fait que PHPMyAdmin se soit installé dans un autre dossier. Dans ce cas, essayez la commande
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin

