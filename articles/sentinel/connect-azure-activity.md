---
title: Azure Sentinel Preview に Azure アクティビティ データを接続する | Microsoft Docs
description: Azure Sentinel に Azure アクティビティ データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 67e4bb2d0f308a3f20f0a29ec8b1f2a0d46fc082
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204354"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure アクティビティ ログからデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)から Azure Sentinel にシングル クリックでログをストリーミングできます。 アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントの分析情報を提供するサブスクリプション ログです。 たとえば、Azure Resource Manager の運用データから、サービスの正常性イベントまでの範囲のデータが含まれています。 アクティビティ ログを使用して、お使いのサブスクリプションのリソースに対して発生したすべての書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック/"RDFE" モデルを使用しているリソースに対する操作は含まれません。 


## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー


## <a name="connect-to-azure-activity-log"></a>Azure アクティビティ ログに接続する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure アクティビティ ログ]** タイルをクリックします。

2. [Azure アクティビティ ログ] ウィンドウで、Azure Sentinel にストリーミングするサブスクリプションを選択します。 

3. **[接続]** をクリックします。

4. Activity アクティビティのアラートで Log Analytics の関連スキーマを使用するには、**AzureActivity** を検索します。


 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure アクティビティ ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
