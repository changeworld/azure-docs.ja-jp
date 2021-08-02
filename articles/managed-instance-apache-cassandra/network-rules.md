---
title: Azure Managed Instance for Apache Cassandra に必要なアウトバウンド ネットワーク規則
description: Azure Managed Instance for Apache Cassandra に必要なアウトバウンド ネットワーク規則と FQDN について説明します
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: 8c66657832000d1d0f9e1e9d842f16ce149e314a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955964"
---
# <a name="required-outbound-network-rules"></a>必要なアウトバウンド ネットワーク規則

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Managed Instance for Apache Casandra サービスを使用するには、サービスを適切に管理するために特定のネットワーク規則が必要です。 適切なルールが公開されているようにすることで、サービスのセキュリティを維持し、運用上の問題を防ぐことができます。

## <a name="azure-global-required-network-rules"></a>Azure Global に必要なネットワーク規則

必要なネットワーク規則と IP アドレスの依存関係は次のとおりです。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap`<region>`.blob.core.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | コントロール プレーンの通信と構成のためにノードと Azure Storage 間で行う安全な通信に必要です。|
|*.blob.core.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | バックアップを格納するためにノードと Azure Storage 間で行う安全な通信に必要です。 "*バックアップ機能を変更しており、GA でストレージ名はパターンに従うことになります*"|
|vmc-p-`<region>`.vault.azure.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) � Azure KeyVault | HTTPS | 443 | ノードと Azure Key Vault 間の安全な通信に必要です。 クラスター内の通信をセキュリティで保護するために証明書とキーが使用されます。|
|management.azure.com:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) � Azure Virtual Machine Scale Sets または Azure Management API | HTTPS | 443 | Cassandra ノードに関する情報を収集し、ノードを管理する (再起動など) ために必要です|
|*.servicebus.windows.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) � Azure EventHub | HTTPS | 443 | Azure にログを転送するために必要です|
|jarvis-west.dc.ad.msft.net:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) � Azure Monitor | HTTPS | 443 | メトリックを Azure に転送するために必要です |
|login.microsoftonline.com:443</br> または</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) � Azure AD | HTTPS | 443 | Azure Active Directory の認証に必要です。|
| packages.microsoft.com | HTTPS | 443 | Azure セキュリティ スキャナーの定義と署名の更新に必要です |

## <a name="managed-instance-for-apache-cassandra-internal-port-usage"></a>Managed Instance for Apache Cassandra の内部ポートの使用

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