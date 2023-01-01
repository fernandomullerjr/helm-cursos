
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 4 - Instalação aplicações"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 4 - Instalação aplicações

- Para exemplificar a instalação de uma aplicação, iremos usar o "NGINX Ingress Controller".


1. Adicionar o repositório que tem o "NGINX Ingress Controller".
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

2. Fazer o update:
    helm repo update


~~~~bash
fernando@debian10x64:~$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
"ingress-nginx" has been added to your repositories
fernando@debian10x64:~$
fernando@debian10x64:~$
fernando@debian10x64:~$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
fernando@debian10x64:~$
~~~~


3. Procurar pelo "NGINX Ingress Controller" nos meus repositórios:
    helm search repo ingress

~~~~bash
fernando@debian10x64:~$ helm search repo ingress
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
ingress-nginx/ingress-nginx     4.4.2           1.5.1           Ingress controller for Kubernetes using NGINX a...
stable/gce-ingress              1.2.2           1.4.0           DEPRECATED A GCE Ingress Controller
stable/ingressmonitorcontroller 1.0.50          1.0.47          DEPRECATED - IngressMonitorController chart tha...
stable/nginx-ingress            1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller that us...
stable/contour                  0.2.2           v0.15.0         DEPRECATED Contour Ingress controller for Kuber...
stable/external-dns             1.8.0           0.5.14          Configure external DNS servers (AWS Route53, Go...
stable/kong                     0.36.7          1.4             DEPRECATED The Cloud-Native Ingress and API-man...
stable/lamp                     1.1.6           7               DEPRECATED - Modular and transparent LAMP stack...
stable/nginx-lego               0.3.1                           Chart for nginx-ingress-controller and kube-lego
stable/traefik                  1.87.7          1.7.26          DEPRECATED - A Traefik based Kubernetes ingress...
stable/voyager                  3.2.4           6.0.0           DEPRECATED Voyager by AppsCode - Secure Ingress...
fernando@debian10x64:~$
~~~~


Nesse caso ele trouxe tudo que continha ingress.















4. Inspecionar o chart.
    helm inspect all ingress-nginx/ingress-nginx

Ele traz as especificações sobre aquele Chart, TODOS os detalhes dele.


/home/fernando/cursos/helm-cursos/DevOps-Pro-Helm/004-saida-do-inspect.md

~~~~bash

fernando@debian10x64:~$ helm inspect all ingress-nginx/ingress-nginx
annotations:
  artifacthub.io/changes: |
    - Adding support for disabling liveness and readiness probes to the Helm chart
    - add:(admission-webhooks) ability to set securityContext
    - Updated Helm chart to use the fullname for the electionID if not specified
    - Rename controller-wehbooks-networkpolicy.yaml
  artifacthub.io/prerelease: "false"
apiVersion: v2
appVersion: 1.5.1
description: Ingress controller for Kubernetes using NGINX as a reverse proxy and
  load balancer
home: https://github.com/kubernetes/ingress-nginx
icon: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c5/Nginx_logo.svg/500px-Nginx_logo.svg.png
keywords:
- ingress
- nginx
kubeVersion: '>=1.20.0-0'
maintainers:
- name: rikatz
- name: strongjz
- name: tao12345666333
name: ingress-nginx
sources:
- https://github.com/kubernetes/ingress-nginx
version: 4.4.2

---
## nginx configuration
## Ref: https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/index.md
##

## Overrides for generated resource names
# See templates/_helpers.tpl
# nameOverride:
# fullnameOverride:

## Labels to apply to all resources
##
commonLabels: {}
# scmhash: abc123
# myLabel: aakkmd

controller:
  name: controller
  image:
    ## Keep false as default for now!
    chroot: false
    registry: registry.k8s.io
    image: ingress-nginx/controller
    ## for backwards compatibility consider setting the full image url via the repository value below
    ## use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
    ## repository:
    tag: "v1.5.1"
    digest: sha256:4ba73c697770664c1e00e9f968de14e08f606ff961c76e5d7033a4a9c593c629
    digestChroot: sha256:c1c091b88a6c936a83bd7b098662760a87868d12452529bad0d178fb36147345
    pullPolicy: IfNotPresent
    # www-data -> uid 101
    runAsUser: 101
    allowPrivilegeEscalation: true

  # -- Use an existing PSP instead of creating one
  existingPsp: ""

  # -- Configures the controller container name
  containerName: controller

  # -- Configures the ports that the nginx-controller listens on
  containerPort:
    http: 80
    https: 443

  # -- Will add custom configuration options to Nginx https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/
  config: {}

  # -- Annotations to be added to the controller config configuration configmap.
  configAnnotations: {}

~~~~


[...]
Restante do inspect omitido.

















5. Inspecionando apenas os Values.
Removemos a palavra "all" do comando anterior e botamos "values":
    helm inspect values ingress-nginx/ingress-nginx


Agora ele traz apenas informações sobre os Values:

~~~~yaml
## nginx configuration
## Ref: https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/index.md
##

## Overrides for generated resource names
# See templates/_helpers.tpl
# nameOverride:
# fullnameOverride:

## Labels to apply to all resources
##
commonLabels: {}
# scmhash: abc123
# myLabel: aakkmd

controller:
  name: controller
  image:
    ## Keep false as default for now!
    chroot: false
    registry: registry.k8s.io
    image: ingress-nginx/controller
    ## for backwards compatibility consider setting the full image url via the repository value below
    ## use *either* current default registry/image or repository format or installing chart by providing the values.yaml will fail
    ## repository:
    tag: "v1.5.1"
    digest: sha256:4ba73c697770664c1e00e9f968de14e08f606ff961c76e5d7033a4a9c593c629
    digestChroot: sha256:c1c091b88a6c936a83bd7b098662760a87868d12452529bad0d178fb36147345
    pullPolicy: IfNotPresent
    # www-data -> uid 101
    runAsUser: 101
    allowPrivilegeEscalation: true

  # -- Use an existing PSP instead of creating one
  existingPsp: ""

  # -- Configures the controller container name
  containerName: controller

  # -- Configures the ports that the nginx-controller listens on
  containerPort:
    http: 80
    https: 443

  # -- Will add custom configuration options to Nginx https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/
  config: {}

  # -- Annotations to be added to the controller config configuration configmap.
  configAnnotations: {}

  # -- Will add custom headers before sending traffic to backends according to https://github.com/kubernetes/ingress-nginx/tree/main/docs/examples/customization/custom-headers
  proxySetHeaders: {}

  # -- Will add custom headers before sending response traffic to the client according to: https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#add-headers
  addHeaders: {}

  # -- Optionally customize the pod dnsConfig.
  dnsConfig: {}

  # -- Optionally customize the pod hostname.
  hostname: {}

  # -- Optionally change this to ClusterFirstWithHostNet in case you have 'hostNetwork: true'.
  # By default, while using host network, name resolution uses the host's DNS. If you wish nginx-controller
  # to keep resolving names inside the k8s network, use ClusterFirstWithHostNet.
  dnsPolicy: ClusterFirst

~~~~


Aqui só consigo ver o Template, não consigo ver os valores deste arquivo.








- Verificando o arquivo "helm-cursos/DevOps-Pro-Helm/004-saida-do-inspect-values.yaml"
Podemos ver as opções que o Template permite.
Como por exemplo, no Kind podemos usar Deployment ou DaemonSet, usando DaemonSet teríamos maior resiliencia, pois haveria uma cópia do ingress em cada Node.

~~~~yaml

  # -- Use a `DaemonSet` or `Deployment`
  kind: Deployment

~~~~







6. Instalando a primeira aplicação, sem personalizar os valores, indo com tudo default por enquanto:
    helm install meu-ingress-controller ingress-nginx/ingress-nginx
