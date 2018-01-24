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

# Aggiunta nuova AMI di partenza

1. Copiare uno dei file nella cartella _packer-files_ e rinominarlo nel formato _packer\_[nuovonome].json
2. Editare il file secondo le specifiche richieste
3. Nel cloudformation _packer.yaml_ aggiungere una risorsa di tipo _AWS::CodeBuild::Project_ copiandola da una presente e cambiare i seguenti parametri:
	* nome della risorsa
	* parametro _Name_
	* nella sezione _EnvironmentVariables_ il nome della variabile _DISTRO_ che deve coincidere con _[nuovonome]_ utilizzato nel punto 1; questa variabile infatti è utilizzata per prendersi il nome del file da packer nel buildspec.
4. Nel cloudformation _packer.yaml_ aggiungere uno stage nella risorsa _AWS::CodePipeline::Pipeline_, copiandolo da uno già presente ed cambiando nomi e altri parametri, tra cui il riferimento alla risorsa codebuild creata al punto precedente
5. Aggiungere nella policy _CodePipelineBuildAccess_ il riferimento alla risorsa Codebuild, in modo che la pipeline possa triggherare la build del nuovo codebuild project
6. Aggiornare lo stack cloudformation ```aws cloudformation update-stack --profile xpeppers --stack-name silver-ami-pipeline --template-body file://cloudformation/pipeline.yaml --parameters file://cloudformation/pipeline-parameters.json --capabilities CAPABILITY_IAM```
7. Committare e pushare le modifiche

