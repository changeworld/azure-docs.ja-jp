---
title: "Azure インスタンス メタデータ サービスの概要 | Microsoft Docs"
description: "VM のコンピューティング、ネットワーク、および今後のメンテナンス イベントに関する情報を取得するための RESTful インターフェイスです。"
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: 
ms.service: azure-instancemetadataservice
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: harijay
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: b5c0268e1a0ebfcb33781678a367090cad865907
ms.lasthandoff: 04/11/2017

---

# <a name="azures-instance-metadata-service---preview"></a>Azure Instance Metadata Service -- プレビュー

> [!NOTE] 
> プレビューは、使用条件に同意することを条件に使用することができます。 詳細については、『Microsoft Azure プレビューの追加使用条件』 (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) をご覧ください。
>

インスタンス メタデータ サービスは、実行中の仮想マシン インスタンスのに関する情報を提供しています。 この情報を使用して、Azure でインスタンスを管理および構成できます。 Azure インスタンス メタデータ サービスは、新しい [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) を使用して作成されたすべての IaaS VM で使用可能な RESTful エンドポイントです。 このエンドポイントは、VM からのみアクセスできる、よく知られているルーティング不可 IP アドレス (*169.254.169.254*) で使用できます。 このサービスでは、仮想マシン インスタンス、そのネットワーク構成、および今後のメンテナンス イベントに関する重要なデータが提供されます。 詳細については、[メタデータのカテゴリ](#instance-metadata-data-categories)を参照してください。

### <a name="important-information"></a>重要な情報
現在、このサービスは**プレビュー**状態であり、仮想マシン インスタンスと今後のメンテナンス イベントに関する情報を提供しています。 このサービスは、継続的に更新されています。ページには、使用可能な特定の[データ カテゴリ](#instance-metadata-data-categories)が随時表示されます。


## <a name="service-availability"></a>サービスの可用性
現在のプレビューは、Azure の**米国中西部**リージョンで使用できます。 以下の表には、サービス プレビューが使用可能になるリージョンについての更新情報が表示されています。
インスタンス メタデータ サービスを試すには、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) または [Azure Portal](http://portal.azure.com) から VM を作成し、例のセクションにある手順の例に従い、メタデータ サービスにアクセスします。 

インスタンス メタデータ サービスのプレビューを使用可能なリージョン|
------------------------------------------------------------|
米国中西部 |



## <a name="retrieving-instance-metadata"></a>インスタンス メタデータの取得 

インスタンス メタデータは、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/?redirectedfrom=MSDN) を使用して作成/管理されている実行中の VM で使用できます。 仮想マシン インスタンスのすべてのデータ カテゴリにアクセスするには、次の URI を使用します。

```
 curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

すべてのインスタンス メタデータの既定の出力は、json 形式 (コンテンツ タイプ Application/json) で行われます。

## <a name="usage-examples"></a>使用例
次に、インスタンス メタデータ サービスの一連の例と使用セマンティクスを示します。

### <a name="versioning"></a>バージョン管理 
インスタンス メタデータ サービスはバージョン管理されています。 バージョンは必須で、最新のプレビュー バージョンは 2017-03-01 です。


```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。 サービスが一般に使用可能になると、現在のプレビュー バージョンは使用できなくなる可能性があります。


### <a name="data-output"></a>データ出力
既定では、インスタンス メタデータはデータを json 形式 (コンテンツ タイプ =application/json) で返します。別のノード要素の場合、適宜、異なる既定形式でデータが返される可能性があります。次の表に、データ形式に関する簡単なリファレンスを示します。 

要素 | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/instance | json | テキスト
/scheduledevents | json | なし

たとえば、テキスト形式の場合、要求 URL に format=text を使用します 

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01&format=text"
```
### <a name="security"></a>セキュリティ
インスタンス メタデータのエンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティングできない 169.254.169.254 IP アドレスでアクセスできます。 また、**X-Forwarded-For header** のあるすべての要求は、メタデータ サービスによって拒否されます。 実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に **Metadata:true** ヘッダーが含まれた状態で送信されるようにする必要もあります。 
### <a name="error"></a>エラー
見つからないデータ要素または無効な形式の要求があると、インスタンス メタデータ サービスによって標準の HTTP エラーが返されます。リターン コードの例を以下に示します。 

HTTP リターン コード | 理由
----------------|-------
200 | OK
400 | 正しくない要求。ヘッダーの欠落。-H Metadata:true が渡されます
404 | 見つかりません。要求された要素は存在しません 
405 | メソッドがサポートされていません 
429 | 要求が多すぎます。現在、1 秒間にサポートされるクエリは最大で 5 つです

### <a name="examples"></a>例
#### <a name="retrieving-the-network-information"></a>ネットワーク情報の取得 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-03-01"

```
**応答**
> [!NOTE] 
> 応答は json 文字列です。 次の出力は、[jq](https://stedolan.github.io/jq/) などのユーティリティを使用して json で形式設定されます。
>

```
{
  "interface": [
    {
      "ipv4": {
        "ipaddress": [
          {
            "ipaddress": "10.0.0.4",
            "publicip": "<>.<>.<>.<>"
          }
        ],
        "subnet": [
          {
            "address": "10.0.0.0",
            "dnsservers": [
              {
                "ipaddress": "10.0.0.2"
              },
              {
                "ipaddress": "10.0.0.3"
              }
            ],
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipaddress": []
      },
      "mac": "000D3A00FA89"
    }
  ]
}
```

#### <a name="retrieving-public-ip-address"></a>パブリック IP アドレスの取得

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipaddress/0/publicip?api-version=2017-03-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>インスタンスのすべてのメタデータの取得

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-03-01"
```
**応答**
> [!NOTE]
> 応答は json 文字列です。 次の出力は、[jq](https://stedolan.github.io/jq/) などのユーティリティを使用して json で書式設定されます。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipaddress": [
            {
              "ipaddress": "10.0.0.4",
              "publicip": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.0.0",
              "dnsservers": [
                {
                  "ipaddress": "10.0.0.2"
                },
                {
                  "ipaddress": "10.0.0.3"
                }
              ],
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipaddress": []
        },
        "mac": "000D3A00FA89"
      }
    ]
  }
}

```
#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows 仮想マシンでのメタデータの取得

インスタンスのメタデータは、Powershell ユーティリティ curl を使用して Windows で取得できます。 Powershell プロンプトから、次のコマンドを実行します。 

**要求**
```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-03-01 | select -ExpandProperty Content
```
**応答**
>[!NOTE]
> 応答は json 文字列です。 次の出力は、ConvertTo-Json Powershell ユーティリティを使用して json で書式設定されます。
> 

```
{
    "compute":  {
                    "location":  "CentralUSEUAP",
                    "name":  "SQLTest",
                    "offer":  "SQL2016SP1-WS2016",
                    "osType":  "Windows",
                    "platformFaultDomain":  "0",
                    "platformUpdateDomain":  "0",
                    "publisher":  "MicrosoftSQLServer",
                    "sku":  "Enterprise",
                    "version":  "13.0.400110",
                    "vmId":  "453945c8-3923-4366-b2d3-ea4c80e9b70e",
                    "vmSize":  "Standard_DS2"
                },
    "network":  {
                    "interface":  [
                                      "@{ipv4=; ipv6=; mac=002248020E1E}"
                                  ]
                }
}
```


## <a name="instance-metadata-data-categories"></a>インスタンス メタデータのデータ カテゴリ
次の表には、インスタンス メタデータを介して使用できるすべてのデータ カテゴリが一覧表示されています。

データ | Description
-----|------------
location | VM が実行されている Azure リージョン 
name | VM の名前 
offer | VM イメージのプラン情報。これらの値は Azure イメージ ギャラリーからデプロイされるイメージについてのみ存在します 
publisher | VM イメージの発行元
sku | VM イメージの特定の SKU  
version | VM イメージのバージョン 
osType | Linux または Windows 
platformUpdateDomain |  VM が実行されている[更新ドメイン](virtual-machines-windows-manage-availability.md)。 
platformFaultDomain | VM が実行されている[障害ドメイン](virtual-machines-windows-manage-availability.md)。
vmId | VM の一意の ID。詳細については[ここ](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)を参照
vmSize | [VM サイズ](virtual-machines-windows-sizes.md)
ipv4/ipaddress | VM のローカル IP アドレス 
ipv4/publicip | インスタンスのパブリック IP アドレス 
subnet/address | サブネットのアドレス 
subnet/dnsservers/ipaddress1 | プライマリ DNS サーバー
subnet/dnsservers/ipaddress2 | セカンダリ DNS サーバー
subnet/prefix | サブネットのプレフィックス (24 など)
ipv6/ipaddress | VM の IPv6 アドレス
mac | VM の mac アドレス 
scheduledevents | [scheduledevents](virtual-machines-scheduled-events.md) を参照



## <a name="example-scenarios-for-usage"></a>使用に関するシナリオ例  

### <a name="tracking-vm-running-on-azure"></a>Azure で動作している VM の追跡 

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM 用に一意の ID を取得できるようにするには、インスタンス メタデータ サービスの vmId フィールドを使用します 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-03-01&format=text"
```
**応答**
```
5c08b38e-4d57-4c23-ac45-aca61037f084

```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>障害/更新ドメインに基づくコンテナー、データ パーティションの配置 

さまざまなレプリカの配置が非常に重要な特定のシナリオ向けです。 たとえば、[HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)、または [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介したコンテナーの配置の場合、VM が実行されている platformFaultDomain と platformUpdateDomain を把握する必要があります。 インスタンス メタデータを使用して直接このデータ ポイントを照会できます。

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-03-01&format=text" 
```
**応答**
```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>サポート ケースの際の VM に関する詳細情報の取得 

サービス プロバイダーは、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。 

**要求**
```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-03-01"
```
**応答**
> [!NOTE] 
> 応答は json 文字列です。 次の出力は、[jq](https://stedolan.github.io/jq/) などのユーティリティを使用して json で書式設定されます。
>

```
{
"compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }

}
```

## <a name="faq"></a>FAQ
1. "400 - 正しくない要求、必要なメタデータ ヘッダーが指定されていません" というエラーを受け取ります。 これはどういう意味でしょうか。
   * インスタンス メタデータ サービスでは、要求でヘッダー Metadata:true が渡される必要があります。 REST 呼び出しでヘッダーを渡すと、インスタンス メタデータ サービスへのアクセスが許可されます。 
2. 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
   * 現在、インスタンス メタデータ サービスでは、Azure Resource Manager で作成されたインスタンスのみがサポートされています。将来、Cloud Services VM に対するサポートも追加される可能性があります。 
3. 少し前に、Azure Resource Manager を介して仮想マシンを作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../azure-resource-manager/resource-group-using-tags.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除し、メタデータを更新します。
4. "500 - 内部サーバー エラー" というエラーを受け取るのはなぜですか。
   * 現在、インスタンス メタデータ プレビューを使用できるのは、米国中西部リージョンのみです。 サポートされているリージョンで VM をデプロイしてください。  
4. その他の質問/コメントはどこで共有すればいいですか。
   * feedback.azure.com でコメントを送信してください。
    
## <a name="next-steps"></a>次のステップ

[scheduledevents] (virtual-machines-scheduled-events.md) について詳しく学習します

