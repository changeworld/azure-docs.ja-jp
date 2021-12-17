---
title: ホスト プールの Azure Virtual Desktop スケーリング プラン (プレビュー)
description: デプロイ内の新規または既存のホスト プールにスケーリング プランを割り当てる方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b61869e09003456f55a6d69cae01356a06bc633d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181545"
---
# <a name="enable-scaling-plans-for-existing-and-new-host-pools-preview"></a>既存および新規のホスト プールに対してスケーリング プランを有効にする (プレビュー)

> [!IMPORTANT]
> 自動スケール機能は、現在プレビューの段階にあります。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

デプロイ内の既存のホスト プールに対してスケーリング プランを有効にすることができます。 スケーリング プランをホスト プールに適用すると、そのホスト プール内のすべてのセッション ホストにもプランが適用されます。 スケーリングは、割り当てられたホスト プールで作成した新しいセッション ホストにも自動的に適用されます。

スケーリング プランを無効にした場合、割り当てられたすべてのリソースは、無効にした時点のスケーリング状態のままになります。

## <a name="assign-a-scaling-plan-to-an-existing-host-pool"></a>スケーリング プランを既存のホスト プールに割り当てる

スケーリング プランを既存のホスト プールに割り当てるには:

1. [Azure Portal](https://portal.azure.com)を開きます。

2. **Windows Virtual Desktop** に移動します。

3. **[ホスト プール]** を選択し、**[Scaling plan]\(スケーリング プラン\)** に移動して **[新規]** を選択します。

4. **[Scaling plan]\(スケーリング プラン\)** を選択し、**[+ 割り当て]** を選択して、次のスクリーンショットに示すように、割り当てられていないホスト プールにスケーリング プランを割り当てます。

    > [!div class="mx-imgBorder"]
    > ![[Scaling plan]\(スケーリング プラン\) ウィンドウのスクリーンショット。 左上の [+ 追加] ボタンが赤い枠線で強調表示されています。](media/assign-scaling-plan.png)

    デプロイ時にスケーリング プランを有効にした場合は、次のスクリーンショットに示すように、**[Scaling plan]\(スケーリング プラン\)** メニューで **[Enable scaling plan]\(スケーリング プランを有効にする\)** チェックボックスをオフにし、選択したホスト プールのプランを無効にすることもできます。

    > [!div class="mx-imgBorder"]
    > ![[Scaling plan]\(スケーリング プラン\) ウィンドウのスクリーンショット。 [自動スケーリングの有効化] チェック ボックスがオンであり、赤い枠線で強調表示されています。](media/enable-autoscale.png)

## <a name="edit-an-existing-scaling-plan"></a>既存のスケーリング プランを編集する

既存のスケーリング プランを編集するには:

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

2. **Azure Virtual Desktop** に移動します。

3. **[Scaling plan]\(スケーリング プラン\)** を選択し、編集するスケーリング プランの名前を選択します。 設定ウィンドウが開きます。

4. プランの表示名、説明、タイム ゾーン、または除外タグを編集するには、**[プロパティ]** タブに移動します。

5. ホスト プールを割り当てるか、スケジュールを編集するには、**[管理]** タブに移動します。

## <a name="next-steps"></a>次のステップ

- スケーリング プランの作成方法については、[Azure Virtual Desktop セッション ホストの自動スケーリング (プレビュー)](autoscale-new-existing-host-pool.md) に関する記事を参照してください。
- スケーリング プランのトラブルシューティング方法については、[スケール プランの診断を有効にする](autoscale-diagnostics.md)方法に関する記事を参照してください。