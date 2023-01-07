helm install <release_name> and <name_of_chart_you_want_to_install>	To install a new package
helm list	To confirm the deployment and view the currently deployed release
helm ls	To confirm the deployment and view the currently deployed release
helm history <release_name>	To view revision numbers for a particular release
helm rollback <release_name> <revision_id>	To execute a rollback
helm upgrade <release_name> <chart_name>	To upgrade a release
	
helm install meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx-ingress	Instalar pacote Helm informando o Namespace
	
helm install meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx-ingress	Instalar pacote Helm informando o Namespace
	
helm upgrade meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx-ingress --values values.yaml	Atualizar informações dos recursos via comando Upgrade
helm history meu-ingress-controller --namespace nginx-ingress	Verificando histórico de releases:
helm rollback meu-ingress-controller --namespace nginx-ingress	Retorna para versão anterior(quando não é especificado o revision ele volta para o último).
helm rollback meu-ingress-controller 2 --namespace nginx-ingress	Rollback para Revision especifica
