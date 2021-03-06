# AWS EC2 ambiente Linux para WordPress e Laravel

### Instancia AWS EC2 para rodar laravel >= 6 e WordPress

Atualizar os pacotes do Ibuntu

```
sudo apt-get update
sudo apt-get upgrade
```

## Instalação do apache

```
sudo apt install apache2
```

> Abilitar a reinscrita de URL

```
a2enmod rewrite
```

> Abilitar MOD SSL

```
a2enmod ssl
```

> Reiniciar o apache

```
sudo /etc/init.d/apache2 restart
```

## Instalação do PHP 7.3

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt install php7.3
```

> Extenções do PHP 

```
sudo apt install php7.3-cli php7.3-fpm php7.3-json php7.3-pdo php7.3-mysql php7.3-zip php7.3-gd  php7.3-mbstring php7.3-curl php7.3-xml php7.3-bcmath php7.3-json php7.3-intl
```

## Instalação do MySql

```
sudo apt install mysql-server
```

> Criar novo banco de dados

```
sudo mysql

CREATE DATABASE mynewdatabase;
SHOW DATABASES;
```

> Criar usuário para o banco de dados

```
CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
```

> Remover usuário de banco de dados

```
DROP USER 'username'@'host';
```

> Permissões do usuário

*Permissões direcionadas para um banco*

```
GRANT ALL PRIVILEGES ON MYDBNAME.* TO 'USERNAME'@'1.2.3.4' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION;

FLUSH PRIVILEGES;

EXIT;
```

*Permissões direcionadas para todos bancos*

```
GRANT ALL PRIVILEGES ON *.* TO 'USERNAME'@'%' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION; *permissões direcionadas para todos bancos*

FLUSH PRIVILEGES;

EXIT;
```

> Senha para o usuário root

```
sudo mysql

SELECT user,authentication_string,plugin,host FROM mysql.user;

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mypassword';

FLUSH PRIVILEGES;

EXIT;
```

> Agora para acessar o usuario root 

```
mysql -u root -p;
```

> Verificar status do MySql

```
sudo service mysql status
```

> Acesso remoto ao MySql

```
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

bind-address = 0.0.0.0
```

> Reiniciar o MySql

```
sudo /etc/init.d/mysql restart
```

## Instalação do CURL

```
sudo apt-get update
sudo apt-get install --upgrade libc-bin libc-dev-bin libc6 libc6-dev klibc-utils libklibc
sudo reboot
```

## Alterar a propriedade do seu diretório remoto para o seu usuário.

Altere a propriedade do seu diretório remoto para o seu usuário. Por exemplo, vou mudar a propriedade de /remote-dir para meu usuário “ubuntu” em vez de “root“.
Usar quando o executar o scp e der *permission denied*

```
sudo chown -R ubuntu:ubuntu "caminho para o seu diretório remoto"
```

> Executar dentro do projeto *Laravel*, para mudar grupo de usuário

```
sudo chown -R ubuntu:www-data storage
sudo chown -R ubuntu:www-data bootstrap
```

> Permissoes das pastas bootstrap e storage

```
sudo chmod -R o+w storage/
sudo chmod -R 775 storage/

sudo chmod -R o+w bootstrap/
sudo chmod -R 775 bootstrap/
```

## Atualizar arquivos no servidor via SCP

```
scp -i C:/dir/to/certificate.pem -r C:/dir/to/app/* ubuntu@host-ip.com:/var/www/html/app
```

## Instalar certificado SSL Letsencrypt

> Baixe e instale o Certbot

```
sudo apt-get install certbot python-certbot-apache
```

> Instale o certificado LetsEncrypt e atualize os arquivos de configuração

```
sudo certbot
```

> Para renovar o certificado que tem duração de 3 meses

```
sudo certbot renew
```

## Criar virtual Host usando apache2

> Apontando domínio ou subdomínio

```
cd /etc/apache2/sites-available

sudo nano 000-default.conf

<VirtualHost *:80>
        ServerName www.subdominio.dominio.com.br
        ServerAlias subdominio.dominio.com.br
        DocumentRoot /var/www/dir
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        <Directory /var/www/dir>
            Options Indexes FollowSymLinks MultiViews
            AllowOverride All
            Require all granted
        </Directory>
</VirtualHost>
```

> Criar arquivo .conf

```
cd /etc/apache2/sites-available

sudo touch site.com.br.conf
```

> Abilitar ou desabilitar arquivo .conf

```
sudo a2ensite site.com.br.conf --> Abilita
sudo a2dissite site.com.br.conf --> Desabilita
```

> Apontando subdominio wildcard

```
<VirtualHost *:80>
        ServerAlias *.dominio.com.br
        DocumentRoot /var/www/dir
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        <Directory /var/www/dir>
            Options Indexes FollowSymLinks MultiViews
            AllowOverride All
            Require all granted
        </Directory>
</VirtualHost>
```

## Criar subdominio Wildcard

> Entre na zona para editar o DNS no seu servidor e crie uma entrada do tipo A ***.dominio.com.br** apontando para o IP de destino

## Instalar o certificado SSL manualmente, pode ser usando para subdominio **Wildcard**

```
sudo certbot certonly --manual -d *.crm.cosmefar.com.br --agree-tos --no-bootstrap --manual-public-ip-logging-ok --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory

// Crie a entrada TXT

Host: _acme-challenge.dominio.com.br
Conteudo: sera retornado quando o comando for executado
```

> Criar o arquivo .conf com as configurações

```
<IfModule mod_ssl.c>
        <VirtualHost *:443>
                ServerAlias *.crm.dominio.com.br
                DocumentRoot /var/www/dir
                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined
                <Directory /var/www/dir>
                    Options Indexes FollowSymLinks MultiViews
                    AllowOverride All
                    Require all granted
                </Directory>

                SSLEngine on

                SSLCertificateFile /etc/letsencrypt/live/crm.dominio.com.br/fullchain.pem
                SSLCertificateKeyFile /etc/letsencrypt/live/crm.dominio.com.br/privkey.pem

        </VirtualHost>
</IfModule>
```
