---
title: "Log Analytics の Azure Networking Analytics ソリューション | Microsoft Docs"
description: "Log Analytics の Azure Networking Analytics ソリューションを使用すると、Azure ネットワーク セキュリティ グループのログと Azure Application Gateway のログを調査することができます。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed3bd763edb94d7bea28b4039c03afa7359fee1


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Log Analytics の Azure Networking Analytics (プレビュー) ソリューション
> [!NOTE]
> これは[プレビュー ソリューション](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)です。
> 
> 

Log Analytics の Azure Networking Analytics ソリューションを使用すると、Azure Application Gateway のログと Azure ネットワーク セキュリティ グループのログを調査することができます。

Azure Application Gateway と Azure ネットワーク セキュリティ グループのログを記録することができます。 これらのログは Blob Storage に書き込まれ、そこで Log Analytics によってインデックスが作成されて検索や分析が可能となります。

Application Gateway に関しては、次のログがサポートされます。

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

ネットワーク セキュリティ グループに関しては、次のログがサポートされます。

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Networking Analytics ソリューションのインストールと構成は、次の手順で行います。

1. 監視するリソースの診断ログを有効にします。
   * [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
   * [ネットワーク セキュリティ グループ](../virtual-network/virtual-network-nsg-manage-log.md)
2. Blob Storage からログを読み取るための設定を Log Analytics に対して行います。[Blob Storage 内の JSON ファイル](log-analytics-azure-storage-json.md)に関するページで説明されている手順に従ってください。
3. 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」に説明されている手順に従って Azure Networking Analytics ソリューションを有効にします。  

特定のリソース タイプの診断ログを有効にしなかった場合、そのリソースに対応するダッシュボードのブレードは空白になります。

## <a name="review-azure-networking-analytics-data-collection-details"></a>Azure Networking Analytics データ収集の詳細の確認
Azure Application Gateway とネットワーク セキュリティ グループの診断ログは、Azure Networking Analytics ソリューションによって Azure Blob Storage から収集されます。
データ収集のためのエージェントは不要です。

次の表は、Azure Networking Analytics におけるデータの収集に関して、その手段と各種情報をまとめたものです。

| Platform | 直接エージェント | Systems Center Operations Manager (SCOM) エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![いいえ](./media/log-analytics-azure-networking/oms-bullet-red.png) |![いいえ](./media/log-analytics-azure-networking/oms-bullet-red.png) |![あり](./media/log-analytics-azure-networking/oms-bullet-green.png) |![なし](./media/log-analytics-azure-networking/oms-bullet-red.png) |![なし](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 分 |

## <a name="use-azure-networking-analytics"></a>Azure Networking Analytics の使用
ソリューションのインストール後は、監視対象となる Application Gateway のクライアント エラーとサーバー エラーを、Log Analytics の **[概要]** ページの **[Azure Networking Analytics]** タイルに集約して表示できます。

![[Azure Networking Analytics] タイルの画像](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

**[概要]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示することができます。

* Application Gateway のアクセス ログ
  * Application Gateway のアクセス ログにおけるクライアント エラーとサーバー エラー
  * 1 時間あたりの要求数 (Application Gateway ごと)
  * 1 時間あたりの失敗した要求数 (Application Gateway ごと)
  * Application Gateway のユーザー エージェントごとのエラー数
* Application Gateway のパフォーマンス
  * Application Gateway のホストの正常性
  * Application Gateway の失敗した要求数の最大と 95 パーセンタイル
* ネットワーク セキュリティ グループのブロック済みフロー数
  * ネットワーク セキュリティ グループの規則とブロック済みフロー数
  * MAC アドレスとブロック済みフロー数
* ネットワーク セキュリティ グループの許可済みフロー数
  * ネットワーク セキュリティ グループの規則と許可済みフロー数
  * MAC アドレスと許可済みフロー数

![Azure Networking Analytics ダッシュボードの画像](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Azure Networking Analytics ダッシュボードの画像](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Azure Networking Analytics ダッシュボードの画像](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Azure Networking Analytics ダッシュボードの画像](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>ログ集計の詳細を表示するには
1. **[概要]** ページの **[Azure Networking Analytics]** タイルをクリックします。
2. **[Azure Networking Analytics]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。
   
    どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="next-steps"></a>次のステップ
* [Log Analytics のログ検索機能](log-analytics-log-searches.md)を使用して、詳細な Azure Networking Analytics データを確認してください。




<!--HONumber=Nov16_HO3-->


