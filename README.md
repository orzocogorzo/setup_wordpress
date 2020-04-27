## INSTAL·LANT WORDPRESS

### 0. VINCULAR SERVIDOR I DOMINI

   Des del proveidor del domini buscar algo com **afegir registre DNS**. El proveidor de dominis tindrá accés a un servidor on s'allotja una taula amb linies on es recull un domini, una ip i quin tipus de conexió hi ha entre els dos. El que hem fer es introduir en aquesta taula un registre que vinculi el nostre domini i la IP del nostre servidor amb un tipus de conexió de tipus **"A"**. Un cop generat aquest vincle hem de comprobar que estigui funcionant, anem al navegador i posem la direcció del nostre domini i comprobem si ens dirigeix de forma correcta. És possible que el domini no redireccioni a la nostra màquina fins al cap d'una estona, no desesperar, és normal. Tornem a provar al cap d'una hora i repetim el mateix procediment.

### 1. CONEXIÓ AMB EL SERVIDOR

Ens conectem al nostre servidor amb un client* **ssh**. En ordinadors linux tindrem ssh-agent instal·lat per defecte a la nostra màquina i l'executarem desde la consola amb la comanda `ssh user@ipdelanostramaquina`. A Windows haurem de descarregar-nos un client tipus PUTTY i seguir les instruccions per conectar-nos. Per a les conexions ssh entre màquines sempre ens adreçarem a la màquina on ens volem conectar amb el format `user@ipdelamaquina`.

### 2. INSTAL·LEM APACHE

Un cop conectats al servidor instal·larem l'Apache. Apache és un servidor web al que deixarem escoltant les possibles peticions que arribin desde la xarxa i li donarem unes instruccions bàsiques d'enrutament per a que pugui fer arrivar la informació de les peticions rebudes a allà on tinguem el codi de la nostra aplicació, en el nostre cas el codi de wordpress.

Per instal·lar apache en un sistema operatiu Linux Debian seguir les següents [instruccions](https://tecadmin.net/install-apache-debian10/).

### 3. INSTAL·LEM MYSQL

Wordpress es conecta a una base de dades on enmagatzema tota la informació necessària per a pintar la web (entrades, arxius, comentaris, usuaris, temas, etc.). Una base de dades es un sistema d'estructuració de dades en format taula, algo així com un excel sense la interficie gràfica. Wordpress en concret funciona amb les bases de dades *MySQL* o *MariaDB*, dues sistemes de bases de dades diferents però basats en la mateixa tecnologia. Podem escollir qualsevol de les dues ja que el seu funcionament són molt similars. Per al nostre cas treballem amb MySQL. 

Per a instal·lar MySQL en un sistema operatiu Linux Debian seguir les següents [instruccions](https://tecadmin.net/install-apache-debian10/).

### 4. INSTAL·LEM PHP

Wordpress és una aplicació de servidor que funciona amb llenguatge php. Per a poder fer funcionar l'aplicació necessitarem l'interpet de php instal·lat al nostre sistema. A part de tenir-lo instal·lat no haurem de preocupar-nos més per això a no ser que ens volguem dedicar professionalment a ser desenvolupadors de temes fancies de botiques onlines per a wordpress.

Per instal·lar PHP a un sistema operatiu Linux Debian seguir les següents [instruccions](https://tecadmin.net/install-php-debian-9-stretch/).

### 5. DESCARREGAR WORDPRESS

Un cop complim amb els requisits per a fer funcionar wordpress (el servidor, la base de dades i l'interpret de PHP) només ens queda descarregar el codi i configurar la nostra aplicació.
   
El codi el podem trobar a la [pàgina de descarregues](https://wordpress.org/latest.tar.gz) de la pàgina oficial de wordpress. Des d'allà ens descarreguem l'última versió del codi i la allotgem al nostre servidor. Ens tornem a conectar al servidor amb la comanda `ssh user@ipdelamaquina` i la contrasenya, un cop dins utilitzem el client web __wget*__ amb la comanda `wget https://wordpress.org/latest.tar.gz` que ens descarregarà un arxiu zip anomenat __latest.tar.gz__ al directori on ens trobem. El pas següent serà descomprimir-lo amb la comanda `tar -xzvf latest.tar.gz` i que ens generarà una nova carpeta anomenada _wordpress_ amb tot el codi necessari al seu interior. Deixem aquesta carpeta a algun lloc on recordem i passem al pas següent.

### 6. CREEM LA CARPETA DE LA NOSTRA WEB

Al instal·lar apache aquest haurà creat una carpeta dins del nostre ordinador on espera trobar les diferents aplicacions que volguem servir a través d'ell. Aquesta carpeta es troba a la ruta _/var/www_. Naveguem fins a ella amb la comanda `cd /var/www` i des d'allà creem una nova carpeta que allotjarà la nostra aplicació de wordpres amb la comanda `mkdir exemple.com`. Un cop generada aquesta carpeta hem de moure el contingut de la carpeta _wordpress_ que ens hem descarregat abans al seu interior amb la comanda `cp -r /ruta/a/la/carpeta/wordpress/* /var/www/exemple.com/`. Finalment tenim el codi de wordpress localitzat a una carpeta visible per apache i des d'on podrem fer que es comuniquin wordpress i apache.

### 7. CONFIGUREM WORDPRESS

Entre els arxius que trobem dins la carpeta de la nosta aplicació `/var/www/exemple.com` hi trovem un anomenat _wp-config-sample.php_. Harurem de renombrar aquest arxiu amb el nom _wp-config.php_ que serà l'arxiu amb les especificacions que wordpress anirà a buscar quan intenti conectar-se a la nostra base de dades.

Per modificar la configuració recollida a l'arxiu seguim les [instruccions següents](https://www.wpbeginner.com/glossary/wp-config-php/).

En resum, el que haurem de fer es notificar amb quines credencials wordpress s'haurà d'autentificar contra la base de dades, i definir-li també uns codis que wordpress utilitzarà després per xifrar les comunicacions. Per a això haurem de modificar els camps següents i introduir-hi les nostres dades:

   ```php
   define('DB_NAME', 'database_name_here');
   define('DB_USER', 'username_here');
   define('DB_PASSWORD', 'password_here');

   define('AUTH_KEY', 'put your unique phrase here');
   define('SECURE_AUTH_KEY', 'put your unique phrase here');
   define('LOGGED_IN_KEY', 'put your unique phrase here');
   define('NONCE_KEY', 'put your unique phrase here');
   define('AUTH_SALT', 'put your unique phrase here');
   define('SECURE_AUTH_SALT', 'put your unique phrase here');
   define('LOGGED_IN_SALT', 'put your unique phrase here');
   define('NONCE_SALT', 'put your unique phrase here');
   ```

Assigneu amb llibertat el nom de la base de dades, l'usuari i la contrasenya ja que els haurem de crear després a la nostra voluntat. Per a omplir els camps de les claus secretes haurem de copair el contingut que obtinguem obrint [aquest enllaç](https://api.wordpress.org/secret-key/1.1/salt/) desde el nostre navegador.

### 8. CREACIÓ DE LA BASE DE DADES

On cop configurat wordpress hem de crear la base de dades que l'hi hem dit. Fins ara tenim instal·lat MySQL, el software de base de dades que utilitzarem. Ara haurem de dir-li a MySQL que crei una nova base de dades per a ser explotada per wordpress.

Els passos a seguir són els següents:

   1. Ens conectem amb el client _mysql_ a la base de dades per defecte amb la següent comanda: `mysql -u root -p` i introduint-hi la contrasenya de l'usuari root que ens demanarà.
   2. Sense haver sortit de la consola veurem que s'han donat alguns canvis com que el _prompter_ ara adopta la forma de `mysql>`. Això ens indica que hem obert el programa de base de dades i que les comandes que utilitzem aquí han de parlar el llenguatge de la base de dades ja que no ens estem comunicant amb la consola de l'ordinador sinó amb la base de dades.
   3. Creem la nova base de dades amb `CREATE DATABASE nomdelabasededades;`.
   4. Creem l'usuari de wordpress amb `CREATE USER usuariwordpress IDENTIFIED BY 'contrasenyadelusuari';`.
   5. Donem accés a la base de dades al nou usuari amb `GRANT ALL PRIVILEGES ON nomdelabasededades.* TO 'usuariwordpress'@'localhost';`
   6. Si hem completat tots els passos sense cap error tindrem la base de dades i l'usuari preparats per a ser utilitzats desde worpress per a establir la conexió necessària amb la base de dades.

Recordeu que és important que tant la base de dades com l'usuari i la contrasenya que generem en aquest pas han de conicidir amb els indicats a l'arxiu de configuració _wp-config.php_.

### 9. CONFIGURAR APACHE

Arribats aquí només ens queda configurar Apache i indicar-li on ha de trobar l'aplicació wordpress al nostre ordinador i com i quan ha de comunicar-se amb ella. Per a això haurem de navegar fins a la carpeta `/etc/apache2/`. Un cop allà hi trobem dues carpetes interessants: **sites-availabe** i **sites-enabled**. La primera allotja múltiples arxius de configuració que anirem copiant a la carpeta sites-enabled on hi depositarem només aquells corresponents a les aplicacions que tinguem actives dins el nostre servidor (és possible que en algun moment hi tinguem més d'una aplicació de _wordpress_ allotjada a la mateixa pàgina, o un _wordpress_ i un _nextcloud_, i cada aplicació tindrà les seves instruccions d'apache on se li indicara com comunicar les peticions web que rebi i la nostra aplicació).

   Un exemple d'arxiu de configuració és el següent:

   ```xml
   <VirtualHost *:80>
     ServerAdmin admin@mail.com
     DocumentRoot /var/www/exemple.com
     ServerName exemple.com
     ServerAlias www.exemple.com
     <Directory /var/www/casabuenosaires.org>
       Allow override all
       Options FollowSymLink
       Require all granted
    </Directory>
    ErrorLog /var/log/exemple.com-error.log
    CustomLog /var/log/exemple.com-access.log combined
   </VirtualHost>
   ```
    
Treballant sobre aquest exemple haurem de substituir tota aquella informació en la que apareix la info genèrica exemple.com per les dades del nostre domini i la nostre carpeta on allotgem l'aplicació de wordpress. La informació la guardem dins d'un arxiu de text dins la carpeta `/etc/apache2/sites-available` amb el nom de la nostra web `exemple.com.conf` i posteriorment el copeim desde la carpeta `sites-available` a la carpeta `sites-enabled`. Un cop copiat l'arxiu vigilem que dins la carpeta sites-enabled no hi hagi cap altre arxiu que el nostre ja que apache t'hi diposita un arxiu per defecte que farà que en comptes de la nostra aplicació wordpres es carregui per defecte una pàgina de presentació d'apache.

### 10. REINICIAR EL SISTEMA
Un cop tenim tot el sistema configurat només ens queda reiniciar les aplicacions:
1. `systemctl reload apache2` per a carregar la nova configuració a apache.
2. `systemctl restart mysql` per a reiniciar la base de dades.
3. Si la cosa no funciona provar de reiniciar la màquina amb `reboot` i tornar-nos-hi a conectar. 

### 11. XIFRAR LES COMUNICACIONS AMB HTTPS (Opcional)

En cas que volguem xifrar les comunicacions entre el nostre servidor i els clients web per a que la informació no pugui ser interceptada haurem de configurar apache per que utilitzi unes claus secretes amb les que xifrarà la informació abans de enviar-la cap a internet.

 Letsencrypt i Certbot ens permeten fer això de forma gratuita i gairebé automàtica. Anem a la [certbot.eff.org](https://certbot.eff.org/lets-encrypt/) i un cop indicat quin és el sistema operatiu de la nostra màqiona (_Debian_, _Ubuntu_, _CentOS_, _Windows_...) i quina tecnologia utilitzem per al servidor (_Apache_, _Ngix_, _Plesk_...) ens donarà unes instruccions de com instal·lar i configurar el nostre servidor per a que treballi amb els certificats.

### 12. GAUDIR DE LA WEB
Naveguem al nostre domini i si tot a anat bé trobarem el nostre wordpress. Per a acabar de configurar-lo haurem d'accedir a l'adreça `http://elnostredomini.ext/wp-admin/install.php` per a acabar la configuració de _wordpress_ i un cop finalitzat només ens queda gaudir amb el resultat. Recordeu que per accedir a l'administrador de wordpress haurem de navegar a l'adreça `http://elnostredomini.ext/wp-admin` des d'on ens demanarà un usuari  i una contrasenya amb les que identificar-nos per a donar-nos accés a totes les eines de gestió de contingut d'aquesta eina.

## RECURSORS

1. [Instal·lar Apache](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-debian-9)
2. [Instal·lar MySQL](https://linuxize.com/post/how-to-install-mysql-on-debian-10/)
3. [Instal·lar PHP](https://tecadmin.net/install-php-debian-9-stretch/)
4. [Descarregar Wordpress](https://wordpress.org/download/)
5. [Guia d'instal·lació de Wordpress](https://wordpress.org/support/article/how-to-install-wordpress/)
6. [Guia d'instal·lació mes guai de Wordpress](https://linux4one.com/how-to-install-wordpress-with-lamp-stack-on-debian-10/)
7. [Treballant amb wp-config.php](https://wordpress.org/support/article/editing-wp-config-php/)
8. [Codis secrets de wp-config.php](https://api.wordpress.org/secret-key/1.1/salt/)
9. [Certificats HTTPS amb Letsencrypt](https://api.wordpress.org/secret-key/1.1/salt/)

## NOTES
1. Client: En el mon de la informàtica s'anomena client a aquell programa que et permet als usuaris comunicar-se amb un altre màquina/software. Les aplicacions web s'anomenen clients ja que et permeten comunicar-te de forma gràfica amb un servidor, fer peticions i esperar respostes. Al igual que una aplicació web un client ssh és un programa que et permet comunicar-te amb un altre ordinador comunican-te a través del protocol de transferència de dades ssh amb el que podrem accedir de forma remota a la consola d'un altre ordinador.
2. WGET: Al igual que al navegador web li diem client perquè ens permet consumir els recursos d'un servidor a demanda, wget també és un client: Se l'ha d'entendre com un navegador web no gràfic i executable desde la consola.

