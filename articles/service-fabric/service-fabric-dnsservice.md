---
title: Azure Service Fabric の DNS サービス | Microsoft Docs
description: Service Fabric の DNS サービスを使用して、クラスター内からマイクロサービスを検出します。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204996"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric の DNS サービス
オプションのシステム サービスである DNS サービスをクラスターで有効にし、DNS プロトコルを使用して他のサービスを検出できます。 

コンテナー化されたサービスなど多くのサービスには既存の URL 名が含まれるため、(Naming Service プロトコルではなく) 標準の DNS プロトコルを使用してこれらを解決できるため、望ましい方法です。特に "リフト アンド シフト" シナリオに適しています。 DNS サービスを使用すると、DNS 名をサービス名にマップして、エンドポイントの IP アドレスを解決できます。 

DNS サービスによって DNS 名はサービス名にマップされ、ネーム サービスによって解決され、サービス エンドポイントに返されます。 サービスの DNS 名は、作成時に提供されます。

![service endpoints](./media/service-fabric-dnsservice/dns.png)

動的ポートは、DNS サービスではサポートされていません。 動的ポート上で公開されたサービスを解決するには、[リバース プロキシ サービス](./service-fabric-reverseproxy.md)を使用します。

## <a name="enabling-the-dns-service"></a>DNS サービスを有効にする
ポータルを使用してクラスターを作成すると、DNS サービスは **[Include DNS service] (DNS サービスを含める)** チェック ボックスと **[クラスター構成]** メニューでは既定で有効になります。

![ポータルで DNS サービスを有効にする][2]

ポータルを使用してクラスターを作成していないか、または既存のクラスターを更新している場合は、テンプレートで DNS サービスを有効にする必要があります。

- 新しいクラスターをデプロイするには、[サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用するか、または独自の Resource Manager テンプレートを作成できます。 
- 既存のクラスターを更新するには、ポータルでそのクラスターのリソース グループに移動し、**[Automation スクリプト]** をクリックして、グループ内のクラスターやその他のリソースの現在の状態が反映されたテンプレートを操作できます。 詳細については、「[リソース グループからのテンプレートのエクスポート](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)」を参照してください。

テンプレートが用意されたら、次の手順で DNS サービスを有効にできます。

1. `Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` 以降に設定されていることを確認し、設定されていない場合は、それを次のスニペットに示すように更新します。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 次のスニペットのように、`fabricSettings` セクションの後に次の `addonFeatures` セクションを追加して DNS サービスを有効にします。 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 上記の変更でクラスター テンプレートを更新したら、その変更を適用してアップグレードを完了します。 アップグレードが完了すると、DNS システム サービスはクラスター内で実行を開始します。 サービス名は `fabric:/System/DnsService` であり、それは Service Fabric Explorer の **[システム]** サービス セクションで見つかります。 


## <a name="setting-the-dns-name-for-your-service"></a>サービスの DNS 名を設定する
DNS サービスがクラスター内で実行されたら、`ApplicationManifest.xml` で既定のサービスに対して宣言的に、または PowerShell コマンドを使用してサービスの DNS 名を設定できます。

サービスの DNS 名は、クラスター全体で解決可能です。 `<ServiceDnsName>.<AppInstanceName>` (`service1.application1` など) の名前付けスキームを使用することを強くお勧めします。 それにより、クラスター全体での DNS 名の一意性が保証されます。 アプリケーションが Docker Compose を使用してデプロイされる場合、サービスには、この名前付けスキームを使用した DNS 名が自動的に割り当てられます。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>ApplicationManifest.xml で既定のサービスの DNS 名を設定する
Visual Studio またはお好みのエディターでプロジェクトを開いて、`ApplicationManifest.xml` ファイルを開きます。 既定のサービスのセクションに移動し、各サービスに `ServiceDnsName` 属性を追加します。 次の例は、サービスの DNS 名を `service1.application1` に設定する方法を示します。

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
アプリケーションをデプロイすると、Service Fabric Explorer のサービス インスタンスに、このインスタンスの DNS 名が次の図のように表示されます。 

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
複数のサービスをデプロイした場合は、DNS 名を使用して、通信する他のサービスのエンドポイントを検索できます。 DNS プロトコルはステートフル サービスと通信できないため、DNS サービスはステートレス サービスにのみ適用されます。 ステートフル サービスの場合は、HTTP 呼び出しのための組み込みの[リバース プロキシ サービス](./service-fabric-reverseproxy.md)を使用して、特定のサービス パーティションを呼び出すことができます。 動的ポートは、DNS サービスではサポートされていません。 動的ポートを使用するサービスを解決するには、リバース プロキシを使用できます。

次のコードは、別のサービスを呼び出す方法を示しています。これはごく普通の HTTP 呼び出しであり、ポートと、省略可能なパスを URL の一部として指定します。

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

## <a name="next-steps"></a>次の手順
[サービスとの接続と通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照して、クラスター内でのサービスの通信の詳細を確認する

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
