---
title: Azure HDInsight でネットワーク仮想アプライアンスを構成する
description: Azure HDInsight でネットワーク仮想アプライアンスに関するいくつかの追加機能を構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 805be8d5c9ab4f6316251adbb9bce3e99f4fa01d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086672"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight でネットワーク仮想アプライアンスを構成する

> [!Important]
> 次の情報が必要なのは、Azure Firewall 以外のネットワーク仮想アプライアンス (NVA) を構成する場合**のみ**です。

Azure Firewall は自動的に、多くの一般的な重要なシナリオのトラフィックを許可するように構成されます。 別のネットワーク仮想アプライアンスを使用するには、いくつかの追加機能を構成する必要があります。 ネットワーク仮想アプライアンスを構成する場合は、次の要因にご注意ください。

* サービス エンドポイント対応のサービスは、サービス エンドポイントを使用して構成できます。これにより、通常はコストやパフォーマンスに関する考慮事項のために NVA がバイパスされます。
* IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています。
* FQDN HTTP/HTTPS エンドポイントは、NVA デバイスでホワイトリストに登録できます。
* 作成したルート テーブルを HDInsight サブネットに割り当てます。

## <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係

必要に応じて、次の 1 つ以上のサービス エンドポイントを有効にすることができます。これにより、NVA がバイパスされます。 このオプションは、大量のデータ転送のコストを節約するだけでなく、パフォーマンスの最適化にも役立ちます。 

| **エンドポイント** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP アドレスの依存関係

| **エンドポイント** | **詳細** |
|---|---|
| [こちら](hdinsight-management-ip-addresses.md)で発行された IP | これらの IP は HDInsight コントロール プレーン用であり、非対称ルーティングを回避するために UDR に含める必要があります |
| AAD-DS プライベート IP | ESP クラスターにのみ必要です|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係

> [!Important]
> 以下の一覧には、いくつかの最も重要な FQDN のみを示しています。 NVA を構成するための FQDN の完全な一覧 (ほとんどは Azure Storage と Azure Service Bus) は[このファイルで](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)取得できます。 これらの依存関係は、クラスターを正常に作成するために HDInsight コントロール プレーン操作によって使用されます。

| **エンドポイント**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>次のステップ

* [ファイアウォールを使用したアウトバウンド トラフィックの制限](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
