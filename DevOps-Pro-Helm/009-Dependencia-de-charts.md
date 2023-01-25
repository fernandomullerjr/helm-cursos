
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 9 - Dependência de Charts"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 9 - Dependência de Charts

- No Helm é possível linkar o Chart com alguma dependencia.

- No nosso caso vamos usar um MongoDB de um repositório do Bitnami.
- Nas aulas anteriores, nós criamos os manifestos para o MongoDB, como o Deployment, Service, Secret, etc.

- Nova pasta para fazer os testes com dependencias:
    DevOps-Pro-Helm/009-Material-chart-novo/api-produto/templates


- Deletar os manifestos do Mongo que estão na pasta:
    DevOps-Pro-Helm/009-Material-chart-novo/api-produto/templates


# Chart - MongoDB - Bitnami
<https://github.com/bitnami/charts/tree/main/bitnami/mongodb/>

- Iremos usar o chart do MongoDB disponibilizado pela Bitnami:
https://github.com/bitnami/charts/tree/main/bitnami/mongodb/
<https://github.com/bitnami/charts/tree/main/bitnami/mongodb/>




1. O primeiro passo é ir no arquivo "Chart.yaml":
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/009-Material-chart-novo/api-produto/Chart.yaml

2. Adicionar a definição de dependencia.

- Exemplo:

~~~~YAML
#For example, this Chart.yaml declares two dependencies:
# Chart.yaml
dependencies:
- name: nginx
  version: "1.2.3"
  repository: "https://example.com/charts"
- name: memcached
  version: "3.2.1"
  repository: "https://another.example.com/charts"
~~~~

