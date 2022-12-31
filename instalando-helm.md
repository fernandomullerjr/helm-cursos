

https://helm.sh/docs/intro/install/
<https://helm.sh/docs/intro/install/>

# From Script

Helm now has an installer script that will automatically grab the latest version of Helm and install it locally.

You can fetch that script, and then execute it locally. It's well documented so that you can read through it and understand what it is doing before you run it.

$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

Yes, you can curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash if you want to live on the edge.



curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh