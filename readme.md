

# Instalación de issuer-coordinator(emisor de credenciales)


Esta pequeña guía nos va ayudar a instalar lo necesario para correr el emisor con las configuraciones en los archivos de entornos para ellos utilizaremos perfiles y ejecutar los comandos pertinentes. Siga el paso a paso detenidamente y luego verifique que la app este corriendo en un entorno local o desarrollo. 

### **docker compose en local**

1. Verifique que los nombre de los servicios tengan el prefijo local ejemplo:
    - coordinator-local
    
2. Verificar tres servicios configurados con sus respectivos archivos o variables de entorno:
    1. coordinator-local
        - .coordinator.local.env

    2. signer-local
        - .signing-service.local.env

    3. status-local
        - .status-service.local.env

3. Ejecutar el comando **docker compose --profile local up** donde **local** es el nombre del **perfil** creado en el archivo de docker compose.

4. Ahora solo abra el docker desktop y verifique que se creo una red de contenedores llamado **issuer-coordinator** 

    ![Texto alternativo](/assets/img/local.png "titulo alternativo")

    ![Texto alternativo](/assets/img/local-docker.png "titulo alternativo")

5. Por ultimo hagamos una solicitud a este Curl en postman y veamos que nos devuelva un 200 Ok.

~~~
curl --location 'http://localhost:4005/instance/tec23/credentials/issue' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 988DKLAJH93KDSFV' \
--data-raw '{
    "@context": [
        "https://www.w3.org/2018/credentials/v1",
        "https://purl.imsglobal.org/spec/ob/v3p0/context-3.0.2.json"
    ],
    "id": "urn:uuid:60738770-c60a-4486-a7a6-289ba8cd033a",
    "type": [
        "VerifiableCredential",
        "OpenBadgeCredential"
    ],
    "issuer": {
        "id": "the issuer code will set this as the issuing DID",
        "type": "Profile",
        "name": "DCC Test Issuer",
        "description": "A test DID used to issue test credentials",
        "url": "https://digitalcredentials.mit.edu",
        "image": {
            "id": "https://certificates.cs50.io/static/success.jpg",
            "type": "Image"
        }
    },
    "issuanceDate": "2020-01-01T00:00:00Z",
    "expirationDate": "2025-01-01T00:00:00Z",
    "name": "Successful Installation",
    "credentialSubject": {
        "type": "AchievementSubject",
        "name": "Me!",
        "achievement": {
            "id": "http://digitalcredentials.mit.edu",
            "type": "Achievement",
            "criteria": {
                "narrative": "Successfully installed the DCC issuer."
            },
            "description": "DCC congratulates you on your successful installation of the DCC Issuer.",
            "name": "Successful Installation",
            "image": {
                "id": "https://certificates.cs50.io/static/success.jpg",
                "type": "Image"
            }
        }
    }
}'
~~~
    

### **docker compose en desarrollo - azure container instance**

1. Verifique que los nombre de los servicios tengan el prefijo dev ejemplo:
    - coordinator-dev
    
2. Verificar tres servicios configurados con sus respectivos archivos o variables de entorno:
    1. coordinator-dev
        - .coordinator.dev.env

    2. signer-dev
        - .signing-service.dev.env

    3. status-dev
        - .status-service.dev.env

3. Para poder publicar en azure container instance primero debemos de asegurarnos de tener una cuenta en azure y una suscripción activa.
    - https://learn.microsoft.com/es-es/azure/cost-management-billing/manage/create-subscription

4. Crear un grupo de recurso
    - https://learn.microsoft.com/es-es/azure/azure-resource-manager/templates/deploy-portal

5. Autenticate con tu cuenta de azure
    - **docker login azure** 

6. Crear un contexto
    - Creamos un nuevo contexto, seleccionamos suscripción y grupo de recursos generados anteriormente para anclarlo al contexto de azure **docker context create aci contextoACI**
    - Seleccionamos el contexto de azure **docker use contextoACI**

7. Ejecutar el comando **docker compose --profile develop up** donde **develop** es el nombre del **perfil** creado en el archivo de docker compose y espere a que se empuje las red de contenedores a azure container instance.

8. Entre al panel de azure y analize el grupo de recursos en ella veremos creado el nombre de la red de contenedor con el nombre **issuer-coordinator** 

    ![Texto alternativo](/assets/img/dev.png "titulo alternativo")

    ![Texto alternativo](/assets/img/dev-docker.png "titulo alternativo")

9. Por ultimo hagamos una solicitud a este Curl en postman y veamos que nos devuelva un 200 Ok.

    ~~~
    curl --location '20.246.172.82:4005/instance/test/credentials/issue' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer 988DKLAJH93KDSFV' \
    --data-raw '{
        "@context": [
            "https://www.w3.org/2018/credentials/v1",
            "https://purl.imsglobal.org/spec/ob/v3p0/context-3.0.2.json"
        ],
        "id": "urn:uuid:60738770-c60a-4486-a7a6-289ba8cd033a",
        "type": [
            "VerifiableCredential",
            "OpenBadgeCredential"
        ],
        "issuer": {
            "id": "the issuer code will set this as the issuing DID",
            "type": "Profile",
            "name": "DCC Test Issuer",
            "description": "A test DID used to issue test credentials",
            "url": "https://digitalcredentials.mit.edu",
            "image": {
                "id": "https://certificates.cs50.io/static/success.jpg",
                "type": "Image"
            }
        },
        "issuanceDate": "2020-01-01T00:00:00Z",
        "expirationDate": "2025-01-01T00:00:00Z",
        "name": "Successful Installation",
        "credentialSubject": {
            "type": "AchievementSubject",
            "name": "Me!",
            "achievement": {
                "id": "http://digitalcredentials.mit.edu",
                "type": "Achievement",
                "criteria": {
                    "narrative": "Successfully installed the DCC issuer."
                },
                "description": "DCC congratulates you on your successful installation of the DCC Issuer.",
                "name": "Successful Installation",
                "image": {
                    "id": "https://certificates.cs50.io/static/success.jpg",
                    "type": "Image"
                }
            }
        }
    }'
    ~~~

    ![Texto alternativo](/assets/img/dev-postman.png "titulo alternativo")
