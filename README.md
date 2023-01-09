**Documentation for becoming a OIDC / 0Auth2.0 provider**

***Before Going further lets look into some of the important Terminologies***


*whats social login ?*
>   Social login is a single sign-on (SSO) technology that allows users to authenticate themselves on various applications and sites by connecting through a site which implements Oauth or OIDC **(in our case Intelops implements hydra which is a Oauth , OIDC provider)**  rather than typing a separate ID and password on each website.

*What is Authentication?*

> authentication means, **the user or computer has to prove its identity to the server or client**. Usually, authentication by a server entails the use of a user name and password. Other ways to authenticate can be through cards, retina scans, voice recognition, and fingerprints

*What is Authorization?*

>   Authorization is  **the process of giving someone permission to have access to something**.


**Some Terminologies around 0auth**


 - **Authorization server**: The server that issues access tokens to clients after successfully authenticating the resource owner and obtaining authorization.
 - **Resource owner**: The entity that can grant access to a protected resource. This is typically the end-user.
 - **Client**: An application that requests access to a protected resource on behalf of the resource owner.
 - **Resource server**: The server hosting the protected resources and capable of accepting and responding to protected resource requests using access tokens.
 - **Access token**: A token that is issued by the authorization server and used by the client to access the protected resources on the resource server.
 - **Refresh token**: A token that is issued by the authorization server and used by the client to obtain a new access token when the original access token becomes invalid or expires.
 - **Scope**: A list of permissions that the client can request to access protected resources on the resource server.
 - **Grant types**: The OAuth 2.0 specification defines several grant types, which indicate how the client can obtain an access token. Examples include authorization code, implicit, resource owner password credentials, and client credentials.
 - **Authorization code**: A grant type in which the client obtains an authorization code from the authorization server and exchanges it for an access token. This grant type is commonly used in server-side web applications.
 - **Implicit**: A grant type in which the authorization server issues an access token directly to the client. This grant type is commonly used in client-side web applications and mobile apps.
 - **Resource owner password credentials**: A grant type in which the client obtains an access token by presenting the resource owner's username and password to the authorization server.
 - **Client credentials**: A grant type in which the client obtains an access token by presenting its own credentials (client ID and secret) to the authorization server.



**Technologies Used**

 - **Kratos** : Kratos is a Authentication tool build by the Ory. we can leverage it for implementing authentication.
 - **Hydra** : _Ory Hydra_ is the most advanced OAuth 2.0 and OIDC Certified® Server. we can leverage it for becoming a  OAuth 2.0 and OIDC provider.
 - **Postgres** : PostgreSQL, also known as Postgres, is a free and open-source relational database management system emphasizing extensibility and SQL compliance.
 
**A quick start guide to run kratos container** :

    version: '3.9'

    services:
    
    kratos-migrate:
    
    image: oryd/kratos:v0.10.1
    
    depends_on:
    
    - postgresd
    
    environment:
    
    - DSN=postgres://core:core@postgresd:5432/kratos?sslmode=disable&max_conns=20&max_idle_conns=4
    
    volumes:
    
    - type: volume
    
    source: kratos-sqlite
    
    target: /var/lib/sqlite
    
    read_only: false
    
    - type: bind
    
    source: ./config
    
    target: /etc/config/kratos
    
    command: -c /etc/config/kratos/kratos.yml migrate sql -e --yes
    
    restart: on-failure
    
    networks:
    
    - intranet
    kratos:
    
    depends_on:
    
    - kratos-migrate
    
    image: oryd/kratos:v0.10.1
    
    ports:
    
    - '4433:4433'  # public
    
    - '4434:4434'  # admin
    
    restart: unless-stopped
    
    environment:
    
    - DSN=postgres://core:core@postgresd:5432/kratos?sslmode=disable&max_conns=20&max_idle_conns=4
    
    - LOG_LEVEL=trace
    
    command: serve -c /etc/config/kratos/kratos.yml --dev --watch-courier
    
    volumes:
    
    - type: volume
    
    source: kratos-sqlite
    
    target: /var/lib/sqlite
    
    read_only: false
    
    - type: bind
    
    source: ./config
    
    target: /etc/config/kratos
    
    networks:
    
    - intranet

    networks:
    
    intranet:
    
    volumes:
    
    kratos-sqlite:

 - In the above docker compose we are running two services named kratos-migrate and kratos

 - kratos migrate is a service used for the migration of the scemas 
 - Kratos is a authentication service.

***In the above dockercompose file we have a kratosmigrate service in that we serve kratos.yml***

***Below I will provide a sample kratos.yml***

    version: v0.10.1 
    dsn: postgres://auth:secret@auth-db:5432/auth?sslmode=disable&max_conns=20&max_idle_conns=4 
    serve:
    public:
    
    base_url: http://localhost:4433/
    
    cors:
    
    enabled: true
    
    admin:
    
    base_url: http://localhost:4434/
    selfservice:
    
    default_browser_return_url: http://localhost:4455/
    
    allowed_return_urls:
    
    - http://localhost:4455 
    methods:
    
    password:
    
    enabled: true
    flows: 
    error:
    
    ui_url: http://localhost:4455/error 
    settings:
    
    ui_url: http://localhost:4455/settings
    
    privileged_session_max_age: 15m
    recovery:
    
    enabled: true
    
    ui_url: http://localhost:4455/recovery  
    logout:
    
    after:
    
    default_browser_return_url: http://localhost:4455/logout
    login:
    
    lifespan: 10m
    
    ui_url: http://localhost:4455/login
    registration:
    
    lifespan: 10m
    
    ui_url: http://localhost:4455/registration
    
    after:
    
    default_browser_return_url: http://localhost:4455/registered
    
    log:
    
    level: debug
    
    format: text
    
    leak_sensitive_values: true
    secrets:
    
    cookie:
    
    - PLEASE-CHANGE-ME-I-AM-VERY-INSECURE
    
    cipher:
    
    - 32-LONG-SECRET-NOT-SECURE-AT-ALL
    ciphers:
    
    algorithm: xchacha20-poly1305
    hashers:
    
    argon2:
    
    parallelism: 1
    
    memory: 128MB
    
    iterations: 2
    
    salt_length: 16
    
    key_length: 16
    identity:
    
    schemas:
    
    - id: default
    
    url: file:///etc/kratos/identity-schemas/service/json/person.schema.json
    
    default_schema_id: "default"
    
      
    
    courier:
    
    smtp:
    
    connection_uri: smtps://test:test@mailslurper:1025/?skip_ssl_verify=true




**A quick start guide to run Postgres using docker**

    postgresd:
    image: postgres:latest
    ports:
    - "5432:5432"   
    volumes:
    - ./pg-init-scripts:/docker-entrypoint-initdb.d
    environment:
    - POSTGRES_USER=${DB_USER}
    - POSTGRES_PASSWORD=${DB_PASSWORD}
    - POSTGRES_MULTIPLE_DATABASES=kratos,accesscontroldb,vizual,auth
    networks:
    - intranet
The official postgres dont have a provision to create multiple database during starting the Postgres instance using docker compose.

     set -e
    
    set -u
    
      
    
    function  create_user_and_database() {
    
    local database=$1
    
    echo  " Creating user and database '$database'"
    
    psql -v ON_ERROR_STOP=1 --username "$POSTGRES_USER"  <<-EOSQL
    
    CREATE USER $database;
    
    CREATE DATABASE $database;
    
    GRANT ALL PRIVILEGES ON DATABASE $database TO $database;
    
    EOSQL
    
    } 
    if [ -n "$POSTGRES_MULTIPLE_DATABASES" ]; then
    
    echo  "Multiple database creation requested: $POSTGRES_MULTIPLE_DATABASES"
    
    for  db  in  $(echo $POSTGRES_MULTIPLE_DATABASES  | tr ','  '  '); do
    
    create_user_and_database $db
    
    done
    
    echo  "Multiple databases created"
    
    fi


we will serve this script into postgres instance which will give us the ability to create multiple database while starting the postgres instance.

    volumes:
    - ./pg-init-scripts:/docker-entrypoint-initdb.d
we can create a .sh file containg the above script and place it inside the **pg-init-scripts**  directory


**A quickstart Guide to start the hydra using docker**

    hydra-migrate:
    
    depends_on:
    
    - postgresd
    
    - kratos-migrate
    
    container_name: hydra-migrate
    
    image: oryd/hydra:v1.11.8
    
    environment:
    
    - DSN=postgres://core:core@postgresd:5432/auth?sslmode=disable&max_conns=20&max_idle_conns=4
    
    - SECRETS_SYSTEM=youReallyNeedToChangeThis
    
    restart: on-failure
    
    command: migrate sql -e --yes
    
    networks:
    
    - intranet
    hydra:
    
    container_name: hydra
    
    image: oryd/hydra:v1.11.8
    
    depends_on:
    
    - postgresd
    
    - hydra-migrate
    
    ports:
    
    - 4444:4444  # Public port
    
    - 4445:4445  # Admin port

    - 5555:5555  # Port for hydra token user
    
    command:
    
    serve -c /etc/hydra/config/hydra.yml all --dangerous-force-http
    
    restart: on-failure  # TODO figure out why we need this (incorporate health check into hydra migrate command?)
    
    networks:
    
    - intranet
    
    volumes:
    
    - type: bind
    
    source: ./config
    
    target: /etc/hydra/config
