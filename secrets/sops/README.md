# SOPS
Ce projet fournit un exemple d'utilisation de [SOPS](https://github.com/getsops/sops) pour chiffrer ses secrets et les injecter dans sa chart helm.

Cloud PI utilise l'algorithme [AGE](https://age-encryption.org/) pour chiffrer/déchiffrer les données sensibles et la CRD [SopsSecret](https://github.com/isindir/sops-secrets-operator)

## Installation

### SOPS

L'installation de SOPS se fait en suivant les instructions du site : [https://github.com/getsops/sops](https://github.com/getsops/sops). Sous Windows, l'installation peut se faire via l'utilitaire chocolatey : 

```bash
choco install sops
```

### AGE

Le format de clé utilisé sur CPiN est age [https://github.com/FiloSottile/age](https://github.com/FiloSottile/age)

Il est nécessaire d'installer cet utilitaire pour manipuler les clés.

### Vérification

Afin de vérifier que SOPS et AGE ont bien été installé lancer un terminal et vérifier les commandes suivantes 

```bash
c:\> sops -v
sops 3.8.1 (latest)

c:\> age-keygen
# created: 2024-06-21T09:54:52+02:00
# public key: age187tmnp3ydzv6wvdl47tyysnakmvpmXXXXXXXXX
AGE-SECRET-KEY-10AR2NPHAYRN4NE4H2C5JUK0AM9NWW0XXXXXXXXXX  
```
La commande ci-dessus génère une paire de clé d'exemple qui n'est utilisée qu'à titre d'exemple.

### Exemples

Se placer dans le répertoire examples du repo.

Créer une paire de clés :
```bash
c:\dev\exemples_ServiceTeam\secrets\sops\templates> age-keygen -o example-key.txt
```

Récupérer la clé public de la commande ci-dessus (soit dans la sortie standard, soit dans le fichier example-key.txt à l'emplacement de l'exécution de la commande)

La clé publique débute par age, par exemple age187tmnp3ydzv6wvdl47tyysnakmvpmXXXXXXXXX.

Exporter cette clé via la commande suivante :

```bash
$ AGE_KEY=age1c5jym99kxg6qcgkadnjhj4s5lu5sunr58n8uf5j38fcxvlch9eussxy79f
$ echo $AGE_KEY
age1c5jym99kxg6qcgkadnjhj4s5lu5sunr58n8uf5j38fcxvlch9eussxy79f
```

Chiffrer le fichier d'exemple via la commande suivante:

```bash
c:\dev\exemples_ServiceTeam\secrets\sops\templates>sops -e --age $AGE_KEY --encrypted-suffix Templates secret-file-test.yaml > secret-file-test.enc.yaml
```

Cette commande chiffre les entrées YAML  du fichier secret-file-test.yaml ayant le suffixe Templates et écrit le résultat dans le fichier secret-file-test.enc.yaml. A noter qu'il est nécessaire de conserver l'extension du fichier (d'où l'extension .enc.yaml).

Le fichier chiffré doit ressembler à :

```yaml
apiVersion: isindir.github.com/v1alpha3
kind: SopsSecret
metadata:
    name: mysecret-sops
spec:
    secretTemplates:
        - name: ENC[AES256_GCM,data:jKvBNbnzzaqWX89vHAE=,iv:okRWxG4nPvMbcU94/I1boHScw6r4P0egl3C/HB8pjZQ=,tag:nIAOqeHIw0o54KMAXo2h6g==,type:str]
          labels:
            label1: ENC[AES256_GCM,data:I52cHxFmaCrIWKWMBQ==,iv:eItI5ulvabsx46HGe0G7NaUnXdULUK1IgW4qvmLUE7U=,tag:gV6dX3jrqlp+/PieoZVCkg==,type:str]
          annotations:
            key1: ENC[AES256_GCM,data:RF3Xvk8o4E52Gw==,iv:3pYYUAA5FUjYKGPgGuoUlhv/woGFe0bEBd6ktT/tREA=,tag:6Xfw+fUrSotl7bK7lwjZeg==,type:str]
          stringData:
            data-name0: ENC[AES256_GCM,data:36XMn7E0zwwb4AY=,iv:qj2c6nW7RxDSrC3E35VIvv89GV3crcdB3JQwivj4oPc=,tag:35/7rEZZMQwsyXuniRiEjQ==,type:str]
          data:
            data-name1: ENC[AES256_GCM,data:E89mimETlurhH6IqP4rPGA==,iv:3L6oZEbfCgb1mKLpfCqhBwixlWGqjWyFMLV9wahyKgM=,tag:eB9x9SQM/3BhEJeK0FWHHg==,type:str]
        - name: ENC[AES256_GCM,data:2kFaFOY9aUtkGORbFw==,iv:FU0djNFYTT1MyDQ70w3hQxaAZGrLVojCd9cnNukF4xc=,tag:vnL8cHNGMATaOdj4p8q+8Q==,type:str]
          stringData:
            token: ENC[AES256_GCM,data:NQWj1h9OEVuDjRydon00Rg==,iv:JAgsq7MHc9/sTIPcy525BhXl/Srk9VRyjT6tXOuaxGo=,tag:ACHCD61XKdoWX/zgDPouEg==,type:str]
        - name: ENC[AES256_GCM,data:SYtF1XlyqFOB/8v6,iv:l87LQBZWCG0mzTPr1WpG1cpFC9LU3i2U9/b3/0Y3op0=,tag:GxovUKk12prUuEc3tqnDGw==,type:str]
          type: ENC[AES256_GCM,data:lxDTSxygsufiTrgQcMvYN1A+eOCw+ikb6E0RV10Y,iv:JRIcXr79HCcaLZiXKYe4HzBC/PMwmUlDRa6flzmJ90g=,tag:NfXBQxbFJbLcNPVXq1vi6w==,type:str]
          stringData:
            .dockerconfigjson: ENC[AES256_GCM,data:c9zJoM0gNFEGxGAi4884K72WY8p80VwhHzpT+ZP9l8ssq62xc3dPq5c/KQx603ctAlZpDJuPKnB3uIj2zqkFMBj9Ov1MRWsEONBE6AeNdnG/QUch0kSX4b7A/lY3HXyYMIT2/PD+YyItQQYjS+Un2Jy87tI=,iv:GdfAHu/6oXdysT9ezs7n31RTIwuS3RwCiOYgqv70uSY=,tag:uIbRuNsnuN9Fox6JqnwIKA==,type:str]
sops:
    kms: []
    gcp_kms: []
    azure_kv: []
    hc_vault: []
    age:
        - recipient: age1c5jym99kxg6qcgkadnjhj4s5lu5sunr58n8uf5j38fcxvlch9eussxy79f
          enc: |
            -----BEGIN AGE ENCRYPTED FILE-----
            YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSBIK1lqUExJamd1cmdVYjdi
            SkpHYzNiOFVnZG8zTEsyY25mMThQK3liTURRCmd6U2h2SzE0ZFRvL2FiZ09PT09E
            MlhSeVZJbkhZUjh5cHNVdTBJTnZwVFUKLS0tIHZLajBJQmRlOUlLZG14NTZvazVq
            ZFdGbjVPb3N3MklUdnRZMGJJZVZoVUEKhtqGZ62vLBVyWfTnqVldnvppGEy37yUU
            wBgMIivoRRCXeHVKdFru3ski3V2Q3M/y/yAIR0n3ybvbbVt1dhcjng==
            -----END AGE ENCRYPTED FILE-----
    lastmodified: "2024-06-21T08:06:36Z"
    mac: ENC[AES256_GCM,data:ops8RJZ7aKFLk8qvCcKxHYCw6TMlgb1Rdpv4qy6SoJM0Rz/TXAu9o33pXsIzWTAiFPc0Z3eBh0KpSFxWVbuJvz5uFIp0i/+aoVbNl4U8M6DFeYfnRY1U9lw/jR9tkBIVZk6OJUDCfavidG6qZmTZv5PBENfVAdlLy8EyBoqEIsY=,iv:uA6mxvukRFp66OzmkiuHb4UbcfGmQGLqq3A8dC1p9x8=,tag:DOxPrhoRHb5nUqqtZWclMA==,type:str]
    pgp: []
    encrypted_suffix: Templates
    version: 3.8.1
```

Pour déchiffrer le fichier, il est nécessaire de préciser la clé privée soit :
Dans la variable d'environnement **SOPS_AGE_KEY** soit en plaçant le fichier keys.txt dans son répertoire $HOME/sops/age/keys.txt  (%AppData%\sops\age\keys.txt sous windows) puis en exécutant la commande :

```bash
sops -d secrets/sops/examples/secret-file-test.enc.yaml
```

Enfin il est possible de chiffrer avec plusieurs clé (par exemple une clé pour le cluster de destination et une clé pour l'équipe de dev.) comme suit :

```bash
 AGE_KEY1=age1c5jym99kxg6qcgkadnjhj4s5lu5sunr58n8uf5j38fcxXXXXXXXXX
 AGE_KEY2=age1z49g23u55m0jj8ptwv8xxavvtvpp8cjs4v76sl69jvfegtXXXXXXX
sops -e --age $AGE_KEY1,$AGE_KEY2 --encrypted-suffix Templates secret-file-test.yaml
```


## Exemple d'intégration dans un projet
Exemple d'arborescence permettant de gérer des secrets différents par plateforme/environnement.

```
.
├── Chart.yaml
├── README.md
├── charts
├── conf                         : dossier contenant les secrets en clairs (non versionnés)/chiffrés. Dossier non lu par helm
│   ├── decrypt.sh               : script pour déchiffrer un fichier chiffré
│   ├── encrypt.sh               : script pour chiffrer un/des fichiers en clairs
│   ├── mi                       : dossier représentant la plateforme mi
│   │   ├── preprod              : sous-dossier représentant l'environnement preprod
│   │   │   ├── secrets.yml.dec  : fichier secret en clair
│   │   │   └── secrets.yml.enc  : fichier chiffré
│   │   └── prod                 : sous-dossier représentant l'environnement prod
│   │       ├── secrets.yml.dec
│   │       └── secrets.yml.enc
│   └── ovh                      : dossier représentant la plateforme ovh
│       ├── dev                  : sous-dossier représentant l'environnement dev
│       │   ├── secrets.yml.dec
│       │   └── secrets.yml.enc
│       └── qualif               : sous-dossier représentant l'environnement qualif
│           ├── secrets.yml.dec
│           └── secrets.yml.enc
├── templates                    : dossier contenant les différents fichiers yaml que helm va utiliser
│   ├── deployment.yml           : exemple d'un déploiement utilisant les secrets créés via SopsSecret
│   └── secrets.yml              : fichier permettant d'injecter les bons secrets selon la plateforme et l'environnement    
├── values-mi-preprod.yaml       : surchage du values pour la plateforme mi et l'environnement preprod
├── values-mi-prod.yaml          : surchage du values pour la plateforme mi et l'environnement prod
├── values-ovh-dev.yaml          : surchage du values pour la plateforme ovh et l'environnement dev
├── values-ovh-qualif.yaml       : surchage du values pour la plateforme ovh et l'environnement qualif
└── values.yaml                  : valeur par défaut pour le chart helm
```

## SopsSecret
Pour pouvoir utiliser SOPS avec kubernetes, il faut définir un `SopsSecret`.

Ce `SopsSecret` sera responsable de créer les différents `Secret` (il est possible de définir plusieurs `Secret` par `SopsSecret`)

Exemple d'un fichier en clair, qui sera par la suite chiffré:
```yaml
apiVersion: isindir.github.com/v1alpha3
kind: SopsSecret
metadata:
  # Nom du SopsSecret, n'est pas réellement utile
  name: example-sopssecret-mi-preprod
spec:
  # Tableau contenant les différents secrets
  secretTemplates:
      # Nom du secret qui sera utilisé plus tard dans l'application
    - name: my-secret-name-1
       
      # Labels du secret
      labels:
        label1: value1

      # Annotations du secret
      annotations:
        key1: value1

      # Données du secret sous forme clé/valeur
      # la différence entre stringData et data, est que les valeurs dans le dictionnaire data doivent être encodées en base64
      stringData:
        data-name0: data-value0
      data:
        data-name1: ZGF0YS12YWx1ZTE=
    - name: jenkins-secret
      labels:
        "jenkins.io/credentials-type": "usernamePassword"
      annotations:
        "jenkins.io/credentials-description": "credentials from Kubernetes"
      stringData:
        username: myUsername
        password: 'Pa$$word'
    - name: some-token
      stringData:
        token: Wb4ziZdELkdUf6m6KtNd7iRjjQRvSeJno5meH4NAGHFmpqJyEsekZ2WjX232s4Gj
    - name: docker-login
      type: 'kubernetes.io/dockerconfigjson'
      stringData:
        .dockerconfigjson: '{"auths":{"index.docker.io":{"username":"imyuser","password":"mypass","email":"myuser@abc.com","auth":"aW15dXNlcjpteXBhc3M="}}}'
```

## Scripts
### Chiffrement
Ce projet propose un script permettant de chiffrer via l'utilitaire SOPS (à installer séparément) et l'algorithme AGE différents fichiers YAML.

Il contient déjà les différentes clés publiques utilisées sur Cloud PI.

Le script parcourt le dossier courant et sous-dossiers pour trouver des fichiers avec l'extension .dec

Les fichiers chiffrés sont sauvegardés avec l'extension .enc au lieu de .dec

Il est possible de passer un argument pour changer le dossier courant, voir de donner un chemin vers un fichier
```
.
├── encrypt.sh
├── mi
│   ├── preprod
│   │   ├── secrets.yml.dec
│   │   └── secrets.yml.enc
│   └── prod
│       ├── secrets.yml.dec
│       └── secrets.yml.enc
└── ovh
    ├── dev
    │   ├── secrets.yml.dec
    │   └── secrets.yml.enc
    └── qualif
        ├── secrets.yml.dec
        └── secrets.yml.enc
```

Ci-dessous un tableau reprenant les fichiers concernés selon l'argument fourni au script:
| Argument                | Fichiers concernés                                                                                   |
|-------------------------|------------------------------------------------------------------------------------------------------|
| <aucun>                 | mi/preprod/secrets.yml.dec, mi/prod/secrets.yml.dec, ovh/dev/secrets.yml, ovh/qualif/secrets.yml.dec |
| ovh                     | ovh/dev/secrets.yml, ovh/qualif/secrets.yml.dec                                                      |
| ovh/dev/secrets.yml.dec | ovh/dev/secrets.yml                                                                                  |


## Divers
### Clé publique
AGE est un algorithme à clé publique/clé privée.

Pour avoir les clés publiques, s'adresser à la ServiceTeam


### Git
Ne surtout pas oublier de ne pas versionner les fichiers en clair dans le dépôt git.

Ajouter cette ligne au `.gitignore`:
```
*.dec
```

