README
======

Progetto per la creazione di silver AMIs preconfigurate con gli utilities base tools.

Piattaforme disponibili e testate:
* centos 7
* ubuntu 16.04
* debian 8
* rhel 7.4 (rhel74)


# Cartelle
* __ansible__: cartella principale con il codice ansible da lanciare localmente sulle macchine per provisionarle
* __cloudformation__: template cloudformation per la codebuild, codepipeline ed altri tool AWS

# How to

## Creazione pipeline

1. Editare il file _pipeline-parameters.json_
2. Creare lo stack con il comando:
```aws cloudformation create-stack --profile xpeppers --stack-name silver-ami-pipeline --template-body file://cloudformation/pipeline.yaml --parameters file://cloudformation/pipeline-parameters.json --capabilities CAPABILITY_IAM```
3. Lanciare a mano la pipeline o committare sul repository.


## Creazione codebuild

1. Entrare nella cartella _cloudformation_: ```cd cluodformation```
2. Editare il file di parameters _codebuild-parameters.json_, mettendo l'url al repo codecommit contentent il progetto (questo stesso repository se non si sono fatte modifiche custom)
3. Creare lo stack con il comando
```
aws cloudformation create-stack --profile <profile_name> --stack-name codebuild-ami --template-body file://codebuild.yaml --parameters file://codebuild-parameters.json --capabilities CAPABILITY_IAM
```
4. Dopo la creazione dello stack lanciare la build a mano.

