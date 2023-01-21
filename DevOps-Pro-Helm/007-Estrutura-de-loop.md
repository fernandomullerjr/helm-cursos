
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# push

git status
git add .
git commit -m "Curso Formação DevOps PRO - Helm - Aula 7 - Estrutura de loop"
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git push
git status





# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# ##############################################################################################################################################################
# Aula 7 - Estrutura de loop

- Agora iremos adicionar uma regra para ingress.

- Criar um manifesto de ingress na pasta "templates":
DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates

- Criado arquivo:
DevOps-Pro-Helm/005-Material-aula__primeiro-helm-chart/api-produto/templates/api-ingress.yaml

~~~~YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
~~~~




- Name:
{{ .Release.Name }}-api-ingress