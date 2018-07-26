---
title: Azure Instance Metadata Service | Microsoft Docs
description: Windows VM のコンピューティング、ネットワーク、および今後のメンテナンス イベントに関する情報を取得するための RESTful インターフェイスです。
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: de597424c1be01e651068b7900acbece822610b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008377"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service


Azure Instance Metadata Service は、実行中の仮想マシン インスタンスに関する情報を提供します。これらの情報を使用して仮想マシンの管理と構成を行うことができます。
SKU、ネットワークの構成、今後のメンテナンス イベントなどの情報が含まれます。 使用できる情報の種類の詳細については、「[メタデータ のカテゴリ](#instance-metadata-data-categories)」を参照してください。

Azure Instance Metadata Service は、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成されたすべての IaaS VM にアクセスできる REST エンドポイントです。 このエンドポイントは、VM からのみアクセスすることができる、よく知られているルーティング不可能な IP アドレス (`169.254.169.254`) で使用できます。

> [!IMPORTANT]
> このサービスは、すべての Azure リージョンで**一般公開**されています。  仮想マシン インスタンスに関する新しい情報を公開する更新が定期的に行われています。 このページは、使用できる最新の[データ カテゴリ](#instance-metadata-data-categories)を反映しています。

## <a name="service-availability"></a>サービスの提供状況
このサービスは、一般公開されている Azure リージョンで使用できます。 すべての API バージョンが、すべての Azure リージョンで利用可能なわけではありません。

リージョン                                        | 提供状況                                 | サポートされているバージョン
-----------------------------------------------|-----------------------------------------------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | 一般公開   | 2017-04-02、2017-08-01、2017-12-01、2018-02-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 一般公開 | 2017-04-02、2017-08-01
[Azure China](https://www.azure.cn/)                                                           | 一般公開 | 2017-04-02、2017-08-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | 一般公開 | 2017-04-02、2017-08-01

この表は、サービスが更新された場合や、バージョンが新しくサポートされた場合に更新されます。

Instance Metadata Service を試すには、上記のリージョンで [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) または [Azure ポータル](http://portal.azure.com)から VM を作成し、この後の例に従います。

## <a name="usage"></a>使用法

### <a name="versioning"></a>バージョン管理
インスタンス メタデータ サービスはバージョン管理されています。 バージョンは必須であり、Global Azure の現在のバージョンは `2017-12-01` です。 現在サポートされているバージョンは (2017-04-02、2017-08-01、2017-12-01) です。

> [!NOTE] 
> スケジュールされたイベントの前のプレビュー リリースでは、api-version として {latest} がサポートされていました。 この形式はサポートされなくなり、今後非推奨となる予定です。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。 ただし、サービスが一般公開されると、以前のプレビュー バージョン (2017-03-01) は使用できなくなる可能性があります。

### <a name="using-headers"></a>ヘッダーの使用
Instance Metadata Service のクエリを実行するときは、要求が意図せずリダイレクトされないように、`Metadata: true` ヘッダーを指定する必要があります。

### <a name="retrieving-metadata"></a>メタデータの取得

インスタンス メタデータは、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成/管理されている実行中の VM で使用できます。 仮想マシン インスタンスのすべてのデータ カテゴリにアクセスするには、次の要求を使用します。

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> すべてのインスタンス メタデータ クエリでは、大文字小文字が区別されます。

### <a name="data-output"></a>データ出力
既定では、Instance Metadata Service は JSON 形式でデータを返します (`Content-Type: application/json`)。 ただし、要求すれば、別の API から別の形式でデータが返されます。
次の表は、API がサポートできるその他のデータ形式を示しています。

API | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/instance | json | テキスト
/scheduledevents | json | なし

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>セキュリティ
Instance Metadata Service エンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティング不可能な IP アドレスでアクセスできます。 さらに、`X-Forwarded-For` ヘッダーがあるすべての要求は、サービスによって拒否されます。
また、実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に `Metadata: true`ヘッダーを含める必要があります。 

### <a name="error"></a>エラー
見つからないデータ要素または無効な形式の要求がある場合、Instance Metadata Service は標準 HTTP エラーを返します。 For example:

HTTP 状態コード | 理由
----------------|-------
200 OK |
400 Bad Request | `Metadata: true` ヘッダーがありません
404 見つかりません | 要求された要素は存在しません 
405 Method Not Allowed | `GET` 要求と `POST` 要求のみがサポートされています
429 Too Many Requests | 現在 API は、1 秒あたり最大 5 つのクエリをサポートしています
500 Service Error     | しばらくしてからやり直してください

### <a name="examples"></a>例

> [!NOTE] 
> すべての API の応答は、JSON 文字列です。 次のすべての例の応答は、読みやすくするために整えられています。

#### <a name="retrieving-network-information"></a>ネットワーク情報の取得

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>パブリック IP アドレスの取得

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>インスタンスのすべてのメタデータの取得

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows 仮想マシンでのメタデータの取得

**要求**

インスタンス メタデータは、Windows で `curl` プログラムを使用して取得できます。 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

あるいは、`Invoke-RestMethod` PowerShell コマンドレットで取得できます。
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>インスタンス メタデータのデータ カテゴリ
Instance Metadata Service を通して次のデータ カテゴリを使用できます。

データ | Description | 導入されたバージョン 
-----|-------------|-----------------------
location | VM を実行中の Azure リージョン | 2017-04-02 
name | VM の名前 | 2017-04-02
offer | VM イメージのプラン情報。 この値は、Azure イメージ ギャラリーからデプロイされるイメージにのみ存在します。 | 2017-04-02
publisher | VM イメージの発行元 | 2017-04-02
sku | VM イメージの特定の SKU | 2017-04-02
version | VM イメージのバージョン | 2017-04-02
osType | Linux または Windows | 2017-04-02
platformUpdateDomain |  VM を実行中の[更新ドメイン](manage-availability.md) | 2017-04-02
platformFaultDomain | VM を実行中の[障害ドメイン](manage-availability.md) | 2017-04-02
vmId | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmSize | [VM サイズ](sizes.md) | 2017-04-02
subscriptionId | 仮想マシンの Azure サブスクリプション | 2017-08-01
tags | お使いの仮想マシンの[タグ](../../azure-resource-manager/resource-group-using-tags.md)  | 2017-08-01
resourceGroupName | お使いの仮想マシンの[リソース グループ](../../azure-resource-manager/resource-group-overview.md) | 2017-08-01
placementGroupId | お使いの仮想マシン スケール セットの[配置グループ](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
vmScaleSetName | お使いの仮想マシン スケール セットの[仮想マシン スケール セット名](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
ゾーン | 仮想マシンの[可用性ゾーン](../../availability-zones/az-overview.md) | 2017-12-01 
ipv4/privateIpAddress | VM のローカル IPv4 アドレス | 2017-04-02
ipv4/publicIpAddress | VM のパブリック IPv4 アドレス | 2017-04-02
subnet/address | VM のサブネット アドレス | 2017-04-02 
subnet/prefix | サブネットのプレフィックス (24 など) | 2017-04-02 
ipv6/ipAddress | VM のローカル IPv6 アドレス | 2017-04-02 
macAddress | VM の mac アドレス | 2017-04-02 
scheduledevents | [スケジュールされたイベント](scheduled-events.md)に関する記事を参照してください。 | 2017-08-01
identity | (プレビュー) マネージド サービス ID。 「[アクセス トークンの取得](../../active-directory/managed-service-identity/how-to-use-vm-token.md)」を参照してください | 2018-02-01

## <a name="example-scenarios-for-usage"></a>使用に関するシナリオ例  

### <a name="tracking-vm-running-on-azure"></a>Azure で動作している VM の追跡

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、Instance Metadata Service から `vmId` フィールドを使用します。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**応答**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>障害/更新ドメインに基づくコンテナー、データ パーティションの配置 

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した[ HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておかなければならない場合があります。
これらの決定を行うために、インスタンスの[可用性ゾーン](../../availability-zones/az-overview.md)を活用することもできます。
このデータは、Instance Metadata Service を使用して直接照会できます。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**応答**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>サポート ケースの際の VM に関する詳細情報の取得 

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。 

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**応答**

> [!NOTE] 
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM 内でさまざまな言語を使用してメタデータ サービスを呼び出す例 

言語 | 例 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>FAQ
1. `400 Bad Request, Required metadata header not specified` エラーが発生します。 これはどういう意味でしょうか。
   * Instance Metadata Service では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、Instance Metadata Service へのアクセスが許可されます。 
2. 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
   * 現時点では、Instance Metadata Service は、Azure Resource Manager で作成されたインスタンスのみをサポートします。 今後、クラウド サービス VM に対するサポートが追加される可能性があります。
3. 少し前に、Azure Resource Manager を介して仮想マシンを作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/resource-group-using-tags.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
4. 2017-08-01 の新しいバージョンに入力されたすべてのデータが表示されません。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/resource-group-using-tags.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
5. `500 Internal Server Error` エラーが発生します。なぜですか。
   * 指数バックオフ システムに基づいて、要求を再試行してください。 問題が解決しない場合は、Azure サポートにお問い合わせください。
6. その他の質問/コメントはどこで共有すればいいですか。
   * コメントは http://feedback.azure.com で送信してください。
7. このサービスは仮想マシン スケール セット インスタンスで機能しますか。
   * はい。Metadata Service は、スケール セット インスタンスで利用できます。 
8. このサービスのサポートを受けるにはどうすればよいですか。
   * サービスのサポートを受けるには、時間がかかる再試行の後もメタデータの応答を取得できない VM を管理する Azure ポータルでサポート問題を作成します。 

   ![Instance Metadata のサポート](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>次の手順

- 詳細については、「[スケジュール化されたイベント](scheduled-events.md)」を参照してください。
