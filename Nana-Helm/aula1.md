
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso - What is Helm in Kubernetes? Helm and Helm Charts explained | Kubernetes Tutorial 23"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 1 - What is Helm in Kubernetes? Helm and Helm Charts explained | Kubernetes Tutorial 23

- Video:
https://www.youtube.com/watch?v=-ykwb1d0DXU
<https://www.youtube.com/watch?v=-ykwb1d0DXU>



- Helm Chart é um pacote de vários YAML.


- Procurando por algum Deployment?

helm search <keyword>


- Artifact Hub:
https://artifacthub.io/
<https://artifacthub.io/>





# Templating Engine

1. Define a common blueprint.
2. Dynamic values are replaced by placeholders.






- User case
same applications across different environments




# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# The Chart File Structure

https://helm.sh/docs/topics/charts/
<https://helm.sh/docs/topics/charts/>

A chart is organized as a collection of files inside of a directory. The directory name is the name of the chart (without versioning information). Thus, a chart describing WordPress would be stored in a wordpress/ directory.

Inside of this directory, Helm will expect a structure that matches this:

wordpress/
  Chart.yaml          # A YAML file containing information about the chart
  LICENSE             # OPTIONAL: A plain text file containing the license for the chart
  README.md           # OPTIONAL: A human-readable README file
  values.yaml         # The default configuration values for this chart
  values.schema.json  # OPTIONAL: A JSON Schema for imposing a structure on the values.yaml file
  charts/             # A directory containing any charts upon which this chart depends.
  crds/               # Custom Resource Definitions
  templates/          # A directory of templates that, when combined with values,
                      # will generate valid Kubernetes manifest files.
  templates/NOTES.txt # OPTIONAL: A plain text file containing short usage notes

Helm reserves use of the charts/, crds/, and templates/ directories, and of the listed file names. Other files will be left as they are.



- No topo da estrutura de diretório, o nome da pasta vai ter o nome do Chart.
- O arquivo Chart.yaml contem informações de meta info sobre o Chart. Informações como nome, dependencias, versão, etc.
- O arquivo values.yaml contem os valores de templates files.
- A pasta "charts" tem dependencias dos charts.

- Ao usar o comando "helm install", ele preenche os templates com os valores do values.yaml e gera manifestos válidos que podem ser deployados no cluster Kubernetes:
helm install <chartname>





# Value injection into template files

- O values por default, por exemplo, pode vir assim:

values.yaml

~~~~yaml
imageName: myapp
port: 8080
version: 1.0.0
~~~~


- Podemos passar para o Helm usar um values personalizado, usando o comando:
    helm install –values=custom-values.yaml <chart-name> 
overrides the default values.yaml file and takes the inputs from the custom-values.yaml file for this install.

custom-values.yaml

~~~~yaml
version: 2.0.0
~~~~



- Resultado ao usar o custom-values.yaml
Teremos o .Values object

~~~~yaml
imageName: myapp
port: 8080
version: 2.0.0
~~~~





- Podemos definir valores personalizados usando a linha de comando também.
- Usar a opção "--set" no comando helm install.
    helm install --set port=443
    helm install --set version=2.0.0










# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Release Management

Helm Version 2 vs 3


# Helm version 2
Helm Version 2 comes in two parts:
Client(Helm CLI)
Server(Tiller)

- Quando o Helm v2 recebe um helm install:
helm install <chartname>

- Ele envia a requisição ao Tiller(servidor).
- Tiller cria os recursos no Kubernetes, com base nos yaml recebidos.
- O Tiller cria uma cópia das configurações, toda vez que um Deployment é criado ou alterado.
- Cria um rastreamento de todas execuções de charts.
- As modificações são aplicadas em Deployments existentes, ao invés de criar novos.
- Ele lida com Rollbacks.

helm install <chartname>
helm upgrade <chartname>
helm rollback <chartname>





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Downsides of Helm

- Tiller tem muito poder no cluster Kubernetes.
- É um problema de segurança.
- Helm v3 removeu o Tiller. Resolve o problema de segurança, mas deixa mais dificil de usar o Helm.