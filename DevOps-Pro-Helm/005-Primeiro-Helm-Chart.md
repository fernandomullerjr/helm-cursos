
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
