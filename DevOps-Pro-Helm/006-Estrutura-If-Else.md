
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


http://masterminds.github.io/sprig/encoding.html
<http://masterminds.github.io/sprig/encoding.html>
    b64enc/b64dec: Encode or decode with Base64
    b32enc/b32dec: Encode or decode with Base32




## Quote Strings, Don't Quote Integers

When you are working with string data, you are always safer quoting the strings than leaving them as bare words:

name: {{ .Values.MyName | quote }}

But when working with integers do not quote the values. That can, in many cases, cause parsing errors inside of Kubernetes.

port: {{ .Values.Port }}




- Ajustando o manifesto do MongoDB Deployment:

DE:

~~~YAML
        env:        
          - name: MONGO_INITDB_ROOT_USERNAME
            value: {{ .Values.mongodb.credentials.userName }}
          - name: MONGO_INITDB_ROOT_PASSWORD
            value: {{ .Values.mongodb.credentials.userPassword }}
~~~

PARA:

~~~YAML
        envFrom:
          - secretRef:
            name: {{ .Release.Name }}-mongodb-secret
~~~




cd /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto
cd /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto


- Simulando upgrade:
helm upgrade minhaapi <caminho-do-chart> --dry-run --debug
helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug


- Apresentou erro:

~~~~bash
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug
upgrade.go:142: [debug] preparing upgrade for minhaapi
Error: UPGRADE FAILED: error validating "": error validating data: ValidationError(Deployment.spec.template.spec.containers[0].envFrom[0]): unknown field "name" in io.k8s.api.core.v1.EnvFromSource
helm.go:84: [debug] error validating "": error validating data: ValidationError(Deployment.spec.template.spec.containers[0].envFrom[0]): unknown field "name" in io.k8s.api.core.v1.EnvFromSource
helm.sh/helm/v3/pkg/kube.scrubValidationError
        helm.sh/helm/v3/pkg/kube/client.go:643
helm.sh/helm/v3/pkg/kube.(*Client).Build
        helm.sh/helm/v3/pkg/kube/client.go:215
helm.sh/helm/v3/pkg/action.validateManifest
        helm.sh/helm/v3/pkg/action/upgrade.go:531
helm.sh/helm/v3/pkg/action.(*Upgrade).prepareUpgrade
        helm.sh/helm/v3/pkg/action/upgrade.go:259
helm.sh/helm/v3/pkg/action.(*Upgrade).RunWithContext
        helm.sh/helm/v3/pkg/action/upgrade.go:143
main.newUpgradeCmd.func2
        helm.sh/helm/v3/cmd/helm/upgrade.go:199
github.com/spf13/cobra.(*Command).execute
        github.com/spf13/cobra@v1.5.0/command.go:872
github.com/spf13/cobra.(*Command).ExecuteC
        github.com/spf13/cobra@v1.5.0/command.go:990
github.com/spf13/cobra.(*Command).Execute
        github.com/spf13/cobra@v1.5.0/command.go:918
main.main
        helm.sh/helm/v3/cmd/helm/helm.go:83
runtime.main
        runtime/proc.go:250
runtime.goexit
        runtime/asm_amd64.s:1571
UPGRADE FAILED
main.newUpgradeCmd.func2
        helm.sh/helm/v3/cmd/helm/upgrade.go:201
github.com/spf13/cobra.(*Command).execute
        github.com/spf13/cobra@v1.5.0/command.go:872
github.com/spf13/cobra.(*Command).ExecuteC
        github.com/spf13/cobra@v1.5.0/command.go:990
github.com/spf13/cobra.(*Command).Execute
        github.com/spf13/cobra@v1.5.0/command.go:918
main.main
        helm.sh/helm/v3/cmd/helm/helm.go:83
runtime.main
        runtime/proc.go:250
runtime.goexit
        runtime/asm_amd64.s:1571
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$


fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS  CHART                   APP VERSION
minhaapi        default         1               2023-01-14 00:39:29.189971908 -0300 -03 failed  api-produto-0.1.0       1.16.0
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$

~~~~






- Analisando, foi ajustada a identação do YAML, na parte sobre o name da Secret:

~~~~YAML
        envFrom:
          - secretRef:
              name: {{ .Release.Name }}-mongodb-secret
~~~~


- Nova tentativa de upgrade usando Dry-Run, para validar:
helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug

- Funcionou, porém o valor do "MONGO_INITDB_ROOT_USERNAME" e do "MONGO_INITDB_ROOT_PASSWORD" não ficou em base64:

~~~~bash
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug
upgrade.go:142: [debug] preparing upgrade for minhaapi
upgrade.go:150: [debug] performing update for minhaapi
upgrade.go:313: [debug] dry run for minhaapi
Release "minhaapi" has been upgraded. Happy Helming!
NAME: minhaapi
LAST DEPLOYED: Sat Jan 14 20:35:29 2023
NAMESPACE: default
STATUS: pending-upgrade
REVISION: 2
TEST SUITE: None
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
api:
  image: fabricioveronez/pedelogo-catalogo:v1
  serviceType: LoadBalancer
mongodb:
  credentials:
    userName: mongouser
    userPassword: mongopwd
  databaseName: admin
  tag: 4.2.8

HOOKS:
MANIFEST:
---
# Source: api-produto/templates/mongodb-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: minhaapi-mongodb-secret
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: mongouser
  MONGO_INITDB_ROOT_PASSWORD: mongopwd
---
# Source: api-produto/templates/api-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: minhaapi-api-service
spec:
  selector:
    app: minhaapi-api
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
---
# Source: api-produto/templates/mongodb-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: minhaapi-mongo-service
spec:
  selector:
    app: minhaapi-mongodb
  ports:
  - port: 27017
    targetPort: 27017
---
# Source: api-produto/templates/api-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minhaapi-api-deployment
spec:
  selector:
    matchLabels:
      app: minhaapi-api
  template:
    metadata:
      labels:
        app: minhaapi-api
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
            value: minhaapi-mongo-service
          - name: Mongo__DataBase
            value: admin
---
# Source: api-produto/templates/mongodb-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minhaapi-mongodb-deployment
spec:
  selector:
    matchLabels:
      app: minhaapi-mongodb
  template:
    metadata:
      labels:
        app: minhaapi-mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:4.2.8
        ports:
        - containerPort: 27017
        resources:
          requests:
            memory: "1Gi"
            cpu: "1500m"
          limits:
            memory: "1Gi"
            cpu: "1500m"
        envFrom:
          - secretRef:
              name: minhaapi-mongodb-secret

NOTES:
Instalado
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$

~~~~







- Pegando o ajustado de antes, que faltou passar pro manifesto do Secret, que acabou ficando sem os "b64enc | quote" para encodar:

/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/mongodb-secret.yaml

DE:

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

PARA:

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



- Nova tentativa de upgrade usando Dry-Run, para validar:
helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug

- Funcionou, pt2, agora gerou todos os manifestos e encodou as Secrets em base64, conforme o esperado:
  MONGO_INITDB_ROOT_USERNAME: "bW9uZ291c2Vy"
  MONGO_INITDB_ROOT_PASSWORD: "bW9uZ29wd2Q="

~~~~bash
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$ helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug
upgrade.go:142: [debug] preparing upgrade for minhaapi
upgrade.go:150: [debug] performing update for minhaapi
upgrade.go:313: [debug] dry run for minhaapi
Release "minhaapi" has been upgraded. Happy Helming!
NAME: minhaapi
LAST DEPLOYED: Sat Jan 14 20:39:11 2023
NAMESPACE: default
STATUS: pending-upgrade
REVISION: 2
TEST SUITE: None
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
api:
  image: fabricioveronez/pedelogo-catalogo:v1
  serviceType: LoadBalancer
mongodb:
  credentials:
    userName: mongouser
    userPassword: mongopwd
  databaseName: admin
  tag: 4.2.8

HOOKS:
MANIFEST:
---
# Source: api-produto/templates/mongodb-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: minhaapi-mongodb-secret
type: Opaque
data:
  MONGO_INITDB_ROOT_USERNAME: "bW9uZ291c2Vy"
  MONGO_INITDB_ROOT_PASSWORD: "bW9uZ29wd2Q="
---
# Source: api-produto/templates/api-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: minhaapi-api-service
spec:
  selector:
    app: minhaapi-api
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
---
# Source: api-produto/templates/mongodb-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: minhaapi-mongo-service
spec:
  selector:
    app: minhaapi-mongodb
  ports:
  - port: 27017
    targetPort: 27017
---
# Source: api-produto/templates/api-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minhaapi-api-deployment
spec:
  selector:
    matchLabels:
      app: minhaapi-api
  template:
    metadata:
      labels:
        app: minhaapi-api
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
            value: minhaapi-mongo-service
          - name: Mongo__DataBase
            value: admin
---
# Source: api-produto/templates/mongodb-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minhaapi-mongodb-deployment
spec:
  selector:
    matchLabels:
      app: minhaapi-mongodb
  template:
    metadata:
      labels:
        app: minhaapi-mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:4.2.8
        ports:
        - containerPort: 27017
        resources:
          requests:
            memory: "1Gi"
            cpu: "1500m"
          limits:
            memory: "1Gi"
            cpu: "1500m"
        envFrom:
          - secretRef:
              name: minhaapi-mongodb-secret

NOTES:
Instalado
fernando@debian10x64:~/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto$

~~~~






git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 6 - Estrutura If/Else - TSHOOT - Ajustado o base64 nas Secrets usando a função b64enc"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





- Agora precisamos ajustar na API.
- No caso da API, vamos gerar um ConfigMap.

~~~~YAML
apiVersion: v1
kind: ConfigMap
metadata:
    name: {{ .Release.Name }}-api-configmap
data:
    Mongo__Host: {{ .Release.Name }}-mongo-service
    Mongo__DataBase: {{ .Values.mongodb.databaseName }}
~~~~


- Atualmente o Deployment do API está assim:

~~~~YAML
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





# PENDENTE:
- Continua em 09:43.
- Modificar os campos de ENV no Deployment do API, usar ConfigMAP.
