---
title: Azure Container Service (AKS) の HTTP アプリケーション ルーティング アドオン
description: Azure Container Service (AKS) の HTTP アプリケーション ルーティング アドオンを使用する
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 6e5a81742b4a6b21e5cfa28d8e772430f8ae30ba
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067505"
---
# <a name="http-application-routing"></a>HTTP アプリケーション ルーティング

HTTP アプリケーション ルーティング ソリューションを使用すると、AKS クラスターに展開されたアプリケーションに簡単にアクセスできます。 アドオンを有効に設定すると、ソリューションによって AKS クラスター内のイングレス コントローラーが構成されます。 また、アプリケーションが配置されると、ソリューションによってアプリケーション エンドポイントに公開アクセスできる DNS 名が作成されます。

このアドオンを有効にすると、サブスクリプション内に DNS ゾーンが作成されます。 DNS のコストの詳細については、「[Azure DNS の価格][dns-pricing]」を参照してください。

## <a name="http-routing-solution-overview"></a>HTTP のルーティング ソリューションの概要

アドオンによって、 [Kubernetes イングレス コントローラー][ingress] と[外部 DNS][external-dns]コントローラーの 2 つのコンポーネントが展開されます。

- **イングレス コントローラー** - イングレス コントローラーは、ロード バランサー タイプの Kubernetes サービスを使用してインターネットに公開されます。 イングレス コントローラーは、アプリケーションのエンドポイントへのルートを作成する [Kubernetes イングレス リソース][ingress-resource]を監視し、実装します。
- **外部 DNS コントローラー** - Kubernetes イングレス リソースを監視し、クラスター固有の DNS ゾーンに DNS A レコードを作成します。

## <a name="deploy-http-routing"></a>HTTP ルーティングを展開する

HTTP アプリケーション ルーティング アドオンは、AKS クラスターを展開するときに、Azure Portal から有効にできます。

![HTTP ルーティング機能を有効化する](media/http-routing/create.png)

クラスターが展開されたら、自動的に作成された AKS リソース グループに移動して、DNS ゾーンを選択します。 DNS ゾーンの名前を書き留めます。 この名前は、アプリケーションを AKS クラスターを展開するときに必要になります。

![DNS ゾーン名を取得する](media/http-routing/dns.png)

## <a name="use-http-routing"></a>HTTP ルーティングを使用する

HTTP アプリケーション ルーティング ソリューションは、次のように記述されたイングレス リソースに対してのみトリガーできます。

```
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

`samples-http-application-routing.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。 43 行めの `<CLUSTER_SPECIFIC_DNS_ZONE>` を、このドキュメントの最後の手順で収集した DNS ゾーン名に更新します。


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

[kubectl apply][kubectl-apply] コマンドを使用してリソースを作成します。

```
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

cURL またはブラウザーを使用して、`samples-http-application-routing.yaml` ファイルのホスト セクションに指定されたホスト名に移動します。 アプリケーションにインターネットからアクセスできるようになるまで、最大で 1 分間にかかる場合があります。

```
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="troubleshooting"></a>トラブルシューティング

[kubectl logs][kubectl-logs] コマンドを使用して、外部 DNS アプリケーションのアプリケーション ログを表示します。 A および TXT DNS レコードが正常に作成されたことをログで確認してください。

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

これらのレコードは、Azure Portal の DNS ゾーン リソースにも表示されます。

![DNS レコードを取得する](media/http-routing/clippy.png)

[kubectl logs][kubectl-logs] コマンドを使用して、Nginx イングレス コントローラーのアプリケーション ログを表示します。 イングレス リソースが作成され、コントローラーが再読み込みされたことをログで確認してください。 すべての HTTP アクティビティがログに記録されます。

```
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="cleanup"></a>クリーンアップ

この手順で作成された関連する Kubernetes オブジェクトを削除します。

```
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>次の手順

セキュリティ保護された HTTPS イングレス コントローラーを ASK にインストールする方法については、「[Azure Container Service (AKS) での HTTPS イングレス][ingress-https]」を参照してください。

<!-- LINKS - internal -->
[ingress-https]: ./ingress.md

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource