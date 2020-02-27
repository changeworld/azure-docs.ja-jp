---
title: Azure Sentinel に Azure アクティビティ データを接続する | Microsoft Docs
description: Azure Sentinel に Azure アクティビティ データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588605"
---
# <a name="connect-data-from-azure-activity-log"></a>Azure アクティビティ ログからデータを接続する



[Azure アクティビティ ログ](../azure-monitor/platform/platform-logs-overview.md)から Azure Sentinel にシングル クリックでログをストリーミングできます。 アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントの分析情報を提供するサブスクリプション ログです。 たとえば、Azure Resource Manager の運用データから、サービスの正常性イベントまでの範囲のデータが含まれています。 アクティビティ ログを使用して、お使いのサブスクリプションのリソースに対して発生したすべての書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック/"RDFE" モデルを使用しているリソースに対する操作は含まれません。 


## <a name="prerequisites"></a>前提条件

- Log Analytics ワークスペースに対する共同作成者のアクセス許可を持つユーザー 


## <a name="connect-to-azure-activity-log"></a>Azure アクティビティ ログに接続する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure アクティビティ ログ]** タイルをクリックします。

2. [Azure アクティビティ ログ] ウィンドウで、Azure Sentinel にストリーミングするサブスクリプションを選択します。 

3. **[Connect]** をクリックします。

4. Activity アクティビティのアラートで Log Analytics の関連スキーマを使用するには、**AzureActivity** を検索します。


 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure アクティビティ ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
