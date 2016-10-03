<properties
   pageTitle="スタンドアロン クラスターの構成 | Microsoft Azure"
   description="この記事では、スタンドアロンまたはプライベート Service Fabric クラスターを構成する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>


# スタンドアロン Windows クラスターの構成設定

この記事では、_**ClusterConfig.JSON**_ ファイルを使用して、スタンドアロン Service Fabric クラスターを構成する方法について説明します。[スタンドアロン Service Fabric パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)すると、このファイルが作業コンピューターにダウンロードされます。ClusterConfig.JSON ファイルでは、Service Fabric ノードとその IP アドレス、クラスターの各種ノード、セキュリティ構成などの情報と、障害/アップグレード ドメインの観点での Service Fabric クラスターのネットワーク トポロジを指定できます。

このファイルのさまざまなセクションについて以下に説明します。

## 一般的なクラスター構成
次の JSON スニペットに示すように、ここでは、クラスター固有の一般的な構成について説明します。

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

**name** 変数にフレンドリ名を割り当てることで、Service Fabric クラスターにフレンドリ名を付けることができます。セットアップに従って、**clusterManifestVersion** を変更できます。Service Fabric 構成をアップグレードする前に、これを更新する必要があります。**apiVersion** は既定値のままにしておくこともできます。


<a id="clusternodes"></a>
## クラスターのノード
次のスニペットに示すように、**nodes** セクションを使用して Service Fabric クラスターのノードを構成できます。

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Service Fabric クラスターには、少なくとも 3 つのノードが必要です。セットアップに従って、このセクションにさらに多くのノードを追加できます。各ノードの構成設定を次の表に示します。

|**ノード構成**|**説明**|
|-----------------------|--------------------------|
|nodeName|ノードにフレンドリ名を付けることができます。|
|iPAddress|コマンド ウィンドウを開き、「`ipconfig`」と入力することで、ノードの IP アドレスがわかります。IPV4 アドレスをメモし、そのアドレスを **iPAddress** 変数に割り当てます。|
|nodeTypeRef|ノードごとに異なるノード タイプを割り当てることができます。[node types](#nodetypes) は、後述のセクションで定義されています。|
|faultDomain|障害ドメインにより、クラスター管理者は、共有される物理的な依存関係によって同時に障害が発生する物理ノードを定義できます。|
|upgradeDomain|アップグレード ドメインは、Service Fabric のアップグレードのためにほぼ同時にシャットダウンされる一連のノードを表します。アップグレード ドメインは物理的な要件によって制限されないため、どのノードをどのアップグレード ドメインに割り当てるかを選択できます。| 


## クラスターの **properties**

ClusterConfig.JSON の **properties** セクションは、以下のようにクラスターの構成に使用します。

### **diagnosticsStore**
次のスニペットに示すように、**diagnosticsStore** セクションを使用して、ノードおよびクラスターの障害の診断とトラブルシューティングを可能にするパラメーターを構成できます。

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** は、クラスターの診断の説明であり、セットアップに従って設定できます。これらの変数は、ETW トレース ログ、クラッシュ ダンプ、パフォーマンス カウンターの収集に役立ちます。ETW トレース ログの詳細については、「[Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx)」および「[ETW トレース](https://msdn.microsoft.com/library/ms751538.aspx)」を参照してください。[クラッシュ ダンプ](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/)、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)など、すべてのログを、コンピューターの **connectionString** フォルダーに送信できます。**AzureStorage** を使用して診断を格納することもできます。サンプル スニペットを次に示します。

	"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### **security** 
**security** セクションは、セキュリティで保護されたスタンドアロン Service Fabric クラスターに必要です。次のスニペットは、このセクションの一部を示しています。

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
		. . .
	}

**metadata** は、セキュリティで保護されたクラスターの説明であり、セットアップに従って設定できます。**ClusterCredentialType** と **ServerCredentialType** によって、クラスターとノードが実装するセキュリティの種類が決まります。これらのパラメーターは、証明書ベースのセキュリティの場合は *X509* に設定し、Azure Active Directory ベースのセキュリティの場合は *Windows* に設定します。**security** セクションの残りの部分はセキュリティの種類に基づきます。**security** セクションの残りの部分の入力方法については、[スタンドアロン クラスターの証明書ベースのセキュリティ](service-fabric-windows-cluster-x509-security.md)または[スタンドアロン クラスターの Windows セキュリティ](service-fabric-windows-cluster-windows-security.md)に関する記事をご覧ください。

### **reliabilityLevel**
**reliabilityLevel** では、クラスターのプライマリ ノードで実行できるシステム サービスのコピーの数を定義します。これにより、これらのサービスの信頼性が向上するため、クラスターの信頼性が向上します。この変数は、システム サービスのコピーが 3 個の場合は *Bronze*、5 個の場合は *Silver*、7 個の場合は *Gold*、9 個の場合は *Platinum* に設定できます。次の例をご覧ください。

	"reliabilityLevel": "Bronze",
	
1 つのプライマリ ノードで実行されるシステム サービスのコピーは 1 つであるため、信頼性レベルが *Bronze* の場合は 3 個、*Silver* の場合は 5 個、*Gold* の場合は 7 個、*Platinum* の場合は 9 個のプライマリ ノードが少なくとも必要になります。


<a id="nodetypes"></a>
### **nodeTypes**
**nodeTypes** セクションでは、クラスターのノードのタイプを記述します。次のスニペットに示すように、クラスターにはノード タイプを少なくとも 1 つは指定する必要があります。

	"nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
			"startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

**name** はこのノード タイプのフレンドリ名です。このノード タイプのノードを作成するには、「[クラスターのノード](#clusternodes)」で前述したように、そのノードの **nodeTypeRef** 変数にこのノード タイプのフレンドリ名を割り当てる必要があります。ノード タイプごとに、このクラスターに接続するためのさまざまなエンドポイントを定義できます。このクラスターの他のエンドポイントと競合しない限り、これらの接続エンドポイントの任意のポート番号を選択できます。複数のノード タイプが存在するクラスターでは、プライマリ ノード タイプは 1 つだけであり、**isPrimary** が *true* に設定されています。それ以外のノードは、**isPrimary** が *false* に設定されます。クラスターの容量に従った **nodeTypes** と **reliabilityLevel** の値の詳細、およびプライマリ ノード タイプと非プライマリ ノード タイプの違いについては、「[Service Fabric クラスターの容量計画に関する考慮事項](service-fabric-cluster-capacity.md)」を参照してください。


### **fabricSettings**
このセクションでは、Service Fabric のデータとログのルート ディレクトリを設定できます。これらのディレクトリは、クラスターの最初の作成時にのみカスタマイズできます。このセクションのサンプル スニペットを次に示します。

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

非 OS ドライブは OS のクラッシュに対する信頼性が高いため、FabricDataRoot および FabricLogRoot として使用することをお勧めします。データ ルートだけをカスタマイズすると、ログ ルートはデータ ルートの 1 つ下のレベルに配置されます。


## 次のステップ

スタンドアロン クラスターのセットアップに従って、ClusterConfig.JSON ファイルの構成を完了したら、[オンプレミスまたはクラウドでの Azure Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関するページに従ってクラスターをデプロイした後、「[Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」に進むことができます。

<!---HONumber=AcomDC_0921_2016-->