# Recuperacion-de-Rusben-
## Instal·lació d'apache2, mysql i algunes llibreries al contenidor

1. Actualització de la màquina.
```console
sudo apt update
```
```console
sudo apt upgrade
```

2. Instal·lació del servidor web `apache2`.
```console
sudo apt install -y apache2
```

3. Instal·lació del servidor de bases de dades `mysql-server`.
```console
sudo apt install -y mysql-server
```

4. Instal·lació d'algunes llibreries de `php`, el llenguatge principal que utilitzen les aplicacions.
```console
sudo apt install -y php libapache2-mod-php
```
```console
sudo apt install -y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
```

5. Reiniciem el servidor apache2
```console
sudo systemctl restart apache2
```

## Configuració de MySQL
### Accedim a la consola de MySQL
Des d'un terminal on siguem `root` hem d'executar la següent comanda:
```console
sudo mysql
```

### Creació de la base de dades:
Un cop dins la consola de MySQL executem les comandes per a crear la base de dades. En aquest cas estem creant una base de dades amb el nom `bbdd`.

```console
CREATE DATABASE bbdd;
```

### Creació d'un usuari
Tingueu en compte que s'haurà d'identificar la IP des de la qual s'accedirà a la base de dades, en aquest cas, `localhost`.

```console
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

### Donem privilegis a l'usuari:
```console
GRANT ALL ON bbdd.* to 'usuario'@'localhost';
```

### Sortim de la base de dades
```console
exit
```

### Probem la connexió a la base de dades
Des d'un terminal amb un usuari sense privilegis hem de ser capaços de connectar introduïnt la nostra contrassenya.

```console
mysql -u usuario -p
```

## Extra: permetre la connexió des d'una màquina remota
Per seguretat, MySQL no permet per defecte connexions que no siguin des de localhost. Si volem canviar aquest comportament hem de crear un altre usuari que accedirà des d'una màquina remota i estarà identificat pel nom d'usuari i la seva IP. Així doncs, poden existir diferents usuaris anomenats `usuario` que connecten des de diferents màquines.

### Canviem l'accés per defecte a la nostra màquina
Permetem l'accés des de qualsevol equip a la nostra base de dades. Editem l'arxiu `/etc/mysql/mysql.conf.d/mysqld.cnf`

```console
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

Busquem la línia següent:
```console
bind-address = 127.0.0.1
```

Hem de canviar el `bind-address` per `0.0.0.0` i la línia ha de quedar així:
```console
bind-address = 0.0.0.0
```

### Reiniciem el servidor
```console
systemctl restart mysql
```

### Creació d'un usuari per a accedir des d'una màquina remota
Per accedir des d'una màquina remota, hauriem de crear un usuari nou identificat pel nom d'usuari i la IP de la màquina des de la qual accedirà.

```console
CREATE USER 'usuario'@'192.168.22.100' IDENTIFIED WITH mysql_native_password BY 'password';
```

Hem de donar privilegis a l'usuari que accedirà des de la màquina remota.
Per accedir des de fora, hauriem de donar-li també privilegis a l'usuari a l'altra màquina:

```console
GRANT ALL ON bbdd.* to 'usuario'@'192.168.22.100';
```

```console
exit
```

## Descarreguem els fitxers de l'aplicació web
Anem al directori `/var/www/html` i descomprimim allà els fitxers de l'aplicació web, heu de substituir `app-web.zip` per el nom del vostre fitxer que heu descarregat amb l'aplicació web i el nom de la carpeta `app-web` per la carpeta que us ha creat, si la vostra instal·lació de linux està en un idioma diferent al català, no tindreu la carpeta `Baixades`, modifiqueu la comanda per adaptarla a les vostrs necessitats.

```console
sudo cp ~/Baixades/app-web.zip /var/www/html
```
Aneu al directori `/var/www/html`
```console
cd /var/www/html
```
Descomprimiu el fitxer que heu baixat
```console
sudo unzip app-web.zip
```
Copieu els fitxers a la carpeta `/var/www/html`, modifiqueu `app-web` pel nom del directori on s'ha descomprimit el vostre arxiu.
```console
sudo cp -R app-web/. /var/www/html
```
Eliminem la carpeta creada quan hem fet l'`unzip`
```console
sudo rm -rf app-web/
```

## Eliminem el fitxer `index.html` de l'`apache2`
```console
sudo rm -rf /var/www/html/index.html
```

## Aplicació de permisos a les nostres aplicacions web
Un cop descomprimits els fitxers de l'aplicació web al directori `/var/www/html`, apliquem els següents permisos al directori `/var/www/html`

```console
cd /var/www/html
```
```console
sudo chmod -R 775 .
```
```console
sudo chown -R usuario:www-data .
```
## Accedim al navegador per veure que tot funciona
Poseu la direcció http://localhost al navegador web i configureu la cloud.

Si tot ha anat bé i heu seguit el manual us apareixerà l'instal·lador de l'aplicació web que heu baixat i us demanarà crear un usuario admin i la informació de la base de dades.

La informació que heu de posar (si no heu modificat la informació del manual) és la següent:

* **usuari:** usuario
* **contrasenya:** password
* **base de dades:** bbdd
* **domini:** localhost

Deixaré totes les imatges capturades durant cada pas per ajudar a entendre millor tot el procés. 
![Captura desde 2025-05-06 13-33-01](https://github.com/user-attachments/assets/2539f4dd-293e-4c95-b798-081394e99246)
![Captura desde 2025-05-06 13-33-10](https://github.com/user-attachments/assets/82eff7d5-16e6-4fd8-8209-024d93406d7e)
![Captura desde 2025-05-06 13-34-39](https://github.com/user-attachments/assets/a1bf2b13-e5e0-4122-a785-de8f3471b5b8)
![Captura desde 2025-05-06 13-35-29](https://github.com/user-attachments/assets/30a10cb9-4381-4560-97a8-4409e1e67c6a)
![Captura desde 2025-05-06 13-36-00](https://github.com/user-attachments/assets/dc19cb67-3d43-460d-ba17-fb14bc74991b)
![Captura desde 2025-05-06 13-36-55](https://github.com/user-attachments/assets/1d194e28-77de-4633-9e52-24e0620a248f)
![Captura desde 2025-05-06 13-37-22](https://github.com/user-attachments/assets/efaf2061-1fe1-47c2-8a13-87650d27d2fe)
![Captura desde 2025-05-06 13-37-58](https://github.com/user-attachments/assets/b494d9a9-1c70-4976-9281-ddfced46d4f8)
![Captura desde 2025-05-06 13-38-12](https://github.com/user-attachments/assets/d7443267-89ab-4e7d-bdca-f6d6eb8af6c7)
![Captura desde 2025-05-06 13-38-46](https://github.com/user-attachments/assets/faf4bd04-1a85-4a04-89a0-0388a38f9a6b)
![Captura desde 2025-05-06 13-39-29](https://github.com/user-attachments/assets/a49e831a-c488-41ab-8dfd-b4382c4f7c34)
![Captura desde 2025-05-06 13-52-39](https://github.com/user-attachments/assets/e6c9317a-cdd1-4ffb-98b1-f2a3a41d0f39)
![Captura desde 2025-05-06 13-56-16](https://github.com/user-attachments/assets/84227edd-cb28-48ac-819b-354d4718ec00)
![Captura desde 2025-05-06 13-56-34](https://github.com/user-attachments/assets/59758119-f43a-4126-ab3e-2083656d36cd)
![Captura desde 2025-05-06 13-57-05](https://github.com/user-attachments/assets/f7e51cef-8fe0-4f1c-ae5f-29f08164632c)
![Captura desde 2025-05-06 14-02-47](https://github.com/user-attachments/assets/f912a883-f4e0-47b5-b999-133ebc2e274d)
![Captura desde 2025-05-06 14-06-09](https://github.com/user-attachments/assets/a749e2a4-394e-4f12-b948-d93b3ffe40ad)
