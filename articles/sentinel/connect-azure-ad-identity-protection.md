---
title: Azure Sentinel に Azure AD Identity Protection データを接続する方法
description: Azure Sentinel に Azure AD Identity Protection データを接続する方法について説明します。
author: rkarlin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 8d916606913b19a9eb15cb280defbeb0440565e4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563719"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Azure AD Identity Protection からデータを接続する



[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) から Azure Sentinel にログをストリーミングし、Azure Sentinel にストリーミングしたアラームのダッシュボードに表示し、カスタム アラームを作成し、調査を改善できます。 Azure Active Directory Identity Protection では、危険な状態のユーザー、リスク検出、および脆弱性の統合ビューが用意されているほか、リスクを迅速に修復したり、今後のイベントを自動的に修復するようにポリシーを設定したりできます。 このサービスは、Microsoft のお客様の ID を保護する経験に基づいて構築されており、1 日 130 億を超えるログインからの信号によって高い精度を得ることができます。 


## <a name="prerequisites"></a>前提条件

- [Azure Active Directory Premium P1 または P2 ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)の所有
- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Identity Protection に接続する

Azure AD Identity Protection が既にある場合は、[お使いのネットワーク上で有効](../active-directory/identity-protection/overview-identity-protection.md)になっていることを確認します。
Azure AD Identity Protection がデプロイされ、データを取得している場合は、アラート データを Azure Sentinel に簡単にストリーミングできます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure AD Identity Protection]** タイルをクリックします。

2. **[接続]** をクリックして、Azure AD Identity Protection のイベントの Azure Sentinel へのストリーミングを開始します。


6. Azure AD Identity Protection のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure AD Identity Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
