
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 5 - Primeiro Helm Chart"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 5 - Primeiro Helm Chart


api-produto


helm create api-produto



# PENDENTE
- Ver retorno do Fabricio sobre manifestos do k8s usados no video da aula.





# Dia 10/01/2023

- Obtidos os manifestos com o Veronez.
localizados na pasta abaixo no Windows:
D:\OneDrive\Documents\Kubernetes_k8s\KubeDev__DevOps-PRO\Helm\aula-helm
ou no Linux:
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart




- Criar o template para o Helm chart:
cd /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart
helm create api-produto

~~~~bash
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart$ helm create api-produto
Creating api-produto
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart$ ls -lhasp
total 28K
4.0K drwxr-xr-x 3 fernando fernando 4.0K Jan 10 21:58 ./
4.0K drwxr-xr-x 4 fernando fernando 4.0K Jan 10 21:52 ../
4.0K -rw-r--r-- 1 fernando fernando  651 Jan 10 21:53 api-deployment.yaml
4.0K drwxr-xr-x 4 fernando fernando 4.0K Jan 10 21:58 api-produto/
4.0K -rw-r--r-- 1 fernando fernando  151 Jan 10 21:53 api-service.yaml
4.0K -rw-r--r-- 1 fernando fernando  507 Jan 10 21:53 mongodb-deployment.yaml
4.0K -rw-r--r-- 1 fernando fernando  142 Jan 10 21:53 mongodb-service.yaml
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart$

fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ ls -lhasp
total 28K
4.0K drwxr-xr-x 4 fernando fernando 4.0K Jan 10 21:58 ./
4.0K drwxr-xr-x 3 fernando fernando 4.0K Jan 10 21:58 ../
4.0K drwxr-xr-x 2 fernando fernando 4.0K Jan 10 21:58 charts/
4.0K -rw-r--r-- 1 fernando fernando 1.2K Jan 10 21:58 Chart.yaml
4.0K -rw-r--r-- 1 fernando fernando  349 Jan 10 21:58 .helmignore
4.0K drwxr-xr-x 3 fernando fernando 4.0K Jan 10 21:58 templates/
4.0K -rw-r--r-- 1 fernando fernando 1.9K Jan 10 21:58 values.yaml
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$

~~~~







- Limpar o arquivo values.yaml
- Helm ignore, manter como está.
DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/.helmignore

- Na pasta "templates":
deletar a pasta "tests"
deletar o "DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/_helpers.tpl"
deletar os arquivos:
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/deployment.yaml
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/hpa.yaml
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/ingress.yaml
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/service.yaml
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/serviceaccount.yaml

- Vamos aproveitar somente a estrutura de pasta. Restante vai ser criado tudo do zero.


- No arquivo "/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/NOTES.txt":
apagar o conteúdo e colocar a palavra "Instalado", ele guarda informações sobre a release:
Instalado



- Copiar os manifestos originais.
- Colar eles na pasta "templates":
DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates







# ##############################################################################################################################################################
# # ##############################################################################################################################################################
#  Ajustando os templates

[02:33]

## Mongo Deployment

- Iremos começar pelo Deployment do Mongo:
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/mongodb-deployment.yaml

- Vamos editar o name, que precisa ser dinâmico.
alterando para:
  {{ .Release.Name }}-mongodb-deployment

- Desta maneira não vai conflitar, mesmo que o name seja igual a outro, pois estarão em Namespaces separados, provavelmente.

- Documentação:
<https://helm.sh/docs/chart_template_guide/builtin_objects/>


- Vamos ajustar também o "matchLabels", pela mesma idéia. Pois teremos várias releases, daí elas podem conflitar, se estiverem iguais.



- Atualmente o valor da tag da imagem Docker utilizada é fixo, iremos colocar somente a tag dinâmica, visto que é um valor que se altera com frequência:
ANTES:
~~~~yaml
        image: mongo:4.2.8
~~~~


- No Deployment do Template, colocamos objetos do Values:
DEPOIS:
~~~~yaml
        image: mongo:{{ .Values.mongodb.tag }}
~~~~

- No arquivo values.yaml, precisamos montar a estrutura abaixo:

~~~~yaml
mongodb:
  tag: 4.2.8
~~~~






- ANTES:

~~~~yaml
        env:        
          - name: MONGO_INITDB_ROOT_USERNAME
            value: mongouser
          - name: MONGO_INITDB_ROOT_PASSWORD
            value: mongopwd
~~~~


- DEPOIS:

~~~~yaml
        env:        
          - name: MONGO_INITDB_ROOT_USERNAME
            value: {{ .Values.mongodb.credentials.userName }}
          - name: MONGO_INITDB_ROOT_PASSWORD
            value: {{ .Values.mongodb.credentials.userPassword }}
~~~~


- No arquivo values.yaml, precisamos montar a estrutura abaixo, colocando os campos de credentials:

~~~~yaml
mongodb:
  tag: 4.2.8
  credentials:
    userName: mongouser
    userPassword: mongopwd
~~~~



- Resultado final do Deployment do Mongo:

~~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-mongodb-deployment
spec:
  selector:
    matchLabels:
      app: {{ .Release.Name }}-mongodb
  template:
    metadata:     
      labels:
        app: {{ .Release.Name }}-mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:{{ .Values.mongodb.tag }}
        ports:
        - containerPort: 27017
        resources:
          requests:
            memory: "1Gi"
            cpu: "1500m"
          limits:
            memory: "1Gi"
            cpu: "1500m"
        env:        
          - name: MONGO_INITDB_ROOT_USERNAME
            value: {{ .Values.mongodb.credentials.userName }}
          - name: MONGO_INITDB_ROOT_PASSWORD
            value: {{ .Values.mongodb.credentials.userPassword }}
~~~~






## Mongo Service

- Agora vamos ajustar o Service do Mongo:
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/mongodb-service.yaml

- Atualmente ele está assim:

~~~~yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongodb
  ports:
  - port: 27017
    targetPort: 27017
~~~~


- Vamos ajustar o name, para que seja dinâmico.
- Iremos usar o nome da Release, que nem fizemos para o Deployment do Mongo.
    {{ .Release.Name }}-


- DEPOIS:

~~~~yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-mongo-service
spec:
  selector:
    app: {{ .Release.Name }}-mongodb
  ports:
  - port: 27017
    targetPort: 27017
~~~~





## API Deployment

[09:41]

- Ajustar o Deployment da API



- API tava sem os Requests e Limits, ajustei:

~~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: fabricioveronez/pedelogo-catalogo:v1
        ports:
        - containerPort: 80
        imagePullPolicy: Always
        resources:
          requests:
            memory: "32Mi"
            cpu: "500m"
          limits:
            memory: "64Mi"
            cpu: "500m"
        env:
          - name: Mongo__User
            value: mongouser
          - name: Mongo__Password
            value: mongopwd
          - name: Mongo__Host
            value: mongo-service
          - name: Mongo__DataBase
            value: admin

~~~~



- Vamos editar o Deployment da API.
adicionar o release name na frente de alguns nomes
{{ .Release.Name }}-


- No campo da imagem do container, mudar:
DE:
        image: fabricioveronez/pedelogo-catalogo:v1
PARA:
        image: {{ .Values.api.image }}



- No arquivo values.yaml, precisamos montar a estrutura abaixo, colocando os campos sobre API:

~~~~yaml
api:
  image: fabricioveronez/pedelogo-catalogo:v1

mongodb:
  tag: 4.2.8
  credentials:
    userName: mongouser
    userPassword: mongopwd
~~~~




- Nos campos das variáveis de ambiente, substituir alguns campos por:
            value: {{ .Values.mongodb.credentials.userName }}
            value: {{ .Values.mongodb.credentials.userPassword }}
                  {{ .Release.Name }}-mongo-service
 nome do banco, também usar valores do Values
 {{ .Values.mongodb.databaseName }}


- Ajustar o values.yaml novamente, adicionando o nome do database:

~~~~yaml
api:
  image: fabricioveronez/pedelogo-catalogo:v1

mongodb:
  tag: 4.2.8
  credentials:
    userName: mongouser
    userPassword: mongopwd
  databaseName: admin
~~~~



- Deployment da API ajustado:

/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/api-deployment.yaml

~~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-api-deployment
spec:
  selector:
    matchLabels:
      app: {{ .Release.Name }}-api
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}-api
    spec:
      containers:
      - name: api
        image: {{ .Values.api.image }}
        ports:
        - containerPort: 80
        imagePullPolicy: Always
        resources:
          requests:
            memory: "32Mi"
            cpu: "500m"
          limits:
            memory: "64Mi"
            cpu: "500m"
        env:
          - name: Mongo__User
            value: {{ .Values.mongodb.credentials.userName }}
          - name: Mongo__Password
            value: {{ .Values.mongodb.credentials.userPassword }}
          - name: Mongo__Host
            value: {{ .Release.Name }}-mongo-service
          - name: Mongo__DataBase
            value: {{ .Values.mongodb.databaseName }}
~~~~




- Ajustar o Service da API agora.
- Manifesto do Service API antes:

ANTES

~~~~yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  selector:
    app: api
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
~~~~



- DEPOIS:

~~~~yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-api-service
spec:
  selector:
    app: {{ .Release.Name }}-api
  ports:
  - port: 80
    targetPort: 80
  type: {{ .Values.api.serviceType }}
~~~~



- Ajustado o arquivo values.yaml:

~~~~yaml
api:
  image: fabricioveronez/pedelogo-catalogo:v1
  serviceType: LoadBalancer

mongodb:
  tag: 4.2.8
  credentials:
    userName: mongouser
    userPassword: mongopwd
  databaseName: admin
~~~~





- Como não estamos usando o Chart num repositório git ainda, iremos apontar para a pasta.

- Pasta contendo os manifestos originais:
  /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart

- Pasta contendo os arquivos para o Helm, chart "api-produto":
  /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto

cd /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto

fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ ls -lhasp
total 28K
4.0K drwxr-xr-x 4 fernando fernando 4.0K Jan 10 21:58 ./
4.0K drwxr-xr-x 3 fernando fernando 4.0K Jan 10 22:05 ../
4.0K drwxr-xr-x 2 fernando fernando 4.0K Jan 10 21:58 charts/
4.0K -rw-r--r-- 1 fernando fernando 1.2K Jan 10 21:58 Chart.yaml
4.0K -rw-r--r-- 1 fernando fernando  349 Jan 10 21:58 .helmignore
4.0K drwxr-xr-x 2 fernando fernando 4.0K Jan 10 22:05 templates/
4.0K -rw-r--r-- 1 fernando fernando  189 Jan 13 22:00 values.yaml
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$




- Comando para instalar o Chart:
helm install minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto



- Comando para instalar o Chart:
helm install minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug

/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-dry-run-chart.yaml