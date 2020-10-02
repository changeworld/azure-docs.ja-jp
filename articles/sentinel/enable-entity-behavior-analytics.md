---
title: エンティティ動作分析を利用し、高度な脅威を検出する | Microsoft Docs
description: Azure Sentinel でユーザーとエンティティの動作分析を有効にし、データ ソースを構成する
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993262"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Azure Sentinel でのユーザーとエンティティの動作分析の有効化 (UEBA) 



## <a name="prerequisites"></a>前提条件

- UEBA を有効化または無効化するには (ただし、実行しない)、Azure AD で**全体管理者**ロールまたは**セキュリティ管理者**ロールに割り当てられている必要があります。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>ユーザーとエンティティの動作分析を有効にする方法

1. Azure Sentinel のナビゲーション メニューから、 **[Entity behavior (preview)]\(エンティティ動作 (プレビュー)\)** を選択します。

1. **[有効にする]** という見出しの下でトグルを **[オン]** に切り替えます。

1. **[Select data sources]\(データ ソースの選択\)** ボタンをクリックします。

1. **[データ ソースの選択]** ウィンドウで、UEBA を有効にするデータ ソースの横にあるチェック ボックスを選択し、 **[適用]** を選択します。

    > [!NOTE]
    >
    > **[データ ソースの選択]** ウィンドウの下半分に、まだ有効にしていない UEBA 対応データ ソースの一覧が表示されます。 
    >
    > UEBA を有効にすると、新しいデータ ソースに接続するとき、UEBA 対応であれば、データ コネクタ ウィンドウから直接、データ ソースの UEBA を有効にできるようになります。

1. **[Go to entity search]\(エンティティ検索に進む\)** を選択します。 これでエンティティ検索ウィンドウが表示されます。今後、Azure Sentinel のメイン メニューから **[Entity behavior]\(エンティティ動作\)** を選択したとき、これが表示されます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel でユーザーとエンティティの動作分析 (UEBA) を有効にし、構成する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
