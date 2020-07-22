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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 37106517c47c86f4a4a562eebd6d120e31e22334
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564521"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) からデータを接続する



Azure Sentinel では、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集して、それを Azure Sentinel にストリーミングできます。 ストリームは[サインイン ログ](../active-directory/reports-monitoring/concept-sign-ins.md) と [監査ログ](../active-directory/reports-monitoring/concept-audit-logs.md) を選択できます。

## <a name="prerequisites"></a>前提条件

- サインイン データを Azure AD からエクスポートするには、Azure AD P1 または P2 ライセンスが必要です。

- ログからストリーミングするテナントに対してグローバル管理者アクセス許可またはセキュリティ管理者アクセス許可を持つユーザー。

- 接続の状態を確認できるようにするには、Azure AD の診断ログにアクセスするためのアクセス許可が必要です。 


## <a name="connect-to-azure-active-directory"></a>Azure Active Directory に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Active Directory]** を選択し、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. Azure Sentinel にストリーミングするログの隣のチェック ボックスをオンにして、 **[接続]** をクリックします。

1. Azure AD のアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. Azure AD のアラートのクエリを実行するために Log Analytics 内の関連スキーマを使用するには、クエリ ウィンドウで「`SigninLogs`」または「`AuditLogs`」と入力します。




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Active Directory を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
