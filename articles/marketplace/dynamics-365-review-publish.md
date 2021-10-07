---
title: Dynamics 365 オファーをレビューして、Microsoft AppSource (Azure Marketplace) に公開する
description: Dynamics 365 オファーをレビューして、Microsoft AppSource (Azure Marketplace) に公開します。
ms.reviewer: emuench
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: b5dc12c821471f822d88de89db974c081a4a1e33
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079574"
---
# <a name="review-and-publish-a-dynamics-365-offer"></a>Dynamics 365 オファーのレビューして発行する

この記事では、パートナー センターを使用して下書きの Dynamics 365 オファーをプレビューし、それをコマーシャル マーケットプレースに公開する方法について説明します。 また、公開手順を実行しながら、公開状態を確認する方法についても説明します。

## <a name="offer-status"></a>オファーの状態

オファーの状態は、**パートナー センター** のコマーシャル マーケットプレース ダッシュボードにある [[概要]](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) タブで確認できます。 各オファーの **状態** は、次のいずれかになります。

| Status | 説明 |
| ------------ | ------------- |
| ドラフト | オファーは作成されましたが、発行されていません。 |
| 公開が進行中です | プランの公開プロセスが進行中です。 |
| 注意が必要 | 認定または別の公開フェーズ中に、重大な問題が検出されました。 |
| プレビュー | プランは認定され、現在パブリッシャーによる最終確認を待っています。 プランを公開するには、 **[Go live]** \(公開\) を選択します。 |
| ライブ | オファーがマーケットプレースで公開され、顧客が表示および取得ができます。 |
| Pending stop sell\(保留中の販売停止\) | パブリッシャーがプランで "販売停止" を選択しましたが、アクションはまだ完了していません。 |
| Not available in the marketplace\(マーケットプレースで利用不可\) | マーケットプレースに以前に発行されたオファーが削除されました。 |
|||

## <a name="validation-and-publishing-steps"></a>検証と公開の手順

オファーの公開ステータスは、公開プロセスを移動すると変更されます。 このプロセスの詳細については、「[検証と公開の手順](review-publish-offer.md#validation-and-publishing-steps)」を参照してください。

オファーを公開に向けて送信する準備ができたら、ポータルの右上隅にある **[レビューと公開]** を選択します。 オファーの各ページのステータスは、次のいずれかで表示されます。

- **[未開始]** – ページが不完全です。
- **[不完全]** – ページに必要な情報がないか、修正が必要なエラーがあります。 ページに戻って更新する必要があります。
- **[完了]** – ページが完了しました。 必須のデータはすべて入力済みであり、エラーはありません。

いずれかのページの状態が **[完了]** 以外の場合は、そのページで問題を修正し、 **[レビューと公開]** ページに戻って、状態が **[完了]** と表示されていることを確認する必要があります。 一部のオファーの種類では、テストが必要です。 その場合は、 **[認定の注意書き]** フィールドが表示されます。ここでは、テスト手順と、アプリを理解するために役立つ補足メモを認定チームに提供する必要があります。

すべてのページが完了し、該当するテスト ノートを入力したら、 **[公開]** を選択してオファーを送信します。 オファーのプレビュー バージョンの承認が可能になったら、メールが送信されます。 その時点で、次の手順を実行します。

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)に戻ります。
1. ホーム ページで、 **[マーケットプレースのオファー]** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [マーケットプレースのオファー] タイルを示す図。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [マーケットプレースのオファー] ページで、オファーを選択します。
1. **[レビューと公開]** を選択します。
1. **[公開]** を選択してオファーを一般公開します。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)に戻ります。
1. 左側のナビゲーション メニュー バーで **[概要]** タブを選択します。
1. オファーを選択します。
1. **[レビューと公開]** を選択します。
1. **[公開]** を選択してオファーを一般公開します。

---

**[レビューと公開]** を選択すると、そのオファーが AppSource に公開される前に、認定やその他の検証プロセスが実行されます。 オファーがプレビューで利用可能なると通知が送信されるため、一般公開できるようになります。 問題が発生した場合は、詳細情報を含む通知が送信され、その解決方法に関するガイダンスが提供されます。

## <a name="next-steps"></a>次のステップ

- オファーの "_Microsoft によるサードパーティ製アプリ ライセンス管理_" を有効にした場合は、そのオファーを販売した後、パートナー センターでその取引を登録する必要があります。 詳細については、[マーケットプレースのオファーでのライセンスの管理](/partner-center/csp-commercial-marketplace-licensing)に関するページを参照してください。
- [商業マーケットプレースで既存のオファーを更新する](update-existing-offer.md)
