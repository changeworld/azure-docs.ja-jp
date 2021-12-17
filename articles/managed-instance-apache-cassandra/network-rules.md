---
title: Azure Managed Instance for Apache Cassandra に必要なアウトバウンド ネットワーク規則
description: Azure Managed Instance for Apache Cassandra に必要なアウトバウンド ネットワーク規則と FQDN について説明します
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: chrande
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6d52f1c72765b3e7d3b7dd171c53c84e85138a20
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005235"
---
# <a name="required-outbound-network-rules"></a>必要なアウトバウンド ネットワーク規則

Azure Managed Instance for Apache Casandra サービスを使用するには、サービスを適切に管理するために特定のネットワーク規則が必要です。 適切なルールが公開されているようにすることで、サービスのセキュリティを維持し、運用上の問題を防ぐことができます。

## <a name="virtual-network-service-tags"></a>仮想ネットワーク サービス タグ

Azure Firewall を使用してアウトバウンド アクセスを制限している場合は、[仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)を使用することを強くお勧めします。 Azure Managed Instance for Apache Cassandra を正常に機能させるために必要なタグを次に示します。

| 宛先サービス タグ                                                             | プロトコル | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| 記憶域 | HTTPS | 443 | コントロール プレーンの通信と構成のためにノードと Azure Storage 間で行う安全な通信に必要です。|
| AzureKeyVault | HTTPS | 443 | ノードと Azure Key Vault 間の安全な通信に必要です。 クラスター内の通信をセキュリティで保護するために証明書とキーが使用されます。|
| EventHub | HTTPS | 443 | Azure にログを転送するために必要です |
| AzureMonitor | HTTPS | 443 | Azure にメトリックを転送するために必要です |
| AzureActiveDirectory| HTTPS | 443 | Azure Active Directory の認証に必要です。|
| AzureResourceManager| HTTPS | 443 | Cassandra ノードに関する情報を収集し、ノードを管理する (再起動など) ために必要です|
| AzureFrontDoor.Firstparty| HTTPS | 443 | ログ操作に必要です。|
| GuestAndHybridManagement | HTTPS | 443 |  Cassandra ノードに関する情報を収集し、ノードを管理する (再起動など) ために必要です |
| ApiManagement  | HTTPS | 443 | Cassandra ノードに関する情報を収集し、ノードを管理する (再起動など) ために必要です |

> [!NOTE]
> 上記に加えて、関連するサービスのサービス タグが存在しない場合、104.40.0.0/13 13.104.0.0/14 40.64.0.0/10 のアドレス プレフィックスも追加する必要があります。

## <a name="user-defined-routes"></a>ユーザー定義のルート

サードパーティのファイアウォールを使用して送信アクセスを制限している場合は、独自のファイアウォール経由の接続を許可するのではなく、Microsoft アドレス プレフィックス用に[ユーザー定義ルート (UDR)](../virtual-network/virtual-networks-udr-overview.md#user-defined) を構成することをお勧めします。 ユーザー定義のルートに必要なアドレス プレフィックスを追加するサンプル [bash スクリプト](https://github.com/Azure-Samples/cassandra-managed-instance-tools/blob/main/configureUDR.sh)を参照してください。

## <a name="azure-global-required-network-rules"></a>Azure Global に必要なネットワーク規則

必要なネットワーク規則と IP アドレスの依存関係は次のとおりです。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap\<region\>.blob.core.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | コントロール プレーンの通信と構成のためにノードと Azure Storage 間で行う安全な通信に必要です。|
|\*.store.core.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | コントロール プレーンの通信と構成のためにノードと Azure Storage 間で行う安全な通信に必要です。|
|\*.blob.core.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | バックアップを格納するためにノードと Azure Storage 間で行う安全な通信に必要です。 "*バックアップ機能を変更しており、GA でストレージ名はパターンに従うことになります*"|
|vmc-p-\<region\>.vault.azure.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | ノードと Azure Key Vault 間の安全な通信に必要です。 クラスター内の通信をセキュリティで保護するために証明書とキーが使用されます。|
|management.azure.com:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Virtual Machine Scale Sets または Azure Management API | HTTPS | 443 | Cassandra ノードに関する情報を収集し、ノードを管理する (再起動など) ために必要です|
|\*.servicebus.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | Azure にログを転送するために必要です|
|jarvis-west.dc.ad.msft.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Monitor | HTTPS | 443 | メトリックを Azure に転送するために必要です |
|login.microsoftonline.com:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure AD | HTTPS | 443 | Azure Active Directory の認証に必要です。|
| packages.microsoft.com | HTTPS | 443 | Azure セキュリティ スキャナーの定義と署名の更新に必要です |
| azure.microsoft.com | HTTPS | 443 | 仮想マシン スケール セットに関する情報を取得するために必要です |
| \<region\>-dsms.dsms.core.windows.net | HTTPS | 443 | ログ記録用の証明書 |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | ログ記録に必要なログ エンドポイント |
| global.prod.microsoftmetrics.com | HTTPS | 443 | メトリックに必要です |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | セキュリティ スキャナーのダウンロードまたは更新に必要です |
| crl.microsoft.com | HTTPS | 443 | 公開されている Microsoft 証明書にアクセスするために必要です |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | 公開されている Microsoft 証明書にアクセスするために必要です |

### <a name="dns-access"></a>DNS アクセス

システムでは、ロード バランサーを使用できるように、この記事で説明されている Azure サービスへの到達に DNS 名を使用します。 そのため、仮想ネットワークでは、これらのアドレスを解決できる DNS サーバーを実行する必要があります。 仮想ネットワーク内の仮想マシンは、DHCP プロトコルを介して通信されるネーム サーバーを優先します。 ほとんどの場合、Azure では仮想ネットワークの DNS サーバーが自動的に設定されます。 このような状況が発生しない場合は、この記事で説明されている DNS 名を使用して作業を開始することをお勧めします。

## <a name="internal-port-usage"></a>内部ポートの使用

次のポートは、VNET (またはピアリングされた vnets./express ルート) 内でのみアクセスできます。 Managed Instance for Apache Cassandra インスタンスにはパブリック IP がなく、インターネットでアクセス可能にしてはなりません。

| Port | 用途 |
| ---- | --- |
| 8443 | 内部 |
| 9443 | 内部 |
| 7001 | ゴシップ - Cassandra ノードの相互通信に使用 |
| 9042 | Cassandra - クライアントが Cassandra に接続するために使用 |
| 7199 | 内部 |

## <a name="next-steps"></a>次のステップ

この記事では、サービスを適切に管理するためのネットワーク規則について説明しました。 Azure Managed Instance for Apache Cassandra の詳細については、次の記事を参照してください。

* [Azure Managed Instance for Apache Cassandra の概要](introduction.md)
* [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)
