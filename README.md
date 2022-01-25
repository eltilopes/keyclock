# keyclock

## Iniciando com Keyclock

 Instalando o Keycloak

Faça o download do keycloak:

curl https://downloads.jboss.org/keycloak/6.0.1/keycloak-6.0.1.zip --output keycloak-6.0.1.zip

Unzip 

unzip keycloak-6.0.1.zip

Adicionando um novo usuário admin

./add-user-keycloak.sh -r master -u admin -p admin

Iniciando o Keycloak

./standalone.sh 

Acessar: http://localhost:8080/auth
Criar um novo Realm
Criar uma nova Role
Criar um novo usuário e associar com a Role criada
Fazer login com o usuário criado
Modificar algumas configurações de Realm


 https://www.youtube.com/playlist?list=PL3KCMjFLdlVgnnJc4DAmoY5aRGr0p9AVL


## Configuração do bd

 Configurando Banco de Dados MySQL
Pré-requisitos
Java 1.8+
MySQL 8+ instalado
Keycloak instalado (ver vídeo 02)
Configuração
Fazer o Download do MySQL Connector 8+
Criar um novo usuário no mysql

CREATE USER 'keycloak'@'%' IDENTIFIED WITH mysql_native_password BY 'Keycloak@123';

Você precisa criar o usuário com tipo mysql_native_password se não terá um erro de “Unable to load authentication plugin 'caching_sha2_password'”

Criar um novo Database chamado keycloak

CREATE DATABASE keycloak CHARACTER SET utf8 COLLATE utf8_unicode_ci;

Crie o database com CHARACTER SET utf8 COLLATE uft8_unicode_ci ser não vai pegar o erro:  ERROR [org.keycloak.connections.jpa.updater.liquibase.conn.DefaultLiquibaseConnectionProvider] (ServerService Thread Pool -- 63) Change Set META-INF/jpa-changelog-1.9.1.xml::1.9.1::keycloak failed.  Error: (conn=16) Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs [Failed SQL: ALTER TABLE keycloak.REALM MODIFY CERTIFICATE VARCHAR(4000)]

Dar previlégios ao usuário

GRANT ALL PRIVILEGES ON keycloak.* TO 'keycloak'@'%';

Iniciar o Keycloak

$ cd bin
$ ./standalone.sh

Iniciar o jboss-cli.sh

$ sh jboss-cli.sh

Adicionar o módulo

module add --name=com.mysql --dependencies=javax.api,javax.transaction.api --resources=/opt/oracle/mysql-connector-java-8.0.17/mysql-connector-java-8.0.17.jar --module-root-dir=modules/system/layers/keycloak/

Conectar no jboss-cli

connect

Criar um novo Driver

/subsystem=datasources/jdbc-driver=com.mysql:add(driver-name=com.mysql,driver-module-name=com.mysql,xa-datasource-class=com.mysql.cj.jdbc.MysqlXADataSource)

Remover Datasource H2

/subsystem=datasources/data-source=KeycloakDS: remove()

Criar um novo Datasource

data-source add --name=KeycloakDS --jndi-name=java:jboss/datasources/KeycloakDS --enabled=true --max-pool-size=30 --password=Keycloak@123 --user-name=keycloak --driver-name=com.mysql --use-java-context=true --connection-url=jdbc:mysql://localhost:3306/keycloak?useSSL=false

Mostrar o SPI connectionsJpa

<spi name="connectionsJpa">
                <provider name="default" enabled="true">
                    <properties>
                        <property name="dataSource" value="java:jboss/datasources/KeycloakDS"/>
                        <property name="initializeEmpty" value="true"/>
                        <property name="migrationStrategy" value="update"/>
                        <property name="migrationExport" value="${jboss.home.dir}/keycloak-database-update.sql"/>
                    </properties>
                </provider>
            </spi>

https://www.youtube.com/watch?v=gsWROB9WFrs&list=PL3KCMjFLdlVgnnJc4DAmoY5aRGr0p9AVL&index=6
https://medium.com/@pratik.dandavate/setting-up-keycloak-standalone-with-mysql-database-7ebb614cc229

 

## Customizando Tela de Login - Inlcusão de THEME

Foi customizada a tela de login.
Criado um novo diretorio themes/ecommerce e definida na aba Realm/Themes

Referências:

https://www.baeldung.com/keycloak-custom-login-page
https://www.baeldung.com/spring-keycloak-custom-themes#customization-example
https://www.keycloak.org/docs/latest/server_development/index.html#_themes

## Configuração do email

