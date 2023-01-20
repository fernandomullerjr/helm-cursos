



# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
#  FONTE
- Fonte:
https://helm.sh/docs/chart_template_guide/control_structures/#controlling-whitespace
<https://helm.sh/docs/chart_template_guide/control_structures/#controlling-whitespace>





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
#  RESUMO

Primeiro, a sintaxe das chaves das declarações do modelo pode ser modificada com caracteres especiais para instruir o mecanismo do modelo a cortar os espaços em branco.

 {{- (com o traço e o espaço adicionados) indica que os espaços em branco devem ser cortados à esquerda, 
 enquanto -}} significa que os espaços em branco à direita devem ser consumidos. 
 Tome cuidado! Novas linhas são espaços em branco!



# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
#  Explicação - site

In this section, we'll talk about if, with, and range. The others are covered in the "Named Templates" section later in this guide.
If/Else

The first control structure we'll look at is for conditionally including blocks of text in a template. This is the if/else block.

The basic structure for a conditional looks like this:

{{ if PIPELINE }}
  # Do something
{{ else if OTHER PIPELINE }}
  # Do something else
{{ else }}
  # Default case
{{ end }}

Notice that we're now talking about pipelines instead of values. The reason for this is to make it clear that control structures can execute an entire pipeline, not just evaluate a value.

A pipeline is evaluated as false if the value is:

    a boolean false
    a numeric zero
    an empty string
    a nil (empty or null)
    an empty collection (map, slice, tuple, dict, array)

Under all other conditions, the condition is true.

Vamos adicionar uma condicional simples ao nosso ConfigMap. Adicionaremos outra configuração se a bebida for café:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  drink: {{ .Values.favorite.drink | default "tea" | quote }}
  food: {{ .Values.favorite.food | upper | quote }}
  {{ if eq .Values.favorite.drink "coffee" }}mug: "true"{{ end }}

Como comentamos drink: coffeeem nosso último exemplo, a saída não deve incluir um mug: "true"sinalizador. Mas se adicionarmos essa linha de volta ao nosso values.yamlarquivo, a saída deve ficar assim:

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: eyewitness-elk-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"
  mug: "true"

Controlando o espaço em branco

Enquanto examinamos os condicionais, devemos dar uma olhada rápida na maneira como os espaços em branco são controlados nos modelos. Vamos pegar o exemplo anterior e formatá-lo para ficar um pouco mais fácil de ler:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  drink: {{ .Values.favorite.drink | default "tea" | quote }}
  food: {{ .Values.favorite.food | upper | quote }}
  {{ if eq .Values.favorite.drink "coffee" }}
    mug: "true"
  {{ end }}

Inicialmente, isso parece bom. Mas se o executarmos no mecanismo de modelo, obteremos um resultado infeliz:

$ helm install --dry-run --debug ./mychart
SERVER: "localhost:44134"
CHART PATH: /Users/mattbutcher/Code/Go/src/helm.sh/helm/_scratch/mychart
Error: YAML parse error on mychart/templates/configmap.yaml: error converting YAML to JSON: yaml: line 9: did not find expected key

O que aconteceu? Geramos YAML incorreto devido ao espaço em branco acima.

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: eyewitness-elk-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"
    mug: "true"

mugestá incorretamente recuado. Vamos simplesmente remover essa linha e executar novamente:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  drink: {{ .Values.favorite.drink | default "tea" | quote }}
  food: {{ .Values.favorite.food | upper | quote }}
  {{ if eq .Values.favorite.drink "coffee" }}
  mug: "true"
  {{ end }}

Quando enviarmos isso, obteremos YAML que é válido, mas ainda parece um pouco engraçado:

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: telling-chimp-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"

  mug: "true"

Observe que recebemos algumas linhas vazias em nosso YAML. Porque? Quando o mecanismo de modelo é executado, ele remove o conteúdo de {{end }}, mas deixa o espaço em branco restante exatamente como está.

O YAML atribui significado ao espaço em branco, portanto, gerenciar o espaço em branco torna-se muito importante. Felizmente, os modelos do Helm têm algumas ferramentas para ajudar.

Primeiro, a sintaxe das chaves das declarações do modelo pode ser modificada com caracteres especiais para instruir o mecanismo do modelo a cortar os espaços em branco. {{-(com o traço e o espaço adicionados) indica que o espaço em branco deve ser cortado à esquerda, enquanto -}}significa que o espaço em branco à direita deve ser consumido. Tome cuidado! Novas linhas são espaços em branco!

    Certifique-se de que haja um espaço entre o -e o restante de sua diretiva. {{- 3 }}significa "aparar o espaço em branco esquerdo e imprimir 3" enquanto {{-3 }}significa "imprimir -3".

Usando esta sintaxe, podemos modificar nosso modelo para nos livrarmos dessas novas linhas:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  drink: {{ .Values.favorite.drink | default "tea" | quote }}
  food: {{ .Values.favorite.food | upper | quote }}
  {{- if eq .Values.favorite.drink "coffee" }}
  mug: "true"
  {{- end }}

Apenas para esclarecer este ponto, vamos ajustar o que foi dito acima e substituir por um *para cada espaço em branco que será excluído seguindo esta regra. Um *no final da linha indica um caractere de nova linha que seria removido

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  drink: {{ .Values.favorite.drink | default "tea" | quote }}
  food: {{ .Values.favorite.food | upper | quote }}*
**{{- if eq .Values.favorite.drink "coffee" }}
  mug: "true"*
**{{- end }}

Tendo isso em mente, podemos rodar nosso template pelo Helm e ver o resultado:

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: clunky-cat-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"
  mug: "true"

Tenha cuidado com os modificadores de mastigação. É fácil acidentalmente fazer coisas como esta:

  food: {{ .Values.favorite.food | upper | quote }}
  {{- if eq .Values.favorite.drink "coffee" -}}
  mug: "true"
  {{- end -}}

Isso produzirá food: "PIZZA"mug: "true"porque consumiu novas linhas em ambos os lados.

    For the details on whitespace control in templates, see the Official Go template documentation

Finally, sometimes it's easier to tell the template system how to indent for you instead of trying to master the spacing of template directives. For that reason, you may sometimes find it useful to use the indent function ({{ indent 2 "mug:true" }}).
Modifying scope using with

The next control structure to look at is the with action. This controls variable scoping. Recall that . is a reference to the current scope. So .Values tells the template to find the Values object in the current scope.

The syntax for with is similar to a simple if statement:

{{ with PIPELINE }}
  # restricted scope
{{ end }}

Scopes can be changed. with can allow you to set the current scope (.) to a particular object. For example, we've been working with .Values.favorite. Let's rewrite our ConfigMap to alter the . scope to point to .Values.favorite:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  {{- end }}

Note that we removed the if conditional from the previous exercise because it is now unnecessary - the block after with only executes if the value of PIPELINE is not empty.

Notice that now we can reference .drink and .food without qualifying them. That is because the with statement sets . to point to .Values.favorite. The . is reset to its previous scope after {{ end }}.

But here's a note of caution! Inside of the restricted scope, you will not be able to access the other objects from the parent scope using .. This, for example, will fail:

  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  release: {{ .Release.Name }}
  {{- end }}

It will produce an error because Release.Name is not inside of the restricted scope for .. However, if we swap the last two lines, all will work as expected because the scope is reset after {{ end }}.

  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  {{- end }}
  release: {{ .Release.Name }}

Or, we can use $ for accessing the object Release.Name from the parent scope. $ is mapped to the root scope when template execution begins and it does not change during template execution. The following would work as well:

  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  release: {{ $.Release.Name }}
  {{- end }}

After looking at range, we will take a look at template variables, which offer one solution to the scoping issue above.
Looping with the range action

Many programming languages have support for looping using for loops, foreach loops, or similar functional mechanisms. In Helm's template language, the way to iterate through a collection is to use the range operator.

To start, let's add a list of pizza toppings to our values.yaml file:

favorite:
  drink: coffee
  food: pizza
pizzaToppings:
  - mushrooms
  - cheese
  - peppers
  - onions

Now we have a list (called a slice in templates) of pizzaToppings. We can modify our template to print this list into our ConfigMap:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  {{- end }}
  toppings: |-
    {{- range .Values.pizzaToppings }}
    - {{ . | title | quote }}
    {{- end }}    

We can use $ for accessing the list Values.pizzaToppings from the parent scope. $ is mapped to the root scope when template execution begins and it does not change during template execution. The following would work as well:

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  toppings: |-
    {{- range $.Values.pizzaToppings }}
    - {{ . | title | quote }}
    {{- end }}    
  {{- end }}

Let's take a closer look at the toppings: list. The range function will "range over" (iterate through) the pizzaToppings list. But now something interesting happens. Just like with sets the scope of ., so does a range operator. Each time through the loop, . is set to the current pizza topping. That is, the first time, . is set to mushrooms. The second iteration it is set to cheese, and so on.

We can send the value of . directly down a pipeline, so when we do {{ . | title | quote }}, it sends . to title (title case function) and then to quote. If we run this template, the output will be:

# Source: mychart/templates/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: edgy-dragonfly-configmap
data:
  myvalue: "Hello World"
  drink: "coffee"
  food: "PIZZA"
  toppings: |-
    - "Mushrooms"
    - "Cheese"
    - "Peppers"
    - "Onions"    

Now, in this example we've done something tricky. The toppings: |- line is declaring a multi-line string. So our list of toppings is actually not a YAML list. It's a big string. Why would we do this? Because the data in ConfigMaps data is composed of key/value pairs, where both the key and the value are simple strings. To understand why this is the case, take a look at the Kubernetes ConfigMap docs. For us, though, this detail doesn't matter much.

    The |- marker in YAML takes a multi-line string. This can be a useful technique for embedding big blocks of data inside of your manifests, as exemplified here.

Sometimes it's useful to be able to quickly make a list inside of your template, and then iterate over that list. Helm templates have a function to make this easy: tuple. In computer science, a tuple is a list-like collection of fixed size, but with arbitrary data types. This roughly conveys the way a tuple is used.

  sizes: |-
    {{- range tuple "small" "medium" "large" }}
    - {{ . }}
    {{- end }}    

The above will produce this:

  sizes: |-
    - small
    - medium
    - large    

In addition to lists and tuples, range can be used to iterate over collections that have a key and a value (like a map or dict). We'll see how to do that in the next section when we introduce template variables.
Prev

← Template Function List
Next

Variables →

Helm Project
    Blog 
    Events 
    Quick Start Guide 
    Code of Conduct 

Charts
    Introduction 
    Chart tips & tricks 
    Developing Charts 
    Search 800+ Charts 

Development
    #helm-dev (slack) 
    Contribution Guide 
    Mantenedores 
    Reuniões semanais 

Comunidade
    #helm-users (slack) 
    lista de discussão 
    Logotipos e Arte 
    Twitter 

Somos um projeto graduado da Cloud Native Computing Foundation.
We are a Cloud Native Computing Foundation project

© Helm Autores 2023 | Documentação distribuída sob CC-BY-4.0

© 2023 Fundação Linux. Todos os direitos reservados. A Linux Foundation tem marcas registradas e usa marcas comerciais. Para obter uma lista de marcas registradas da The Linux Foundation, consulte nossa página de uso de marcas registradas .














# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# 


- ANTES:


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
        envFrom:
          {{ if empty .Values.mongodb.existSecret }}
          - secretRef:
              name: {{ .Release.Name }}-mongodb-secret
          {{ else }}
          - secretRef:
              name: {{ .Values.mongodb.existSecret }}
          {{ end }}
~~~~




- Simulando upgrade:
helm upgrade minhaapi <caminho-do-chart> --dry-run --debug
helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug





~~~~bash
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
fernando@debian10x64:~$
~~~~





- DEPOIS:

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
        envFrom:
          {{ if empty .Values.mongodb.existSecret }}
          - secretRef:
              name: {{ .Release.Name }}-mongodb-secret
          {{ else }}
          - secretRef:
              name: {{ .Values.mongodb.existSecret }}
          {{- end }}
~~~~


- Simulando upgrade:
helm upgrade minhaapi <caminho-do-chart> --dry-run --debug
helm upgrade minhaapi /home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto --dry-run --debug

- Após aplicado AJUSTE, colocando o traço no começo da das chaves, o espaço em branco após o "envFrom" não surgiu novamente:

~~~~bash
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
fernando@debian10x64:~$
~~~~