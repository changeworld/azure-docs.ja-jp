---
title: Microsoft Sentinel 分析ルールのインポートとエクスポート | Microsoft Docs
description: デプロイを支援するために ARM テンプレート間で分析ルールをエクスポートおよびインポートします
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c2b94a19c900005d3a03f193f95cd249e2f4eaf9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720931"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>ARM テンプレート間で分析ルールをエクスポートおよびインポートします

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - ルールのエクスポートとインポートは **プレビュー** 中です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="introduction"></a>はじめに

Microsoft Sentinel デプロイをコードとして管理および制御する一環として、分析ルールを Azure Resource Manager (ARM) テンプレート ファイルからエクスポートし、それらのルールを同様のファイルにインポートできます。 エクスポート操作により、ブラウザーのダウンロード場所に JSON ファイル (*Azure_Sentinel_analytic_rule.json* という名前) が作成されます。このファイルは、ファイル名の変更や移動など、他のファイルと同様に処理することができます。

エクスポートされた JSON ファイルはワークスペースに依存しないので、他のワークスペースや他のテナントにもインポートできます。 コードとして、マネージド CI/CD フレームワークでバージョン管理、更新、デプロイすることもできます。

ファイルには、分析ルールで定義されているすべてのパラメーターが含まれます。そのため、**Scheduled** ルールの場合は、基本的なクエリに加え、それに付随するスケジュール設定、重大度、インシデントの作成、イベントとアラートのグループ化設定、割り当てられた MITRE ATT&CK 戦術なども含まれます。 **Scheduled** だけでなく、すべての種類の分析ルールを JSON ファイルにエクスポートできます。

## <a name="export-rules"></a>ルールのエクスポート

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. エクスポートするルールを選択し、画面の上部にあるバーから **[エクスポート]** をクリックします。

    :::image type="content" source="./media/import-export-analytics-rules/export-rule.png" alt-text="分析ルールのエクスポート" lightbox="./media/import-export-analytics-rules/export-rule.png":::

    > [!NOTE]
    > - エクスポートに対して一度に複数の分析ルールを選択するには、ルールの横にあるチェック ボックスをオンにし、最後に **[エクスポート]** をクリックします。
    >
    > - **[エクスポート]** をクリックする前に、ヘッダー行 ( **[重大度**] の横) のチェック ボックスをオンにすることで、表示グリッド 1 ページにあるすべてのルールを一度にエクスポートできます。 ただし、一度に複数のページのルールをエクスポートすることはできません。
    >
    > - このシナリオでは、1 つのファイル (*Azure_Sentinel_analytic_ **rules**.json* という名前) が作成され、エクスポートされたすべてのルールの JSON コードがそのファイルに書き込まれます。

## <a name="import-rules"></a>ルールのインポート

1. 分析ルールの ARM テンプレート JSON ファイルを準備します。

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. 画面上部にあるバーから **[インポート]** をクリックします。 表示されるダイアログ ボックスで、インポートするルールが含まれる JSON ファイルに移動して選択し、 **[開く]** を選択します。

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="分析ルールのインポート" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > 1 つの ARM テンプレート ファイルから **最大 50 個** の分析ルールをインポートできます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、ARM テンプレート間で分析ルールをエクスポートおよびインポートする方法について学習しました。
- [カスタム スケジュール済みルール](detect-threats-custom.md)を含む、[分析ルール](detect-threats-built-in.md)の詳細を確認します。
- [ARM テンプレート](../azure-resource-manager/templates/overview.md)の詳細を確認します。
