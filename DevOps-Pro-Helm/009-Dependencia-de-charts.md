
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


- Ajustado, dependencia do MongoDB:

a versão que iremos setar, podemos pegar no arquivo do chart no repositório:
https://github.com/bitnami/charts/blob/main/bitnami/mongodb/Chart.yaml
no caso era 13.6.4
e o endereço do repositório do chart, pegamos no README do repo do Github:
https://charts.bitnami.com/bitnami

~~~~YAML
dependencies:
- name: mongodb
  version: "13.6.4"
  repository: "https://charts.bitnami.com/bitnami"
~~~~



- Para setar os valores, necessário ajustar o arquivo values.yaml para os valores desejados, seguindo a estrutura mostrada no manual do repositório no GitHub:
https://github.com/bitnami/charts/tree/main/bitnami/mongodb/
<https://github.com/bitnami/charts/tree/main/bitnami/mongodb/>

vamos pegar e definir os valores de senha, usuario, banco de dados, seguindo a estrutura do manual, removendo o credentials e usando o auth, na identação necessária
auth.rootPassword 	MongoDB(®) root password 	""
auth.usernames 	List of custom users to be created during the initialization 	[]
auth.passwords 	List of passwords for the custom users set at auth.usernames 	[]

Quanto a persistencia, vamos deixar em false por enquanto, pois o default dela é true
Persistence parameters
Name 	Description 	Value
persistence.enabled 	Enable MongoDB(®) data persistence using PVC 	true


DE:

~~~~YAML
api:
  image: fabricioveronez/pedelogo-catalogo:v1
  serviceType: ClusterIP
  ingress:
  - aulakubedev.com.br
  - api.aulakubedev.com.br

mongodb:
  tag: 4.2.8
  credentials:
    userName: mongouser
    userPassword: mongopwd
  databaseName: admin
  #existSecret: nome do secret
~~~~


PARA:

~~~~YAML
api:
  image: fabricioveronez/pedelogo-catalogo:v1
  serviceType: ClusterIP
  ingress:
  - aulakubedev.com.br
  - api.aulakubedev.com.br

mongodb:
  auth:
    usernames: mongouser
    passwords: mongopwd
    rootPassword: mongoRoot
    databases: admin
  persistence:
    enabled: false
~~~~



- Ajustado o values.



- Necessário ajustar agora os Templates.


- Como não teremos mais o Secret do MongoDB, devemos remover o trecho de variáveis no manifesto do API-Deployment:

        env:
          - name: Mongo__User
            valueFrom:
              secretKeyRef:
                key: MONGO_INITDB_ROOT_USERNAME
                {{- if empty .Values.mongodb.existSecret }}
                name: {{ .Release.Name }}-mongodb-secret
                {{- else }}
                name: {{ .Values.mongodb.existSecret }}
                {{- end }}
          - name: Mongo__Password
            valueFrom:
              secretKeyRef:
                key: MONGO_INITDB_ROOT_PASSWORD
                {{- if empty .Values.mongodb.existSecret }}
                name: {{ .Release.Name }}-mongodb-secret
                {{- else }}
                name: {{ .Values.mongodb.existSecret }}
                {{- end }}


- O nome do MongoDB Service é diferente neste chart, devemos ajustar o arquivo  _helpers.tpl , colocando o valor correto para o template:

DevOps-Pro-Helm/009-Material-chart-novo/api-produto/templates/_helpers.tpl

~~~~YAML
{{- define "mongodb.serviceName" -}}
{{ .Release.Name }}-mongo-service
{{- end -}}
~~~~

PARA:

~~~~YAML
{{/* Nome do Service do MongoDB */}}
{{- define "mongodb.serviceName" -}}
{{ .Release.Name }}-mongo
{{- end -}}
~~~~




- VIDEO CONTINUA EM
06:51