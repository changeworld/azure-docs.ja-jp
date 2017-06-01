---
title: "Azure Service Fabric の DNS サービス | Microsoft Docs"
description: "Service Fabric の DNS サービスを使用して、クラスター内からマイクロサービスを検出します。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e0f6a3a91207b73320d60a498d635262ef730d89
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric の DNS サービス
オプションのシステム サービスである DNS サービスをクラスターで有効にし、DNS プロトコルを使用して他のサービスを検出できます。

コンテナー化されたサービスなど多くのサービスには既存の URL 名が含まれるため、標準の DNS プロトコル (Naming Service プロトコルではなく) を使用してこれらを解決できます。これは特にアプリケーションの移行 (リフト アンド シフト) シナリオで便利です。 DNS サービスを使用すると、DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。 

次の図に示すように、Service Fabric クラスターで実行されている DNS サービスが DNS 名をサービス名にマップすると、Naming Service によって解決されて、接続するエンドポイントのアドレスが返されます。 サービスの DNS 名は、作成時に提供されます。 

![service endpoints][0]

## <a name="enabling-the-dns-service"></a>DNS サービスを有効にする
最初に、DNS サービスをクラスターで有効にする必要があります。 デプロイするクラスター用テンプレートを用意します。 [サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用することも、Resource Manager テンプレートを作成することもできます。 次の手順で DNS サービスを有効にできます。

1. まず、次のスニペットに示すように、`Microsoft.ServiceFabric/clusters` リソースに対して `apiversion` が `2017-07-01-preview` に設定されていることを確認します。 違う場合は、`apiVersion` を `2017-07-01-preview` の値に更新する必要があります。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 次に示すように、`addonFeatures` セクションを `fabricSettings` セクションの後に追加して、DNS サービスを有効にします。

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. これらの変更によりクラスター テンプレートを更新したら、変更を適用して更新を完了します。 完了すると、DNS システム サービスがクラスターで実行されます。このサービスの名前は Service Fabric Explorer のシステム サービス セクションに `fabric:/System/DnsService` と表示されます。 

## <a name="setting-the-dns-name-for-your-service"></a>サービスの DNS 名を設定する
DNS サービスがクラスターで実行されるようになったため、`ApplicationManifest.xml` で既定のサービスに対する宣言によって、または Powershell を使用して、サービスの DNS 名を設定できます。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml で既定のサービスの DNS 名を設定する
Visual Studio またはお好みのエディターでプロジェクトを開いて、`ApplicationManifest.xml` ファイルを開きます。 既定のサービスのセクションに移動し、各サービスに `ServiceDnsName` 属性を追加します。 次の例は、サービスの DNS 名を `service1.application1` に設定する方法を示します。

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
ここで、アプリケーションをデプロイします。 アプリケーションのデプロイ後に Service Fabric Explorer のサービス インスタンスに移動すると、このインスタンスの DNS 名が次のように表示されます。 

![service endpoints][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Powershell を使用してサービスの DNS 名を設定する
サービスの作成時に、`New-ServiceFabricService` Powershell を使用してサービスの DNS 名を設定できます。 次の例では、DNS 名が `service1.application1` の新しいステートレス サービスを作成します。

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>サービスで DNS を使用する
複数のサービスをデプロイした場合は、DNS 名を使用して、通信する他のサービスのエンドポイントを検索できます。 ステートフル サービスでは DNS プロトコルの通信先が不明なため、これはステートレス サービスにのみ適用されます。 ステートフル サービスの場合は、HTTP 呼び出し用の組み込みのリバース プロキシを使用して、特定のサービス パーティションを呼び出します。

次のコードは、別のサービスを呼び出す方法、つまり通常の HTTP 呼び出しを示します。 URL の一部として、ポートと省略可能なパスを入力する必要があることに注意してください。

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>次のステップ
[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照して、クラスター内でのサービスの通信の詳細を確認する

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG

