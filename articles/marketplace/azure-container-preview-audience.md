---
title: Microsoft AppSource で Azure コンテナー プランの対象ユーザーを設定します。
description: Microsoft AppSource で Azure コンテナー プランの対象ユーザーを設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0d8bd02e247a3db0429f227e396abbfbc7cdfd91
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892490"
---
# <a name="set-the-preview-audience-for-an-azure-container-offer"></a>Azure コンテナー プランのプレビュー対象ユーザーを設定する

この記事では、パートナー センターを使用して、コマーシャル マーケット プレース内の Azure コンテナー プランに対してプレビュー対象ユーザーを構成する方法について説明します。 オファーが一般公開される前に、プレビュー対象ユーザーはそれをレビューすることができます。

## <a name="define-a-preview-audience"></a>プレビュー対象ユーザーを定義する

**[プレビュー対象ユーザー]** ページでは、マーケットプレースの幅広い対象ユーザーに公開する前にコンテナー プランをレビューできる限られた対象ユーザーを定義できます。 Azure サブスクリプション ID GUID を使用してプレビュー対象ユーザーを定義し、それぞれに、省略できる説明を付けます。 いずれのフィールドも顧客は表示できません。 Azure サブスクリプション ID は、Azure portal の **[サブスクリプション]** ページにあります。

1 つ以上の Azure サブスクリプション ID を個別に追加するか (最大 10 個)、CSV ファイルをアップロードして (最大 100 個)、それをプレビューできるユーザーを定義します。 オファーが既に公開されている場合も、オファーの更新をテストするためにプレビュー対象ユーザーを定義することができます。

## <a name="add-email-addresses-manually"></a>メール アドレスを手動で追加する

1. **[プレビュー対象ユーザー]** ページで、表示されるボックスに、1 つの Azure サブスクリプション ID と説明 (省略可能) を追加します。
1. 別のメール アドレスを追加するには、 **[ID の追加 (最大 10)]** リンクを選択します。
1. **[下書きの保存]** を選択してから、次のタブへ進みプランを設定します。

## <a name="add-email-addresses-using-a-csv-file"></a>CSV ファイルを使用してメール アドレスを追加する

1. **[プレビュー対象ユーザー]** ページで、 **[対象者のエクスポート (csv)]** リンクを選択します。
1. .CSV ファイルを開きます。 **ID** 列に、プレビュー対象ユーザーに追加する Azure サブスクリプション ID を入力します。
1. **[説明]** 列に、各エントリの説明を追加することもできます。
1. **[タイプ]** 列で、ID を含む各行に **SubscriptionId** を追加します。
1. ファイルを CSV ファイルとして保存します。
1. **[プレビュー対象ユーザー]** ページで、 **[対象者のインポート (csv)]** リンクを選択します。
1. **[確認]** ダイアログ ボックスで **[はい]** を選択し、CSV ファイルをアップロードします。
1. **[下書きの保存]** を選択してから、次のタブへ進みプランを設定します。

> [!IMPORTANT]
> プレビューでオファーを表示したら、 **[一般公開する]** を選択し、オファーを一般公開する必要があります。

## <a name="next-steps"></a>次の手順

- [プランを作成して管理する](azure-container-plan-overview.md)
