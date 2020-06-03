---
title: Azure HDInsight でネットワーク仮想アプライアンスを構成する
description: Azure HDInsight でネットワーク仮想アプライアンスに関するいくつかの追加機能を構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868280"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight でネットワーク仮想アプライアンスを構成する

> [!Important]
> 次の情報が必要なのは、Azure Firewall 以外のネットワーク仮想アプライアンス (NVA) を構成する場合**のみ**です。

Azure Firewall は自動的に、多くの一般的な重要なシナリオのトラフィックを許可するように構成されます。 別のネットワーク仮想アプライアンスを使用するには、いくつかの追加機能を構成する必要があります。 ネットワーク仮想アプライアンスを構成する場合は、次の要因にご注意ください。

* サービス エンドポイント対応のサービスは、サービス エンドポイントを使用して構成する必要があります。
* IP アドレスの依存関係が HTTP/S 以外のトラフィック (TCP トラフィックと UDP トラフィックの両方) に対応しています。
* FQDN HTTP/HTTPS エンドポイントは、NVA デバイスに配置することができます。
* ワイルドカード HTTP/HTTPS エンドポイントは、さまざまな修飾子に応じて変わる場合がある依存関係です。
* 作成したルート テーブルを HDInsight サブネットに割り当てます。

## <a name="service-endpoint-capable-dependencies"></a>サービス エンドポイント対応の依存関係

| **エンドポイント** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP アドレスの依存関係

| **エンドポイント** | **詳細** |
|---|---|
| \*:123 | NTP クロック チェック。 トラフィックは、ポート 123 上の複数のエンドポイントでチェックされます |
| [こちら](hdinsight-management-ip-addresses.md)で発行された IP | これらの IP は HDInsight サービスです |
| ESP クラスター用の AAD DS プライベート IP |
| \*:KMS Windows ライセンス認証の場合は 16800 |
| \*Log Analytics の場合は 12000 |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS の依存関係

> [!Important]
> 以下の一覧には、いくつかの最も重要な FQDN のみを示しています。 NVA を構成するための追加の FQDN (ほとんど Azure Storage と Azure Service Bus) は[このファイルで](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)取得できます。

| **エンドポイント**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>次のステップ

* [ファイアウォールを使用したアウトバウンド トラフィックの制限](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
