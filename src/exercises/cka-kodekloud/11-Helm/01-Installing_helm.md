# Installing Helm

Learning never exhausts the mind.

– Leonardo da Vinci

Don't just think, do.

– Horace


#### Reference

[Helm install](https://helm.sh/docs/intro/install/)


1. You must have Kubernetes installed for a successful and properly secured use of Helm.

Refer the documentation to check the prerequisites. The Documentation tab is available at the top right panel.

From Script
Helm now has an installer script that will automatically grab the latest version of Helm and install it locally.

You can fetch that script, and then execute it locally. It's well documented so that you can read through it and understand what it is doing before you run it.

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh

helm version
```

2. Which version?

**3.18.6**

3. Which environment variable can be used to indicate whether or not Helm is running in Debug mode?

**$HELM_DEBUG**

Use the help mode of the helm command to look for this information.

La variable de entorno que indica si Helm está en modo Debug es ``` $HELM_DEBUG ```. Cuando esta variable está activa, Helm ejecuta en modo de depuración, mostrando información adicional útil para solucionar problemas.

4. What is a command line flag that can be used to enable verbose output?

**--debug**








