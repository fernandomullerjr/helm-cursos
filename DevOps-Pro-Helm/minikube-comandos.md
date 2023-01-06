

# Acessando 1 Service especifico via ip externo:




# OK
# Método 1 - OK - Minikube Service:

- Expondo Service que do tipo LoadBalancer, que fica com EXTERNAL-IP em pending:
  minikube service <nome-do-serviço>
  minikube service meu-ingress-controller-ingress-nginx-controller

minikube service <nome-do-serviço>
minikube service meu-ingress-controller-ingress-nginx-controller
minikube service meu-ingress-controller-ingress-nginx-controller
minikube service meu-ingress-controller-ingress-nginx-controller

fernando@debian10x64:~$ minikube service meu-ingress-controller-ingress-nginx-controller
|-----------|-------------------------------------------------|-------------|---------------------------|
| NAMESPACE |                      NAME                       | TARGET PORT |            URL            |
|-----------|-------------------------------------------------|-------------|---------------------------|
| default   | meu-ingress-controller-ingress-nginx-controller | http/80     | http://192.168.49.2:32372 |
|           |                                                 | https/443   | http://192.168.49.2:30009 |
|-----------|-------------------------------------------------|-------------|---------------------------|
* Opening service default/meu-ingress-controller-ingress-nginx-controller in default browser...


- Abrindo a página:
http://192.168.49.2:32372/

- Retorna a página do NGINX, conforme esperado, mesmo sendo o 404 neste caso:
404 Not Found
nginx






# ÑOK
# Método 2 - ÑOK - Minikube Tunnel:
# minikube tunnel

Exponodo os Services do tipo LoadBalancer.

https://minikube.sigs.k8s.io/docs/handbook/accessing/#using-minikube-tunnel
<https://minikube.sigs.k8s.io/docs/handbook/accessing/#using-minikube-tunnel>

minikube tunnel runs as a process, creating a network route on the host to the service CIDR of the cluster using the cluster’s IP address as a gateway. The tunnel command exposes the external IP directly to any program running on the host operating system.

- Comando para expor ips externos para os Services do tipo LoadBalancer no minikube:
minikube tunnel

fernando@debian10x64:~$
fernando@debian10x64:~$ minikube tunnel
[sudo] password for fernando:
Status:
        machine: minikube
        pid: 23110
        route: 10.96.0.0/12 -> 192.168.49.2
        minikube: Running
        services: [meu-ingress-controller-ingress-nginx-controller]
    errors:
                minikube: no errors
                router: no errors
                loadbalancer emulator: no errors


- OBSERVAÇÃO:
neste formato o ip não fica acessível externamente!
neste formato o ip não fica acessível externamente!
neste formato o ip não fica acessível externamente!
neste formato o ip não fica acessível externamente!