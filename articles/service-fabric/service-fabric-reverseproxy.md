<properties
   pageTitle="Service Fabric リバース プロキシ | Microsoft Azure"
   description="Service Fabric のリバース プロキシを使用して、クラスターの内外からマイクロサービスとの通信を行います。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman,vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="vturecek"/>

# Service Fabric リバース プロキシ

Service Fabric リバース プロキシは、Service Fabric に組み込まれたリバース プロキシであり、Service Fabric クラスター内の、HTTP エンドポイントを公開するマイクロサービスのアドレス指定を可能にします。

## マイクロサービス通信モデル

通常、Service Fabric のマイクロサービスは、クラスター内の VM のサブセットで実行され、さまざまな理由から VM 間で移動することができます。そのため、マイクロサービスのエンドポイントが動的に変更される可能性があります。マイクロサービスと通信するための標準的なパターンは、次の解決ループです。

1. Naming Service によって最初にサービスの場所を解決します。
2. サービスに接続します。
3. 接続エラーの原因を特定し、必要に応じてサービスの場所を再解決します。

一般に、このプロセスでは、サービス解決および再試行ポリシーを実装する再試行ループにクライアント側の通信ライブラリをラップする必要があります。このトピックの詳細については、[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)に関する記事をご覧ください。

### SF リバース プロキシ経由での通信
Service Fabric リバース プロキシは、クラスターのすべてのノードで実行されます。リバース プロキシはクライアントに代わってサービス解決プロセス全体を実行し、クライアント要求を転送します。そのため、クラスターで実行されているクライアントは、クライアント側の HTTP 通信ライブラリを使用するだけで、同じノードでローカルで実行されている SF リバース プロキシ経由でターゲット サービスと通信できます。

![内部通信][1]

## クラスターの外部からのマイクロサービスへの到達
マイクロサービスの既定の外部通信モデルは**オプトイン**です。既定では、外部クライアントから各サービスに直接アクセスすることはできません。[Azure Load Balancer](../load-balancer/load-balancer-overview.md) は、マイクロサービスと外部クライアントの間のネットワーク境界であり、ネットワーク アドレス変換を実行して、外部要求を内部の **IP:port** エンドポイントに転送します。マイクロサービスのエンドポイントが外部クライアントに直接アクセスできるようにするには、クラスター内のサービスで使用される各ポートにトラフィックを転送するように Azure Load Balancer を構成しておく必要があります。さらに、ほとんどのマイクロサービス (特にステートフル マイクロサービス) はクラスターのすべてのノードに存在するわけではなく、フェールオーバーでノード間を移動する可能性があります。そのため、このような場合、Azure Load Balancer はトラフィックの転送先となるレプリカのターゲット ノードの場所を効率的に特定することができません。

### クラスター外部からの SF リバース プロキシ経由でのマイクロサービスへの到達

Azure Load Balancer で個々のサービスのポートを構成するのではなく、SF リバース プロキシ ポートだけを構成することができます。これにより、クラスターの外部のクライアントは、追加構成なしにリバース プロキシ経由でクラスター内のサービスに到達することができます。

![外部通信][0]

>[AZURE.WARNING] ロード バランサーでリバース プロキシのポートを構成すると、クラスターの外部から、クラスター内の http エンドポイントを公開するすべてのマイクロサービスのアドレス指定が可能になります。


## リバース プロキシ経由でサービスのアドレス指定を行うための URI 形式

リバース プロキシでは、次の URI 形式を使用して、受信要求の転送先となるサービス パーティションを特定します。

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http (s):** HTTP または HTTPS トラフィックを受け入れるようにリバース プロキシを構成できます。HTTPS トラフィックが発生した場合、リバース プロキシで SSL 終了が発生します。リバース プロキシによってクラスター内のサービスに転送される要求は http 経由になります。
 - **Cluster FQDN| internal IP:** For external clients, the reverse proxy can be configured so that it is reachable through the cluster domain (e.g., mycluster.eastus.cloudapp.azure.com). By default the reverse proxy runs on every node, so for internal traffic it can be reached on localhost or on any internal node IP (e.g., 10.0.0.1).
 - **Port:** リバース プロキシに指定されているポート (例: 19008)。
 - **ServiceInstanceName:** "fabric:/" スキームなしでアクセスしようとしているサービスのデプロイ済み完全修飾サービス インスタンス名です。たとえば、*fabric:/myapp/myservice/* サービスにアクセスするには、*myapp/myservice* を使用します。
 - **Suffix path:** 接続先となるサービスの実際の URL パスです (例: *myapi/values/add/3*)。
 - **PartitionKey:** パーティション分割されたサービスの場合、アクセスするパーティションの計算済みのパーティション キーです。これはパーティション ID の GUID ではありません。シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
 - **PartitionKind:** サービス パーティション構成です。これには、"Int64Range" または "Named" を指定できます。シングルトン パーティション構成を使用するサービスでは、このパラメーターは不要です。
 - **Timeout:** クライアント要求の代わりに、リバース プロキシによって作成される、サービスに対する http 要求のタイムアウトを指定します。このパラメーターの既定値は 60 秒です。これは省略可能なパラメーターです。

### 使用例

次の URL で HTTP リスナーを開く **fabric:/MyApp/MyService** サービスを例にとってみましょう。

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

リソースは次のとおりです。

 - `/index.html`
 - `/api/users/<userId>`

サービスがシングルトン パーティション構成を使用している場合、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターは不要であり、次のようにゲートウェイ経由でサービスにアクセスできます。

 - 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - 内部: `http://localhost:19008/MyApp/MyService`

サービスが Uniform Int64 パーティション構成を使用している場合、サービスのパーティションにアクセスするには、*PartitionKey* および *PartitionKind* クエリ文字列パラメーターを使用する必要があります。

 - 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - 内部: `http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

サービスによって公開されるリソースに到達するには、URL のサービス名の後にリソース パスを配置します。

 - 外部: `http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - 内部: `http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

ゲートウェイは、サービスの URL にこれらの要求を転送します。

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## ポートを共有するサービスの特別な処理

サービスに到達できない場合、Application Gateway はサービス アドレスの再解決を試み、要求を再試行します。クライアント コードで独自のサービス解決と解決ループを実装する必要がないため、これはゲートウェイの主な利点の 1 つです。

一般に、サービスに到達できないときは、サービス インスタンスまたはレプリカが通常のライフサイクルの一環として別のノードに移動していることを意味します。この場合、ゲートウェイは、エンドポイントが解決された元のアドレスではもう開いていないことを示すネットワーク接続エラーを受信する可能性があります。

ただし、レプリカまたはサービス インスタンスはホスト プロセスを共有することができます。また、次のような http.sys ベースの Web サーバーによってホストされているときには、ポートを共有している場合もあります。

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

この状況では、Web サーバーはホスト プロセスで要求に応答することができますが、解決されたサービス インスタンスまたはレプリカがホストで使用できなくなっている可能性があります。この場合、ゲートウェイは Web サーバーから HTTP 404 応答を受信します。結果的に、HTTP 404 は次の 2 つの異なる意味を持つことになります。

 1. サービス アドレスは正しいが、ユーザーから要求されたリソースが存在しない。
 2. サービス アドレスが間違っており、ユーザーから要求されたリソースが、実際には別のノードに存在する可能性がある。

最初のケースでは、これはユーザー エラーと見なされる通常の HTTP 404 です。ただし、2 番目のケースでは、ユーザーは存在するリソースを要求しましたが、サービス自体が移動しているため、ゲートウェイがリソースを見つけることができませんでした。この場合、ゲートウェイはアドレスを再解決し、再試行する必要があります。

そのため、ゲートウェイにはこの2つのケースを区別する手段が必要です。この区別を行うには、サーバーからのヒントが必要になります。

 - 既定では、Application Gateway はケース 2 を想定し、アドレスを再解決して要求の再発行を試みます。
 - Application Gateway にケース 1 であることを示すには、サービスが次の HTTP 応答ヘッダーを返す必要があります。

`X-ServiceFabric : ResourceNotFound`

この HTTP 応答ヘッダーは、要求されたリソースが存在しないという通常の HTTP 404 の状況を示すので、ゲートウェイはサービス アドレスの再解決を試みなくなります。

## セットアップと構成
[Azure Resource Manager テンプレート](./service-fabric-cluster-creation-via-arm.md)によって、クラスターの Service Fabric リバース プロキシを有効にすることができます。

(サンプル テンプレートを使用するか、カスタムの Resource Manager テンプレートを作成して) デプロイするクラスター用テンプレートを用意したら、次の手順に従って、テンプレートでリバース プロキシを有効にすることができます。

1. テンプレートの [Parameters セクション](../resource-group-authoring-templates.md)で、リバース プロキシのポートを定義します。

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. **クラスター**の [resources の type](../resource-group-authoring-templates.md) セクションで、nodeType オブジェクトごとにポートを指定します。

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Azure クラスターの外部からリバース プロキシのアドレス指定を行うには、手順 1. で指定したポートの **Azure Load Balancer 規則**を設定します。

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. リバース プロキシのポートで SSL 証明書を構成するには、**クラスター**の [resources の type セクション](../resource-group-authoring-templates.md)で httpApplicationGatewayCertificate プロパティに証明書を追加します。

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## 次のステップ
 - [GitHub のサンプル プロジェクト](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount)で、サービス間の HTTP 通信の例を確認します。

 - [Reliable Services のリモート処理によるリモート プロシージャ コール](service-fabric-reliable-services-communication-remoting.md)

 - [Reliable Services の OWIN を使用する Web API](service-fabric-reliable-services-communication-webapi.md)

 - [Reliable Services を使用した WCF 通信](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png

<!---HONumber=AcomDC_0921_2016-->