
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 8 - Named Templates"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 8 - Named Templates

- Material de apoio:
https://helm.sh/docs/chart_template_guide/named_templates/
<https://helm.sh/docs/chart_template_guide/named_templates/>


- Os "Named Templates" são Templates auxiliares.

- No caso do MongoDB Service, por exemplo, usamos o  {{ .Release.Name }}-mongo-service para definir o name do Service:

~~~~YAML
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



- Usamos diversas vezes o  {{ .Release.Name }}-mongo-service em outros pontos, normalmente.

- Para facilitar a manutenção, vamos criar um arquivo de template.
- Necessário criar um arquivo nomeado:
    _helpers.tpl
- O nome precisa ter um underscore ao inicio do nome.



Named Templates são templates no Helm que possuem nomes específicos e podem ser reutilizados em diferentes arquivos de template. Eles permitem que partes comuns de um template sejam definidas em um único lugar e depois chamadas em vários outros lugares.

Por exemplo, se você tem uma seção de configuração que é comum entre vários charts diferentes, você pode definir essa seção como um Named Template e, em seguida, chamá-la em cada chart onde ela é necessária.

A sintaxe para definir um Named Template é a seguinte:

~~~~YAML
{{- define "nome_do_template" -}}
Conteúdo do template aqui
{{- end -}}
~~~~

A sintaxe para chamar um Named Template é a seguinte:

~~~~YAML
{{ template "nome_do_template" . }}
~~~~

Espero que isso ajude a entender como os Named Templates funcionam no Helm.





- Criado o arquivo tpl:

/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/_helpers.tpl

~~~~YAML
{{- define "mongodb.serviceName" -}}
{{ .Release.Name }}-mongo-service
{{- end -}}
~~~~



- Editando o arquivo api-configmap, para que ele deixe de usar o {{ .Release.Name }}-mongo-service no campo Mongo__Host:
colocar o seguinte:
    {{ template "mongodb.serviceName" . }}

/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/api-configmap.yaml

- ANTES:

~~~~YAML
apiVersion: v1
kind: ConfigMap
metadata:
    name: {{ .Release.Name }}-api-configmap
data:
    Mongo__Host: {{ .Release.Name }}-mongo-service
    Mongo__DataBase: {{ .Values.mongodb.databaseName }}
~~~~

- DEPOIS:

~~~~YAML
apiVersion: v1
kind: ConfigMap
metadata:
    name: {{ .Release.Name }}-api-configmap
data:
    Mongo__Host: {{ template "mongodb.serviceName" . }}
    Mongo__DataBase: {{ .Values.mongodb.databaseName }}
~~~~


- O ponto é pra passar o contexto atual.
