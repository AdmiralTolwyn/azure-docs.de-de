---
title: Verwenden Ihrer eigenen TLS-Zertifikate für eingehende Daten mit AKS-Clustern (Azure Kubernetes Service)
description: Erfahren Sie, wie Sie einen NGINX-Eingangscontroller, der Ihre eigenen Zertifikate verwendet, in einem AKS-Cluster (Azure Kubernetes Service) konfigurieren.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/23/2018
ms.author: iainfou
ms.openlocfilehash: 4e3f2f33cfffeacbcbeccc4f17f55b7d0e1a985c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50128941"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Erstellen eines HTTPS-Eingangscontrollers und Verwenden Ihrer eigenen TLS-Zertifikate in Azure Kubernetes Service (AKS)

Ein Eingangscontroller ist eine Softwarekomponente, die einen Reverseproxy, konfigurierbare Datenverkehrsweiterleitung und TLS-Terminierung für Kubernetes-Dienste bereitstellt. Mithilfe von Ressourcen für eingehende Kubernetes-Daten werden Eingangsregeln und Routen für einzelne Kubernetes-Dienste konfiguriert. Durch die Verwendung von einem Eingangscontroller und Eingangsregeln kann eine einzelne IP-Adresse zum Weiterleiten von Datenverkehr an mehrere Dienste in einem Kubernetes-Cluster verwendet werden.

Dieser Artikel beschreibt, wie Sie den [NGINX-Eingangscontroller][nginx-ingress] in einem Azure Kubernetes Service-Cluster (AKS) bereitstellen. Sie generieren Ihre eigenen Zertifikate und erstellen ein Kubernetes-Geheimnis zur Verwendung mit der Eingangsroute. Schließlich werden zwei Anwendungen im AKS-Cluster ausgeführt, die jeweils über eine einzelne IP-Adresse zugänglich sind.

Weitere Funktionen:

- [Erstellen eines einfachen Eingangscontrollers mit Verbindung mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- Erstellen eines Eingangscontrollers, der Let's Encrypt für das automatische Generieren von TLS-Zertifikaten [mit einer dynamischen öffentlichen IP-Adresse][aks-ingress-tls] oder [mit einer statischen öffentlichen IP-Adresse][aks-ingress-static-tls] verwendet

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird Helm für die Installation des NGINX-Eingangscontrollers und einer Beispiel-Web-App verwendet. Helm muss im AKS-Cluster initialisiert sein und ein Dienstkonto für Tiller verwenden. Stellen Sie sicher, dass Sie das neueste Release von Helm verwenden. Anweisungen zum Upgrade finden Sie in der [Helm-Installationsdokumentation][helm-install]. Weitere Informationen zum Konfigurieren und Verwenden von Helm finden Sie unter [Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS)][use-helm].

Für den Artikel müssen Sie mindestens Version 2.0.41 der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli-install] Informationen dazu.

## <a name="create-an-ingress-controller"></a>Erstellen eines Eingangscontrollers

Verwenden Sie zum Erstellen des Eingangscontrollers `Helm` zum Installieren von *nginx-ingress*. Für zusätzliche Redundanz werden zwei Replikate der NGINX-Eingangscontroller mit dem Parameter `--set controller.replicaCount` bereitgestellt. Um vollständig von der Ausführung von Replikaten des Eingangscontrollers zu profitieren, stellen Sie sicher, dass sich mehr als ein Knoten im AKS-Cluster befindet.

> [!TIP]
> Im folgenden Beispiel wird der Eingangscontroller im `kube-system`-Namespace installiert. Sie können bei Bedarf einen anderen Namespace für Ihre eigene Umgebung angeben. Wenn in Ihrem AKS-Cluster RBAC nicht aktiviert ist, fügen Sie den Befehlen `--set rbac.create=false` hinzu.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Während der Installation wird eine öffentliche Azure-IP-Adresse für den Eingangscontroller erstellt. Diese öffentliche IP-Adresse ist für die Lebensdauer des Eingangscontrollers statisch. Wenn Sie den Eingangscontroller löschen, geht die Zuweisung der öffentlichen IP-Adresse verloren. Wenn Sie dann einen weiteren Eingangscontroller erstellen, wird eine neue öffentliche IP-Adresse zugewiesen. Wenn Sie die Verwendung der öffentlichen IP-Adresse beibehalten möchten, können Sie stattdessen [einen Eingangscontroller mit einer statischen öffentlichen IP-Adresse erstellen][aks-ingress-static-tls].

Sie rufen die öffentliche IP-Adresse mit dem Befehl `kubectl get service` ab. Es dauert möglicherweise einige Minuten, bis die IP-Adresse dem Dienst zugewiesen wird.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Notieren Sie sich diese öffentliche IP-Adresse, da sie im letzten Schritt verwendet wird, um die Bereitstellung zu testen.

Es wurden noch keine Eingangsregeln erstellt. Wenn Sie zu der öffentlichen IP-Adresse navigieren, wird die Standard-404-Seite des NGINX-Eingangscontrollers angezeigt.

## <a name="generate-tls-certificates"></a>Generieren von TLS-Zertifikaten

In diesem Artikel generieren wir ein selbstsigniertes Zertifikat mit `openssl`. Für die Produktion sollten Sie ein vertrauenswürdiges, signiertes Zertifikat über einen Anbieter oder Ihre eigene Zertifizierungsstelle anfordern. Im nächsten Schritt generieren Sie ein Kubernetes-*Geheimnis* unter Verwendung des TLS-Zertifikats und des privaten Schlüssels, der von OpenSSL generiert wurde.

Im folgenden Beispiel wird ein RSA X509-Zertifikat (2048 Bit) namens *aks-ingress-tls.crt* generiert, das für 365 Tage gültig ist. Die private Schlüsseldatei heißt *aks-ingress-tls.key*. Für ein Kubernetes-TLS-Geheimnis sind beide Dateien erforderlich.

Dieser Artikel verwendet den allgemeinen Antragstellernamen *demo.azure.com*, der nicht geändert werden muss. Geben Sie für die Produktion Ihre eigenen Organisationswerte für den `-subj`-Parameter an:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Erstellen eines Kubernetes-Geheimnisses für das TLS-Zertifikat

Damit Kubernetes das TLS-Zertifikat und den privaten Schlüssel für den Eingangscontroller verwenden kann, erstellen und verwenden Sie ein Geheimnis. Das Geheimnis wird einmal definiert und verwendet das Zertifikat und die Schlüsseldatei, das bzw. die im vorherigen Schritt erstellt wurde. Auf dieses Geheimnis verweisen Sie, wenn Sie Eingangsrouten definieren.

Im folgenden Beispiel wird ein Geheimnis namens *aks-ingress-tls* erstellt:

```console
kubectl create secret tls aks-ingress-tls \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Ausführen von Demoanwendungen

Ein Eingangscontroller und ein Geheimnis für Ihr Zertifikat wurden konfiguriert. Nun führen wir zwei Demoanwendungen im AKS-Cluster-aus. In diesem Beispiel wird Helm verwendet, um mehrere Instanzen einer einfachen Hallo-Welt-Anwendung auszuführen.

Bevor Sie Helm-Beispieldiagramme installieren können, fügen Sie das Azure-Beispielrepository wie folgt Ihrer Helm-Umgebung hinzu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Erstellen Sie die erste Demoanwendung aus einem Helm-Diagramm mit dem folgenden Befehl:

```console
helm install azure-samples/aks-helloworld
```

Installieren Sie nun eine zweite Instanz der Demoanwendung. Geben Sie für die zweite Instanz einen neuen Titel an, sodass sich die beiden Anwendungen visuell unterscheiden. Sie geben auch einen eindeutigen Dienstnamen an:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Erstellen einer Eingangsroute

Beide Anwendungen werden nun in Ihrem Kubernetes-Cluster ausgeführt, wurden jedoch mit einem Dienst vom Typ `ClusterIP` konfiguriert. Daher kann nicht über das Internet auf die Anwendungen zugegriffen werden. Um sie öffentlich verfügbar zu machen, erstellen Sie eine Kubernetes-Eingangsressource. Die Eingangsressource konfiguriert Regeln, die den Datenverkehr an eine der beiden Anwendungen weiterleiten.

Im folgenden Beispiel wird der Datenverkehr an die Adresse `https://demo.azure.com/` an den Dienst mit dem Namen `aks-helloworld` weitergeleitet. Datenverkehr an die Adresse `https://demo.azure.com/hello-world-two` wird an den Dienst `ingress-demo` weitergeleitet. Für diesen Artikel müssen Sie diese Demohostnamen nicht ändern. In der Produktion geben Sie die Namen an, die im Rahmen der Zertifikatanforderung und des Generierungsvorgangs angegeben wurden.

> [!TIP]
> Wenn der beim Zertifikatanforderungsvorgang angegebene Hostname (CN-Name) nicht dem Host entspricht, der in Ihrer Eingangsroute definiert wurde, zeigt der Eingangscontroller die Meldung *Kubernetes Ingress Controller Fake Certificate* an. Stellen Sie sicher, dass der Name Ihres Zertifikats mit dem des Eingangsroutenhosts übereinstimmt.

Im Abschnitt *tls* wird die Eingangsroute dazu aufgefordert, das Geheimnis namens *aks-ingress-tls* für den Host *demo.azure.com* zu verwenden. Geben Sie in Ihrer Produktionsumgebung Ihre eigene Hostadresse an.

Erstellen Sie eine Datei mit dem Namen `hello-world-ingress.yaml`, und fügen Sie den folgenden YAML-Beispielcode ein.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Erstellen Sie die Eingangsressource mit dem Befehl `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testen der Konfiguration für eingehende Daten

Zum Testen der Zertifikate mit unserem Fakehost *demo.azure.com* verwenden Sie `curl` und geben den Parameter *--resolve* an. Mit diesem Parameter können Sie den Namen *demo.azure.com* der öffentlichen IP-Adresse des Eingangscontrollers zuordnen. Geben Sie die öffentliche IP-Adresse Ihres eigenen Eingangscontrollers an, wie im folgenden Beispiel gezeigt:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Die Adresse wurde ohne zusätzlichen Pfad angegeben, sodass der Eingangscontroller standardmäßig die Route */* verwendet. Die erste Demoanwendung wird zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Durch den Parameter *-v* in unserem `curl`-Befehl werden ausführliche Informationen ausgegeben, einschließlich des erhaltenen TLS-Zertifikats. Während der CURL-Ausgabe können Sie überprüfen, ob Ihr eigenes TLS-Zertifikat verwendet wurde. Durch den Parameter *-k* wird die Seite auch dann weiterhin geladen, wenn wir ein selbstsigniertes Zertifikat verwenden. Das folgende Beispiel zeigt, dass das Zertifikat *issuer: CN=demo.azure.com; O=aks-ingress-tls* verwendet wurde:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Fügen Sie nun den Pfad */hello-world-two* der Adresse hinzu, z.B. *https://demo.azure.com/hello-world-two*. Die zweite Demoanwendung wird mit dem benutzerdefinierten Titel zurückgegeben, wie in der folgenden verkürzten Beispielausgabe gezeigt:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel wird Helm verwendet, um die Eingangskomponenten und die Beispiel-Apps zu installieren. Wenn Sie ein Helm-Diagramm bereitstellen, werden eine Reihe von Kubernetes-Ressourcen erstellt. Zu diesen Ressourcen gehören Pods, Bereitstellungen und Dienste. Um sie zu bereinigen, listen Sie zuerst mit dem Befehl `helm list` die Helm-Versionen auf. Suchen Sie nach Diagrammen mit den Namen *nginx-ingress* und *aks-helloworld*, wie in der folgenden Beispielausgabe gezeigt:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Löschen Sie die Versionen mit dem Befehl `helm delete`. Im folgenden Beispiel werden die NGINX-Eingangsbereitstellung und die beiden Hallo-Welt-AKS-Beispiel-Apps gelöscht.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Entfernen Sie als Nächstes das Helm-Repository für die Hallo-Welt-AKS-App:

```console
helm repo remove azure-samples
```

Entfernen Sie die Eingangsroute, die Datenverkehr an die Beispiel-Apps weitergeleitet hat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Entfernen Sie abschließend das Zertifikatgeheimnis:

```console
kubectl delete secret aks-ingress-tls
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden einige externe Komponenten in AKS berücksichtigt. Weitere Informationen zu diesen Komponenten finden Sie auf den folgenden Projektseiten:

- [Helm CLI][helm-cli]
- [NGINX-Eingangscontroller][nginx-ingress]

Weitere Funktionen:

- [Erstellen eines einfachen Eingangscontrollers mit Verbindung mit einem externen Netzwerk][aks-ingress-basic]
- [Aktivieren des Add-Ons für das HTTP-Anwendungsrouting][aks-http-app-routing]
- [Erstellen eines Eingangscontrollers, der ein internes, privates Netzwerk und eine IP-Adresse verwendet][aks-ingress-internal]
- Erstellen eines Eingangscontrollers, der Let's Encrypt für das automatische Generieren von TLS-Zertifikaten [mit einer dynamischen öffentlichen IP-Adresse][aks-ingress-tls] oder [mit einer statischen öffentlichen IP-Adresse][aks-ingress-static-tls] verwendet

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
