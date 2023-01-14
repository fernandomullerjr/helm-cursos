
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 6 - Estrutura If/Else"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 6 - Estrutura If/Else


- Quando queremos usar um Secret, de forma dinâmica, sem passar no Values, usando um Secret já existente nas variáveis do trecho "env" no manifesto do Mongo-Deployment, por exemplo.

- Pasta com os manifestos do Chart:
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates

- Mongo-Deployment

ANTES

~~~~YAML
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






# SECRET

- Criar manifesto para o Secret.
na pasta
/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates


- O manifesto do Secret vai ficar assim:

~~~~YAML
apiVersion: v1
kind: Secret
metadata:
  name: {{ .Release.Name }}-mongodb-secret
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: {{ .Values.mongodb.credentials.userName }}
  MONGO_INITDB_ROOT_PASSWORD: {{ .Values.mongodb.credentials.userPassword }}
~~~~


- Neste formato temos um problema, pois o valor do campo "MONGO_INITDB_ROOT_USERNAME" vai receber um valor simples, o username mongouser. Porém o Secret precisa do valor em base64.
- Precisamos do valor encodado.
- Iremos usar funções, usando o pipe | e adicionando funções necessárias.
    b64enc - passar para base64 o valor
    quote - adiciona aspas.

- Ajustando:

~~~~YAML
apiVersion: v1
kind: Secret
metadata:
  name: {{ .Release.Name }}-mongodb-secret
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: {{ .Values.mongodb.credentials.userName | b64enc | quote }}
  MONGO_INITDB_ROOT_PASSWORD: {{ .Values.mongodb.credentials.userPassword | b64enc | quote }}
~~~~


- Mais exemplos de funções no Helm:
https://helm.sh/docs/howto/charts_tips_and_tricks/
<https://helm.sh/docs/howto/charts_tips_and_tricks/>