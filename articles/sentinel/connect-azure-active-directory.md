---
title: Azure Sentinel プレビューで Azure AD データを収集する | Microsoft Docs
description: Azure Sentinel で Azure Active Directory データを収集する方法を説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: b42fb982abb916503d530d5040641ba1dcce3c92
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575132"
---
# <a name="collect-data-from-azure-active-directory"></a>Azure Active Directory からのデータの収集

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel では、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集して、それを Azure Sentinel にストリーミングできます。 ストリームは[サインイン ログ](../active-directory/reports-monitoring/concept-sign-ins.md) と [監査ログ](../active-directory/reports-monitoring/concept-audit-logs.md) を選択できます。

## <a name="prerequisites"></a>前提条件

- サインイン データを Active Directory からエクスポートするには、Azure AD P1 または P2 ライセンスが必要です。

- ログからストリーミングするテナントに対してグローバル管理者アクセス許可またはセキュリティ管理者アクセス許可を持つユーザー。


## <a name="connect-to-azure-ad"></a>Azure への接続

1. Azure Sentinel で **[データ収集]** を選択し、**[Azure Active Directory]** タイルをクリックします。

2. Azure Sentinel にストリーミングするログの隣の **[接続する]** をクリックします。

6. Azure AD アラートで Log Analytics 内の関連スキーマを使用するには、**SigninLogs** と **AuditLogs** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure AD を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md) を開始する。
