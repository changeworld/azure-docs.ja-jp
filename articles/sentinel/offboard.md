---
title: Azure Sentinel の削除 | Microsoft Docs
description: Azure Sentinel インスタンスを削除する方法
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581686"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>ワークスペースから Azure Sentinel を削除する

この記事では、Azure Sentinel が不要になった場合のためにワークスペースから削除する方法について説明します。

## <a name="how-to-remove-azure-sentinel"></a>Azure Sentinel を削除する方法

次のプロセスに従って、ワークスペースから Azure Sentinel を削除します。

1. **Azure Sentinel** にアクセスし、 **[設定]** をクリックして、 **[Azure Sentinel の削除]** タブを選択します。

1. Azure Sentinel を削除する前に、削除する理由をチェックボックスを使用してお知らせください。

1. **[ワークスペースから Azure Sentinel を削除する]** を選択します。
    
    ![SecurityInsights ソリューションを削除する](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>舞台裏では何が起こっているのか?

Azure Sentinel の場合、ソリューションを削除するには、削除プロセスの最初の段階を完了するために最大 48 時間かかります。

分離が確認されると、オフボード プロセスが開始します。

**次のコネクタの構成が削除されます。**
-   Office 365

-   AWS

-   Microsoft サービス セキュリティ アラート (Azure ATP、Cloud Discovery シャドウ IT レポートを含む Microsoft Cloud App Security、Azure AD Identity Protection、Microsoft Defender ATP、Azure Security Center)

-   [脅威インテリジェンス]

-   一般的なセキュリティ ログ (CEF ベースのログ、Barracuda、Syslog を含む) (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

-   Windows セキュリティ イベント (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

最初の 48 時間以内に、Azure Sentinel のデータと分析ルール (リアルタイム自動構成を含む) にアクセスできなくなります。また、クエリを実行できなくなります。

**30 日後、以下のリソースが削除されます。**

-   インシデント (調査メタデータを含む)

-   分析ルール

-   ブックマーク

プレイブック、保存したブック、保存した検索クエリ、ノートブックは削除されません。 **削除したデータが原因で壊れる場合があります。これらは手動で削除できます。**

サービスの削除後、30 日間の猶予期間が与えられます。その間、ソリューションをもう一度有効にすることができます。データと分析ルールが復元されますが、構成したが切断されたコネクタについては、もう一度接続する必要があります。

> [!NOTE]
> ソリューションを削除しても、サブスクリプションは Azure Sentinel リソース プロバイダーに登録されたままになります。 **これは手動で削除できます。**




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel サービスを削除する方法について学習しました。 考えが変わり、もう一度インストールすることになったら、次のページを参照してください。
- [Azure Sentinel のオンボード](quickstart-onboard.md)概要
