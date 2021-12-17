---
title: Azure Stack Edge Pro GPU での Kubernetes ネットワーク
description: Azure Stack Edge Pro GPU デバイスでの Kubernetes ネットワークの動作について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: alkohli
ms.openlocfilehash: a969486194f66be5c7ce4c3973352ca036a025f8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093119"
---
# <a name="kubernetes-networking-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの Kubernetes ネットワーク

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスでは、コンピューティング ロールを構成するときに Kubernetes クラスターが作成されます。 Kubernetes クラスターが作成されると、コンテナー化されたアプリケーションを Kubernetes クラスターのポッドにデプロイできます。 ご利用の Kubernetes クラスター内のポッドに対しては、さまざまな方法でネットワークを使用できます。 

この記事では、Kubernetes クラスターでのネットワークについて概要を示し、Azure Stack Edge Pro GPU デバイスのコンテキストで具体的に説明します。 

## <a name="networking-requirements"></a>ネットワーク要件

Kubernetes クラスターにデプロイされる一般的な 2 層アプリの例を次に示します。

- アプリは、Web サーバー フロント エンドと、バックエンドのデータベース アプリケーションを備えています。 
- すべてのポッドに IP が割り当てられますが、これらの IP はポッドの再起動およびフェールオーバー時に変更される可能性があります。 
- 各アプリは複数のポッドで構成されており、すべてのポッド レプリカ間でトラフィックの負荷分散が行われる必要があります。 

![Kubernetes のネットワーク要件](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1-m.png)

上記のシナリオでは、次のようなネットワーク要件が発生します。

 - 外部に接続するアプリケーションには、Kubernetes クラスターの外部にいるアプリケーション ユーザーが名前または IP アドレスを使用してアクセスする必要があります。 
 - Kubernetes クラスター内のアプリケーション (たとえば、ここではフロント エンドとバックエンド ポッド) は相互に通信できる必要があります。

上記のニーズを両方とも解決できる Kubernetes サービスを紹介します。 


## <a name="networking-services"></a>ネットワーク サービス

Kubernetes サービスには次の 2 種類があります。 

- **クラスター IP サービス** - このサービスは、アプリケーション ポッドに一定のエンドポイントを提供するものと考えてください。 これらのサービスに関連付けられているポッドには、Kubernetes クラスターの外部からアクセスすることはできません。 これらのサービスで使用される IP アドレスは、プライベート ネットワークのアドレス空間から取得されます。 
    
    外部に公開されているロード バランサー サービスとしてではなく、他のポッドとしてアクセスできるように Kubernetes クラスター内のポッドを公開するには、「[Kubernetes サービスを内部通信用のクラスター IP サービスとして公開する]()」方法を参照してください。

- **ロード バランサー IP** - クラスター IP サービスに似ています。ただし、関連する IP は外部ネットワークからのものであり、Kubernetes クラスターの外部からアクセスできます。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->

## <a name="kubernetes-network-configuration"></a>Kubernetes ネットワークの構成

Kubernetes ノードと外部サービスに使用される IP アドレスは、デバイスのローカル UI の **[コンピューティング]** ページで提供されます。

![ローカル UI での Kubernetes IP の割り当て](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 割り当ての対象は次のとおりです。

- **Kubernetes ノード IP**: この IP 範囲は、Kubernetes のマスターおよびワーカー ノードに使用されます。 これらの IP は、Kubernetes ノードが相互に通信するときに使用されます。
- **Kubernetes 外部サービス IP**: この IP 範囲は、Kubernetes クラスターの外部で公開されている外部サービス (ロード バランサー サービスとも呼ばれます) に使用されます。

## <a name="kubernetes-networking-components"></a>Kubernetes ネットワーク コンポーネント

Calico、Metallb、および Core DNS はすべて、Azure Stack Edge Pro GPU 上のネットワーク用にインストールされるコンポーネントです。 

- **Calico** では、プライベート IP 範囲からの IP アドレスがすべてのポッドに割り当てられ、これらのポッドに合わせてネットワークが構成されます。これにより、あるノードのポッドが別のノードのポッドと通信できるようになります。 
- **Metallb** はクラスター内ポッドで実行され、`load balancer` 型のサービスに IP アドレスを割り当てます。 ロード バランサーの IP アドレスは、ローカル UI を介して提供されるサービス IP の範囲から選択されます。 
- **コア DNS** - このアドオンでは、サービス名をクラスター IP アドレスにマッピングする DNS レコードが構成されます。

デバイスの PowerShell インターフェイスに接続すると、上記のネットワーク コンポーネントが Kubernetes クラスターで実行されていることを確認できます。

## <a name="network-interfaces-switches"></a>ネットワーク インターフェイス、スイッチ 

デバイスは、インフラストラクチャ クラスターを構成する 1 ノード構成として使用できます。 Kubernetes クラスターはインフラストラクチャ クラスターから分離され、インフラストラクチャ クラスターの上にデプロイされます。 Kubernetes クラスターには、マスター ノードとワーカー ノードがあります。 これらの Kubernetes ノードはいずれも、アプリケーションとクラウド ワークフローを実行する仮想マシンです。

マスター VM とワーカー VM には、それぞれ 2 つのネットワーク インターフェイスがあります。1 つは内部仮想スイッチに接続し、もう 1 つは外部仮想スイッチに接続します。 

- **外部仮想スイッチ**: このスイッチは、ローカル UI の **[コンピューティング]** ページで、コンピューティング用のデバイス ポートを有効にしたときに作成されます。 これはコンピューティング インフラストラクチャに使用するスイッチです。このスイッチはたとえば、デバイスにデプロイする仮想マシンに使用します。 
- **内部仮想スイッチ**: このスイッチは、デバイスの工場出荷時の既定の設定の一部として作成されます。 内部仮想スイッチは、ネットワーク アドレス変換 (NAT) を使用して、既定のゲートウェイで構成されているポート経由でトラフィックをルーティングします。 このスイッチはたとえば、VM からのすべての IoT ランタイム要求を Azure portal にルーティングします。 

   <!--![Azure Stack Edge networking simplified diagram](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-0.png)-->

## <a name="network-routes"></a>ネットワーク ルート 

デバイス上の Kubernetes VM では、新しいルート構成を追加することでトラフィックをルーティングできます。 ルート構成は、次のフィールドを含むルーティング テーブル エントリです。

| パラメーター     | 説明                                                                              |
|---------------|------------------------------------------------------------------------------------------|
| 宛先   | IP アドレスまたは IP アドレス プレフィックス。                                            |
| プレフィックス長 | 宛先のアドレスまたはアドレスの範囲に対応するプレフィックス長。 |
| 次ホップ      | パケットの転送先の IP アドレス。                                         |
| インターフェイス     | IP パケットを転送するネットワーク インターフェイス。                                       |
| メトリック        | ルーティング メトリックにより、宛先に到達するために使用される優先ネットワーク インターフェイスが決定されます。 |


## <a name="change-routing-on-compute-network"></a>コンピューティング ネットワーク上のルーティングの変更

Kubernetes のワーカー VM とマスター VM のルーティングを変更するには、`Add-HcsNetRoute` コマンドレットを使用します。 下の図のレイアウトについて考えてみましょう。 

![Azure Stack Edge のネットワーク図](./media/azure-stack-edge-gpu-kubernetes-networking/azure-stack-edge-networking-1.png)

- ポート 2 はインターネットに接続されており、送信トラフィックに使用するパスです。 
- ポート 3 でコンピューティングを有効にし、それにより、このネットワーク インターフェイス上に外部仮想スイッチが作成されました。 
- ポート 3 は、プライベート ネットワークに接続されています。このネットワークには、生データを処理するために Azure Stack Edge デバイスにフィードしているカメラと他のセンサーが含まれています。 


プライベート ネットワークの環境内にゲートウェイが構成されている場合は、Kubernetes のマスター VM およびワーカー VM 用のカスタム ルートを設定して、これらが関連するトラフィックについてのみゲートウェイと通信できるようにすることを検討してください。 そうすることで、Azure Stack Edge デバイス上で構成している他のポートに対して、このコンピューティング ネットワーク上を流れるトラフィックを制御できるようになります。 たとえば、インターネットに接続する他のすべてのトラフィックが、デバイス上の他の物理ポートを通過するようにしたい場合があります。 その場合、インターネットに接続するトラフィックはポート 2 を通過することができます。 

また、次のような点も考慮する必要があります。

- フラットなサブネットがある場合は、これらのルートをプライベート ネットワークに追加する必要はありません。 プライベート ネットワークに複数のサブネットが存在する場合は、これらのルートを (必要に応じて) 追加できます。
- これらのルートは、Kubernetes のマスター VM およびワーカー VM にのみ追加でき、デバイス (Windows ホスト) には追加できません。
- このルートを追加する前に、Kubernetes コンピューティングを構成する必要はありません。 Kubernetes コンピューティングが構成された後でも、ルートを追加または更新できます。 
- 新しいルート構成は、ルート UI ではなく、デバイスの PowerShell インターフェイスを介してのみ追加できます。
- 使用するネットワーク インターフェイスに静的構成があることを確認します。

## <a name="add-a-route-configuration"></a>ルート構成の追加

新しいカスタム ルートをプライベート ネットワークに追加するには、次のようにコマンドレットを使用します。

```powershell
Add-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP address or IP address prefix> -NextHop <IP address of next hop> -RouteMetric <Route metric number> 
```
出力例を次に示します。

```output
Add-HcsNetRoute -InterfaceAlias "Port3" -DestinationPrefix "192.168.20.0/24" -NextHop "192.168.20.1" -RouteMetric 100 
```

上のコマンドにより、宛先サブネット 192.168.20.0/24 を定義し、ネクスト ホップを 192.168.20.1 と指定し、このルーティング エントリにルーティング メトリック 100 を割り当てるエントリがルーティング テーブル内に作成されます。 ルーティング メトリックが小さいほど、ルートに割り当てられる優先順位は高くなります。

## <a name="check-route-configuration"></a>ルート構成の確認

デバイスに追加したすべてのカスタム ルート構成を確認するには、次のコマンドレットを使用します。 これらのルートには、すべてのシステム ルート、およびデバイス上に既に存在する既定のルートは含まれません。

```powershell
Get-HcsNetRoute -InterfaceAlias <Port number>
```

## <a name="remove-a-route-configuration"></a>ルート構成の削除

デバイスに追加したルート構成を削除するには、次のコマンドレットを使用します。

```powershell
Remove-HcsNetRoute -InterfaceAlias <Port number> -DestinationPrefix <Destination IP or IP prefix>
```

## <a name="routing-with-multiple-network-interfaces"></a>複数のネットワーク インターフェイスを含むルーティング

デバイスの複数のポートが接続されている場合、Hyper-V 環境内の複数の物理ネットワーク アダプターを 1 つの仮想ネットワーク アダプターにグループ化できる標準 NIC チーミングまたはスイッチ埋め込みチーミング (SET) はサポートされません。


## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro GPU 上で Kubernetes ネットワークを構成するには、以下を参照してください。

- [Azure Stack Edge Pro GPU で IoT Edge を介してステートレス アプリケーションを外部に公開する](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- [Azure Stack Edge Pro GPU で kuebctl を介してステートレス アプリケーションを外部に公開する](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。
