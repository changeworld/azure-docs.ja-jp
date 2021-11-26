---
title: Microsoft Sentinel でアラートの詳細をカスタマイズする | Microsoft Docs
description: アラートの内容に基づいて、アラートの名前と説明を指定する方法を、その重大度と割り当てられた戦術と共にカスタマイズします。
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
ms.openlocfilehash: 8cbffe40ac7773401535eed3d249f0e13903b1e7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721406"
---
# <a name="customize-alert-details-in-microsoft-sentinel"></a>Microsoft Sentinel でアラートの詳細をカスタマイズする 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - アラートの詳細機能は **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="introduction"></a>はじめに

スケジュールされた分析ルールの名前と説明を定義し、重大度と MITRE ATT&CK の戦術を割り当てると、特定のルールによって生成されたすべてのアラートと、結果として作成されたすべてのインシデントが、アラートの特定のインスタンスの特定の内容に関係なく、同じ名前や説明などで表示されます。

**アラートの詳細** 機能を使用すると、アラートの外観をその内容に合わせて調整できます。 ここではアラート内のパラメーターを選択できます。これは、アラートの各インスタンスの名前または説明で表されることも、アラートのそのインスタンスに割り当てられた戦術と重大度が含まれることもあります。 選択したパラメーターに値がない (戦術と重大度の場合は、無効な値がある) 場合、アラートの詳細はウィザードの最初のページで指定された既定値に戻ります。

以下で詳しく説明する手順は、分析ルールの作成ウィザードの一部です。 ここでは、既存の分析ルールでアラートの詳細を追加または変更するシナリオに対処するために、個別に扱います。

## <a name="how-to-customize-alert-details"></a>アラートの詳細をカスタマイズする方法

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. スケジュールされたクエリ ルールを選択し、 **[編集]** をクリックします。 または、画面の上部にある **[作成] > [スケジュール済みクエリ ルール]** をクリックして新しいルールを作成します。

1. **[ルールのロジックを設定]** タブをクリックします。

1. **[Alert enhancement (Preview)]\(アラートの拡張 (プレビュー\))** セクションで、 **[アラートの詳細]** を展開します。

    :::image type="content" source="media/customize-alert-details/alert-enrichment.png" alt-text="アラートの詳細をカスタマイズする":::

1. 展開された **[アラートの詳細]** セクションで、アラートに表示する詳細に対応するパラメーターを含むフリー テキストを追加します。

    1. **[Alert Name Format]\(アラート名の形式\)** フィールドに、アラートの名前として表示するテキスト (アラート テキスト) を入力し、そのアラート テキストに含めるパラメーターを二重中かっこで囲みます。

        例: `Alert from {{ProviderName}}: {{AccountName}} failed to log on to computer {{ComputerName}} with IP address {{IPAddress}}.`

    1. **[Alert Description Format]\(アラートの説明の形式\)** フィールドでも同じ操作を行います。
    
    1. **[Tactic Column]\(戦術列\)** および **[Severity Column]\(重大度列\)** フィールドは、クエリ結果にこの情報がある列が含まれている場合にのみ使用します。 それぞれについて、対応する情報を含む列を選択します。

    気が変わった場合や、間違った場合は、 **[Tactic Column]\(戦術列\) および [Severity Column]\(重大度列\)** フィールドの横にあるごみ箱アイコンをクリックしてアラートの詳細を削除するか、 **[Alert Name Format]\(アラート名の形式\) および [Alert Description Format]\(アラートの説明の形式\)** フィールドからフリー テキストを削除することができます。

1. アラートの詳細のカスタマイズが完了したら、ウィザードの次のタブに進みます。 既存のルールを編集する場合は、 **[確認と作成]** タブをクリックします。ルールの確認が正常に完了したら、 **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Sentinel 分析ルールでアラートの詳細をカスタマイズする方法について学習しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [スケジュールされたクエリ分析ルール](detect-threats-custom.md)の完全な画像を取得します。
- [Microsoft Sentinel のエンティティ](entities.md)の詳細を確認します。
