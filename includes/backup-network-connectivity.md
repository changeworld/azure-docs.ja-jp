---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 234d2510f115edf646ac490126e316a7dd3369c1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019309"
---
MARS エージェントは、Azure Active Directory、Azure Storage、Azure Backup の各サービス エンドポイントにアクセスできる必要があります。 パブリック IP 範囲を取得するには、[JSON ファイル](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true)を参照してください。 Azure Backup (`AzureBackup`)、Azure Storage (`Storage`)、および Azure Active Directory (`AzureActiveDirectory`) に対応する IP へのアクセスを許可します。 また、Windows のバージョンによっては、オペレーティング システムのネットワーク接続チェックで `www.msftconnecttest.com` または `www.msftncsi.com` へのアクセスが必要になります。

コンピューターのインターネット アクセスが制限されている場合は、ファイアウォール、プロキシ、ネットワークの設定で次の FQDN とパブリック IP アドレスへのアクセスが許可されていることを確認してください。

### <a name="url-and-ip-access"></a>URL と IP アクセス

**FQDN**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP アドレス**

- `20.190.128.0/18`
- `40.126.0.0/18`

米国政府機関のお客様の場合は、次の URL にアクセスできることを確認してください。

- `www.msftncsi.com`
- `*.microsoft.com`
- `*.windowsazure.us`
- `*.microsoftonline.us`
- `*.windows.net`
- `*.usgovcloudapi.net`

上記のすべての URL と IP アドレスにアクセスするには、ポート 443 で HTTPS プロトコルを使用します。

MARS エージェントを使用して Azure VM からファイルとフォルダーをバックアップするときは、Azure 仮想ネットワークがアクセスを許可するよう構成する必要もあります。 ネットワーク セキュリティ グループ (NSG) を使用する場合は、AzureBackup サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、Azure AD (AzureActiveDirectory) および Azure Storage (Storage) に対して同様の [NSG 規則](../articles/virtual-network/network-security-groups-overview.md#service-tags)を作成することによって、認証とデータ転送のための接続を許可する必要もあります。

Azure Backup タグの規則を作成するには、次の手順に従います。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。
1. <bpt id="p2">**</bpt>[設定]<ept id="p2">**</ept> で <bpt id="p1">**</bpt>[送信セキュリティ規則]<ept id="p1">**</ept> を選択します。
1. **[追加]** を選択します。
1. [セキュリティ規則の設定](../articles/virtual-network/manage-network-security-group.md#security-rule-settings)の説明に従って、新しい規則を作成するために必要なすべての詳細を指定します。<br>オプションが次のように設定されていることを確認してください。
   - **[宛先]** が _[サービス タグ]_ に設定されている。
   - **[宛先サービス タグ]** が _[AzureBackup]_ に設定されている。
1. **[追加]** を選択して、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する NSG 送信セキュリティ規則も、同様に作成できます。 サービス タグの詳細については、「[仮想ネットワークのサービス タグ](../articles/virtual-network/service-tags-overview.md)」を参照してください。

### <a name="azure-expressroute-support"></a>Azure ExpressRoute のサポート

パブリック ピアリング (古い回線で使用可能) を使用して、Azure ExpressRoute を通してデータをバックアップできます。 プライベート ピアリングを使用した Microsoft ピアリング バックアップはサポートされていません。

パブリック ピアリングを使用するには、次のドメインとアドレスがポート 443 で HTTPS アクセス可能なことを確認してください。

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- `*.microsoft.com`
- `*.windowsazure.com`
- `*.microsoftonline.com`
- `*.windows.net`

**IP アドレス**
- `20.190.128.0/18`
- `40.126.0.0/18`

Microsoft ピアリングを使用するには、次のサービス、リージョン、関連するコミュニティの値を選択します。
- Azure Active Directory (12076:5060)
- Azure リージョン、Recovery Services コンテナーの場所による
- Azure Storage、Recovery Services コンテナーの場所による

[ExpressRoute ルーティングの要件](../articles/expressroute/expressroute-routing.md#bgp)の詳細をご覧ください。

>[!NOTE]
>パブリック ピアリングは、新しい回線では非推奨です。


### <a name="private-endpoint-support"></a>プライベート エンドポイントのサポート

プライベート エンドポイントを使用して、サーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 Azure AD にプライベート エンドポイント経由でアクセスできないため、Azure AD に必要な IP と FQDN にアウトバウンド アクセスを個別に許可する必要があります。

MARS エージェントを使用してオンプレミスのリソースをバックアップするときは、バックアップ対象のリソースが含まれるオンプレミスのネットワークが、コンテナー用のプライベート エンドポイントが含まれる Azure VNet とピアリングされるようにしてください。 その後で、MARS エージェントのインストールを続行し、バックアップを構成することができます。 ただし、バックアップ用のすべての通信が、ピアリングされたネットワークのみを介して行われるようにする必要があります。

MARS エージェントの登録後にそのコンテナー用のプライベート エンドポイントを削除した場合は、コンテナーを Recovery Services コンテナーに再登録する必要があります。 それらに対する保護を停止する必要はありません。 詳細については、[Azure Backup のプライベート エンドポイント](../articles/backup/private-endpoints.md)に関するページを参照してください。

### <a name="throttling-support"></a>調整のサポート

| 特徴量 | 詳細 |
|---|---|
| 帯域幅の制御 | サポートされています。 MARS エージェントで、 **[プロパティの変更]** を使用して帯域幅を調整します。 |
| Network throttling | Windows Server 2008 R2、Windows Server 2008 SP2、または Windows 7 を実行しているバックアップ済みのコンピューターでは使用できません。 |
