---
title: Microsoft Sentinel の削除 | Microsoft Docs
description: Microsoft Sentinel インスタンスを削除する方法。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e811cfb24505c00f521121ce846cc58da9cff2f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517351"
---
# <a name="remove-microsoft-sentinel-from-your-workspace"></a>ワークスペースから Microsoft Sentinel を削除する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel が不要になった場合にワークスペースから削除する方法について説明します。

## <a name="how-to-remove-microsoft-sentinel"></a>Microsoft Sentinel を削除する方法

次のプロセスに従ってワークスペースから Microsoft Sentinel を削除します。

1. **Microsoft Sentinel** にアクセスし、 **[設定]** をクリックして **[Microsoft Sentinel の削除]** タブを選択します。

1. Microsoft Sentinel を削除する前に、削除する理由をチェックボックスを使用してお知らせください。

1. **[ワークスペースから Microsoft Sentinel を削除する]** を選択します。
    
    ![SecurityInsights ソリューションを削除する](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>舞台裏では何が起こっているのか?

Microsoft Sentinel の場合、ソリューションを削除するには、削除プロセスの最初の段階を完了するために最大 48 時間かかります。

分離が確認されると、オフボード プロセスが開始します。

**次のコネクタの構成が削除されます。**
-   Office 365

-   AWS

-   Microsoft サービス セキュリティ アラート: Microsoft Defender for Identity ("*以前の Azure ATP*")、Cloud Discovery シャドウ IT レポートを含む Microsoft Defender for Cloud Apps、Azure AD Identity Protection、Microsoft Defender for Endpoint ("*以前の Microsoft Defender ATP*")、Microsoft Defender for Cloud からのセキュリティ アラート

-   [脅威インテリジェンス]

-   一般的なセキュリティ ログ (CEF ベースのログ、Barracuda、Syslog を含む) (Microsoft Defender for Cloud からセキュリティ アラートを取得する場合、これらのログは引き続き収集されます)

-   Windows セキュリティ イベント (Microsoft Defender for Cloud からセキュリティ アラートを取得する場合、これらのログは引き続き収集されます)

最初の 48 時間以内に、Microsoft Sentinel のデータと分析ルール (リアルタイム自動構成を含む) にアクセスできなくなります。また、クエリを実行できなくなります。

**30 日後、以下のリソースが削除されます。**

-   インシデント (調査メタデータを含む)

-   分析ルール

-   ブックマーク

プレイブック、保存したブック、保存した検索クエリ、ノートブックは削除されません。 **削除したデータが原因で壊れる場合があります。これらは手動で削除できます。**

サービスの削除後、30 日間の猶予期間が与えられます。その間、ソリューションをもう一度有効にすることができます。データと分析ルールが復元されますが、構成したが切断されたコネクタについては、もう一度接続する必要があります。

> [!NOTE]
> ソリューションを削除しても、サブスクリプションは Microsoft Sentinel リソース プロバイダーに登録されたままになります。 **これは手動で削除できます。**




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Sentinel サービスを削除する方法について学習しました。 考えが変わり、もう一度インストールすることになったら、次のページを参照してください。
- [Microsoft Sentinel のオンボード](quickstart-onboard.md)を始めます。
