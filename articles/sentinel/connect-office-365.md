---
title: Azure Sentinel に Office 365 データを接続する | Microsoft Docs
description: Azure Sentinel に Office 365 データを接続する方法について説明します。
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
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252513"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 ログからデータを接続する



[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) から Azure Sentinel にシングル クリックで監査ログをストリーミングできます。 Office 365 の監査ログを、同じテナントの Azure Sentinel ワークスペースにストリーミングすることができます。 Office 365 アクティビティ ログのコネクタは、継続的なユーザー アクティビティに分析情報を提供します。 Office 365 からさまざまなユーザー、管理者、システム、ポリシーのアクションとイベントに関する情報を取得します。 Office 365 のログをAzure Sentinel に接続することで、このデータを使用して、ダッシュボードの表示、カスタムのアラートの作成、調査プロセスの向上を実現できます。

> [!IMPORTANT]
> E3 ライセンスがある場合、Office 365 Management Activity API を使用してデータにアクセスするには、Office 365 組織で統合監査ログを有効にする必要があります。 これを行うには、Office 365 監査ログを有効にします。 手順については、「[Turn Office 365 audit log search on or off](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)」 (Office 365 監査ログの検索を有効または無効にする) を参照してください。 詳細については、「[Office 365 管理アクティビティ API リファレンス](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)」を参照してください。

## <a name="prerequisites"></a>前提条件

- テナントのグローバル管理者またはセキュリティ管理者である必要があります。
- テナントで統合監査が有効になっている必要があります。 Office 365 E3 または E5 ライセンスを使用するテナントでは、統合監査が既定で有効になっています。 <br>テナントにこれらのライセンスのどちらもない場合は、次のいずれかの方法を使用して、テナントで統合監査を有効にする必要があります。
    - [Set-AdminAuditLogConfig コマンドレットを使用](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)して、パラメーター "UnifiedAuditLogIngestionEnabled" を有効にします。
    - [セキュリティ/コンプライアンス センターの UI を使用します](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>Office 365 への接続

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Office 365]** タイルをクリックします。

2. これをまだ有効にしていない場合は、 **[データ コネクタ]** ブレードに移動し、**Office 365** コネクタを選択します。 ここで、 **[コネクタ ページを開く]** をクリックし、 **[構成]** というラベルの構成セクションで、Azure Sentinel に接続する Office 365 アクティビティ ログをすべて選択することができます。 
   > [!NOTE]
   > Azure Sentinel で以前サポートされていたバージョンの Office 365 コネクタで複数のテナントを既に接続している場合は、各テナントから収集したログを表示して変更することができます。 テナントをさらに追加することはできませんが、以前に追加したテナントを削除することはできます。
3. Log Analytics で Office 365 のログに関連するスキーマを使用するために、**OfficeActivity** を検索します。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

