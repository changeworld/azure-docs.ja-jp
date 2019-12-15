---
title: Azure Sentinel のオフボード | Microsoft Docs
description: Azure Sentinel インスタンスを削除する方法
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: 4c0c415235fd290bc47ac402a6b81a1afa7af903
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74777436"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>ワークスペースから Azure Sentinel を削除する

この記事では、Azure Sentinel が不要になった場合のためにワークスペースから削除する方法について説明します。

## <a name="how-to-delete-azure-sentinel"></a>Azure Sentinel を削除する方法

Azure Sentinel のインストール時、バックグラウンドでは、選択したワークスペースに **SecurityInsights** ソリューションがインストールされます。 そのため、最初にすべきことは **SecurityInsights** ソリューションを削除することです。

1.  **[Azure Sentinel]** 、 **[構成]** 、 **[ワークスペースの設定]** 、 **[ソリューション]** の順に進みます。

2.  `SecurityInsights` を選択してクリックします。

    ![SecurityInsights ソリューションを見つける](media/offboard/find-solution.png)

3.  ページの上部で **[削除]** を選択します。

    > [!IMPORTANT]
    > ワークスペースを削除すると、このワークスペースを使用している他のソリューションやデータ ソース (Azure Monitor など) に影響を与える可能性があります。 このワークスペースを使用しているソリューションを確認するには、「[インストールされている監視ソリューションを一覧表示する](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)」を参照してください。 どのソリューションのデータがワークスペースに取り込まれているかを確認するには、「[取り込まれたデータ ボリュームについて理解する](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)」を参照してください。

    ![SecurityInsights ソリューションを削除する](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>舞台裏では何が起こっているのか?

Azure Sentinel の場合、ソリューションを削除するとき、削除プロセスの最初の段階を完了するために最大 48 時間かかります。

分離が確認されると、オフボード プロセスが開始します。

**次のコネクタの構成が削除されます。**
-   Office 365

-   AWS

-   Microsoft サービス セキュリティ アラート (Azure ATP、Cloud Discovery シャドウ IT レポートを含む Microsoft Cloud App Security、Azure AD Identity Protection、Microsoft Defender ATP、Azure Security Center)

-   [脅威インテリジェンス]

-   一般的なセキュリティ ログ (CEF ベースのログ、Barracuda、Syslog を含む) (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

-   Windows セキュリティ イベント (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

最初の 48 時間以内に、Azure Sentinel のデータとアラート ルール (リアルタイム自動構成を含む) にアクセスできなくなります。また、クエリを実行できなくなります。

**30 日後、以下のリソースが削除されます。**

-   インシデント (調査メタデータを含む)

-   アラート ルール

-   ブックマーク

プレイブック、保存したブック、保存した検索クエリ、ノートブックは削除されません。 **削除したデータが原因で壊れる場合があります。これらは手動で削除できます。**

サービスの削除後、30 日間の猶予期間が与えられます。その間、ソリューションをもう一度有効にすることができます。データとアラート ルールが復元されますが、構成したが切断されたコネクタについては、もう一度接続する必要があります。

> [!NOTE]
> ソリューションを削除しても、サブスクリプションは Azure Sentinel リソース プロバイダーに登録されたままになります。 **これは手動で削除できます。**




## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Sentinel サービスを削除する方法について学習しました。 考えが変わり、もう一度インストールすることになったら、次のページを参照してください。
- [Azure Sentinel のオンボード](quickstart-onboard.md)概要

