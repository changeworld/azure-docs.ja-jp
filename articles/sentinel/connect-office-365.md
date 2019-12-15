---
title: Azure Sentinel に Office 365 データを接続する | Microsoft Docs
description: Azure Sentinel に Office 365 データを接続する方法について説明します。
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815786"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 ログからデータを接続する



[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) から Azure Sentinel にシングル クリックで監査ログをストリーミングできます。 複数のテナントから Azure Sentinel の 1 つのワークスペースに監査ログをストリーミングできます。 Office 365 アクティビティ ログのコネクタは、継続的なユーザー アクティビティに分析情報を提供します。 Office 365 からさまざまなユーザー、管理者、システム、ポリシーのアクションとイベントに関する情報を取得します。 Office 365 のログをAzure Sentinel に接続することで、このデータを使用して、ダッシュボードの表示、カスタムのアラートの作成、調査プロセスの向上を実現できます。

> [!IMPORTANT]
> E3 ライセンスがある場合、Office 365 Management Activity API を使用してデータにアクセスするには、Office 365 組織で統合監査ログを有効にする必要があります。 これを行うには、Office 365 監査ログを有効にします。 手順については、「[Turn Office 365 audit log search on or off](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)」 (Office 365 監査ログの検索を有効または無効にする) を参照してください。 詳細については、「[Office 365 管理アクティビティ API リファレンス](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)」を参照してください。

## <a name="prerequisites"></a>前提条件

- テナントのグローバル管理者またはセキュリティ管理者である
- 接続を作成するために Azure Sentinel にログインしているコンピューターで、Web トラフィックに対してポート 4433 が開かれていることを確認します。 このポートは、接続が正常に確立された後に再び閉じることができます。
- テナントに Office 365 E3 または Office 365 E5 ライセンスがない場合は、次のいずれかのプロセスを使用して、テナントで統合監査を有効にする必要があります。
    - [Set-AdminAuditLogConfig コマンドレットを使用](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)して、パラメーター "UnifiedAuditLogIngestionEnabled" を有効にします。
    - [または、セキュリティ/コンプライアンス センターの UI を使用します](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>Office 365 への接続

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Office 365]** タイルをクリックします。

2. これをまだ有効にしていない場合は、 **[データ コネクタ]** ブレードに移動し、**Office 365** コネクタを選択します。 ここで、 **[コネクタ ページを開く]** をクリックし、 **[ワークスペースで Office 365 ソリューションを有効にする]** というラベルの構成セクションで、 **[ソリューションのインストール]** ボタンを使用して有効にすることができます。 既に有効な場合は、既に有効になっていることが接続画面で識別されます。
1. Office 365 により、複数のテナントから Azure Sentinel にデータをストリーミングできます。 接続先のテナントごとに、 **[Connect tenants to Azure Sentinel]\(Azure Sentinel にテナントを接続\)** でテナントを追加します。 
1. Active Directory Domain Services の画面が開きます。 Azure Sentinel に接続するテナントごとにグローバル管理者ユーザーによる認証を求められるので、ログを読み取るための Azure Sentinel へのアクセス許可を付与します。 
5. テナントの一覧に、Azure AD directory ID (テナント ID) と、Exchange ログおよび Sharepoint ログの 2 つのチェックボックスが表示されます。 表示されているいずれかまたはすべてのサービスを選択して、Sentinel に取り込むことができます。 現時点では、Azure Sentinel は、既存の Office365 サービス内で Exchange と SharePoint のログをサポートしています。

4. サービス (Exchange、SharePoint など) を選択したら、ページのテナントの追加フレームで [保存] をクリックします。 

3. Log Analytics で Office 365 のログに関連するスキーマを使用するために、**OfficeActivity** を検索します。


## <a name="next-steps"></a>次の手順
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

