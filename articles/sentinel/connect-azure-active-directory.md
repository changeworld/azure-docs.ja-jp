---
title: Microsoft Azure Sentinel に Azure Active Directory のデータを接続する | Microsoft Docs
description: Microsoft Azure Active Directory Domain Services からデータを収集し、Azure AD のサインイン ログ、監査ログ、プロビジョニング ログを Microsoft Azure Sentinel にストリーミングする方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 029fcc5d9df8ee16aa7727353763f0e4d12e2796
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716127"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-microsoft-sentinel"></a>Azure Active Directory (Azure AD) データを Microsoft Azure Sentinel に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> 後述のように、使用可能なログの種類の一部は現在 **プレビュー** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Azure Sentinel の組み込みコネクタを使用して、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集し、それを Microsoft Azure Sentinel にストリーミングできます。 コネクタを使用すると、次の種類のログをストリーミングできます。

- [**サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md)には、ユーザーが認証要素を提供する対話型ユーザー サインインに関する情報が含まれています。

    Azure AD コネクタには、次の 3 つの追加カテゴリのサインイン ログが含まれるようになりました。現時点ではすべて **プレビュー** 段階です。
    
    - [**対話型ではないユーザー サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)には、ユーザーからの操作や認証の要因なしで、ユーザーに代わってクライアントによって実行されるサインインに関する情報が含まれています。
    
    - [**サービス プリンシパルのサインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)には、ユーザーが関与しないアプリとサービス プリンシパルによるサインインに関する情報が含まれています。 このサインインの場合、認証またはリソースへのアクセス用の資格情報が、アプリまたはサービスによって、それ自身のために提供されます。
    
    - [**マネージド ID サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)には、Azure によってシークレットが管理されている Azure リソースによるサインインに関する情報が含まれています。 詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

- [**監査ログ**](../active-directory/reports-monitoring/concept-audit-logs.md)には、ユーザーとグループの管理、マネージド アプリケーション、およびディレクトリ アクティビティに関連するシステムの利用状況に関する情報が含まれています。

- [**プロビジョニング ログ**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (**プレビュー**) には、Azure AD プロビジョニング サービスによってプロビジョニングされたユーザー、グループ、およびロールに関するシステムアクティビティ情報が含まれています。 


## <a name="prerequisites"></a>前提条件

- サインイン ログを Microsoft Azure Sentinel に取り込むには、Azure Active Directory P1 または P2 ライセンスが必要です。 他の種類のログを取り込む場合、任意の Azure AD ライセンス (Free/O365/P1/P2) で十分です。 Azure Monitor (Log Analytics) と Microsoft Azure Sentinel には、追加のギガバイト単位の料金が適用される場合があります。

- ユーザーには、ワークスペースの Microsoft Azure Sentinel 共同作成者ロールを割り当てる必要があります。

- ユーザーには、ログをストリーミングするテナントの全体管理者またはセキュリティ管理者のロールを割り当てる必要があります。

- 接続の状態を確認できるようにするために、ユーザーは Azure AD 診断設定に対する読み取りおよび書き込みアクセス許可を持っている必要があります。 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory に接続する

1. Microsoft Azure Sentinel で、ナビゲーション メニューから **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Active Directory]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Microsoft Azure Sentinel にストリーミングするログの種類の隣のチェック ボックス (上記を参照) をオンにして、 **[接続]** を選択します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、次のテーブルの **[LogManagement]** セクションの下にある **[ログ]** にデータが表示されます。

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Azure AD ログを照会するには、クエ リ ウィンドウの上部に関連テーブル名を入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Active Directory を Microsoft Azure Sentinel に接続する方法について説明しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
