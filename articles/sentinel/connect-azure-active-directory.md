---
title: Azure Sentinel に Azure Active Directory のデータを接続する | Microsoft Docs
description: Azure Active Directory からデータを収集し、Azure AD のサインイン ログと監査ログを Azure Sentinel にストリーミングする方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802250"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) からデータを接続する

Azure Sentinel の組み込みコネクタを使用して、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集し、それを Azure Sentinel にストリーミングできます。 コネクタを使用すると、[サインイン ログ](../active-directory/reports-monitoring/concept-sign-ins.md)と[監査ログ](../active-directory/reports-monitoring/concept-audit-logs.md)をストリーミングできます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel にサインイン ログを取り込むには、[Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) サブスクリプションが必要です。 Azure Monitor (Log Analytics) と Azure Sentinel には、追加のギガバイト単位の料金が適用される場合があります。

- ユーザーには、ワークスペースの Azure Sentinel 共同作成者ロールを割り当てる必要があります。

- ユーザーには、ログをストリーミングするテナントの全体管理者またはセキュリティ管理者のロールを割り当てる必要があります。

- 接続の状態を確認できるようにするために、ユーザーは Azure AD 診断設定に対する読み取りおよび書き込みアクセス許可を持っている必要があります。 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Active Directory]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Azure Sentinel にストリーミングする種類のログの隣のチェック ボックスをオンにして、 **[接続]** をクリックします。 次の種類のログから選択できます。

    - **サインイン ログ**:マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **[監査ログ]** :ユーザーとグループの管理、マネージド アプリケーション、ディレクトリ アクティビティに関するシステム アクティビティ情報。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、次のテーブルの **[LogManagement]** セクションの下にある **[ログ]** にデータが表示されます。

- `SigninLogs`
- `AuditLogs`

Azure AD ログを照会するには、クエ リ ウィンドウの上部に関連テーブル名を入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Active Directory を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
