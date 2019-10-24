---
title: Azure Network Watcher についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: この記事では、Azure Network Watcher サービスについてよく寄せられる質問に回答します。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: ef46c1a631a79dd1c50b2bf7d263538298de233f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333436"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure Network Watcher に関してよく寄せられる質問 (FAQ)
[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) サービスは、Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示、ログの有効化または無効化を行うツール スイートを提供します。 この記事では、そのサービスに関する一般的な質問への回答を示します。

## <a name="general"></a>全般

### <a name="what-is-network-watcher"></a>Network Watcher とは
Network Watcher は、Virtual Machines、Virtual Networks、アプリケーション ゲートウェイ、ロード バランサー、および Azure 仮想ネットワーク内のその他のリソースの IaaS (サービスとしてのインフラストラクチャ) コンポーネントのネットワーク正常性を監視および修復するように設計されています。 PaaS (サービスとしてのプラットフォーム) インフラストラクチャを監視したり、Web/モバイル分析を取得したりするためのソリューションではありません。

### <a name="what-tools-does-network-watcher-provide"></a>Network Watcher で提供されるツール
Network Watcher には、3 つの主要な機能セットがあります。
* 監視
  * [トポロジ ビュー](https://docs.microsoft.com/azure/network-watcher/view-network-topology)には、仮想ネットワーク内のリソースと、リソース間のリレーションシップが表示されます。
  * [接続モニター](https://docs.microsoft.com/azure/network-watcher/connection-monitor)では、VM と他のネットワーク リソースとの間の接続および待機時間を監視できます。
  * [Network Performance Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) を使用すると、ハイブリッド ネットワーク アーキテクチャ、ExpressRoute 回線、サービス/アプリケーション エンドポイントの全体にわたって接続と待機時間を監視できます。  
* 診断
  * [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)では、VM レベルでトラフィックのフィルター処理に関する問題を検出できます。
  * [次ホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)は、トラフィックのルートを検証してルーティングの問題を検出するのに役立ちます。
  * [接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)では、VM と他のネットワーク リソースとの間で、1 回限りの接続と待機時間のチェックが有効になります。
  * [パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)を使用すると、仮想ネットワーク内の VM でのすべてのトラフィックをキャプチャできます。
  * [VPN のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)では、VPN ゲートウェイと接続に対して複数の診断チェックを実行し、問題のデバッグに役立てることができます。
* ログの記録
  * [NSG フロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)では、[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) のすべてのトラフィックをログに記録できます。
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) では、NSG フロー ログのデータを処理して、ネットワーク トラフィックの視覚化、クエリ、分析、解釈を行うことができます。


詳細については、[Network Watcher の概要のページ](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)を参照してください。


### <a name="how-does-network-watcher-pricing-work"></a>Network Watcher の価格のしくみ
Network Watcher のコンポーネントと各コンポーネントの価格については、[価格のページ](https://azure.microsoft.com/pricing/details/network-watcher/)を参照してください。

### <a name="which-regions-is-network-watcher-available-in"></a>Network Watcher を利用できるリージョン
[Azure サービスの利用可能性のページ](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)で、最新のリージョン別の利用可能性を確認できます。

## <a name="nsg-flow-logs"></a>NSG フロー ログ

### <a name="what-does-nsg-flow-logs-do"></a>NSG フロー ログの機能
Azure ネットワーク リソースは、[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) を使用して結合および管理できます。 NSG フロー ログを使用すると、NSG を介してすべてのトラフィックに関する 5 組のフロー情報をログに記録できます。 未処理のフロー ログは Azure ストレージ アカウントに書き込まれ、必要に応じてさらに処理、分析、クエリ実行、またはエクスポートできます。

### <a name="are-there-caveats-for-using-nsg-flow-logs"></a>NSG フロー ログの使用に関する注意事項
NSG フロー ログを使用するための前提条件はありません。 ただし、2 つの制限があります。
- **サービス エンドポイントが VNET に存在してはなりません。** NSG フロー ログは、VM 上のエージェントからストレージ アカウントに出力されます。 ただし、現在のところ、ログをストレージ アカウントに直接出力することだけが可能であり、VNET に追加したサービス エンドポイントを使用することはできません。

この問題を解決するには、次の 2 つの方法があります。

*オプション 1:VNET エンドポイントを使用せずに Azure ストレージ アカウントに出力するように NSG フロー ログを再構成する*

* 次のエンドポイントがあるサブネットを見つけます。

    - Azure portal の上部にあるグローバル検索で **[リソース グループ]** を検索します
    - 作業中の NSG を含むリソース グループに移動します
    - 2 つ目のドロップダウンを使用して、種類でフィルター処理し、 **[仮想ネットワーク]** を選択します
    - サービス エンドポイントを含む仮想ネットワークをクリックします
    - 左側のウィンドウの **[設定]** で **[サービス エンドポイント]** を選択します
    - **Microsoft.Storage** が有効なサブネットをメモします

* サービス エンドポイントを無効にします。

    - 上記の手順を続行し、左側のウィンドウの **[設定]** で **[サブネット]** を選択します
    * サービス エンドポイントを含むサブネットをクリックします
    - **[サービス エンドポイント]** セクションで、 **[サービス]** の **Microsoft.Storage** をオフにします

数分後にストレージ ログを確認できます。TimeStamp が更新されていることや新しい JSON ファイルが作成されていることがわかります。

*オプション 2:NSG フロー ログを無効にする*

Microsoft.Storage サービス エンドポイントが必要な場合は、NSG フロー ログを無効にする必要があります。


- **ストレージ アカウントにはファイアウォールを使用できません**。内部的な制限により、NSG フロー ログがストレージ アカウントで機能するためには、パブリック インターネット経由でストレージ アカウントにアクセスできる必要があります。 それでもトラフィックは Azure を通じて内部でルーティングされ、追加のエグレス料金は発生しません。

この問題は、"すべてのネットワーク" がストレージ アカウントにアクセスできるようにすることで解決します。

* [NSG フロー ログの概要ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)で NSG を探して、ストレージ アカウントの名前を見つけます
* ポータルのグローバル検索でストレージ アカウントの名前を入力して、ストレージ アカウントに移動します
* **[設定]** セクションで、 **[ファイアウォールと仮想ネットワーク]** を選択します
* **[すべてのネットワーク]** を選択して保存します。 既に選択されている場合、変更は必要ありません。  

これらの両方の制限は、2020 年 1 月までに対処される予定です。

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>フロー ログのバージョン 1 と 2 の違い
フロー ログ バージョン 2 では、"*フロー状態*" という概念が導入されており、転送するバイトとパケットに関する情報が保存されます。 詳細については、[こちら](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)を参照してください。

## <a name="next-steps"></a>次の手順
 - Network Watcher の使用を開始するためのいくつかのチュートリアルについては、[ドキュメントの概要のページ](https://docs.microsoft.com/azure/network-watcher/)を参照してください。
