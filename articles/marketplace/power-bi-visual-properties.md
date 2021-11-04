---
title: Microsoft AppSource 用にパートナー センターで Power BI ビジュアル オファーのプロパティを構成する
description: Microsoft AppSource 用にパートナー センターで Power BI ビジュアル オファーのプロパティを構成する方法について説明します。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: c0a683cbca2f29c7c4f8250b07ebcb5937400723
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079326"
---
# <a name="configure-power-bi-visual-offer-properties"></a>Power BI ビジュアル オファーのプロパティを構成する

このページでは、Microsoft AppSource でのオファーのグループ化に使用される[カテゴリ](./categories.md)、オファーをサポートする法的契約、およびサポート ドキュメントを定義できます。

## <a name="general-info"></a>一般情報

- オファーを適切なマーケットプレース検索領域にグループ化するには、最大 3 つの **[カテゴリ](./categories.md)** を選択します。
- お客様がオンライン ストアで検索を業界で絞り込むときに、オファーを表示するために使用される **業界** を、最大 2 つまで選択します。

## <a name="legal-and-support-info"></a>法的およびサポートに関する情報

オファーの使用条件を指定します。 2 つのオプションがあります。

- [標準契約](#use-the-standard-contract)を使用する
- [独自の使用条件 (EULA) を使用する](#use-your-own-terms-and-conditions)

標準契約の詳細については、「[Microsoft コマーシャル マーケットプレースの標準契約](standard-contract.md)」を参照するか、[標準契約](https://go.microsoft.com/fwlink/?linkid=2041178)に関する PDF をダウンロードしてください (ポップアップ ブロッカーが無効になっていることを確認してください)。

### <a name="use-the-standard-contract"></a>標準契約を使用する

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

1. **[標準契約を使用する]** チェック ボックスを選択します。

    :::image type="content" source="media/power-bi-visual/use-standard-contract.png" alt-text="[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する] チェックボックスの画像。":::

1. **[確認]** ダイアログ ボックスで **[承諾]** を選択します。 画面のサイズによっては、上にスクロールして表示する必要があります。

   > [!NOTE]
   > コマーシャル マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタム使用条件を使用することはできません。 ソリューションは、省略可能な変更ありの標準契約または独自の使用条件の下で指定します。

### <a name="use-your-own-terms-and-conditions"></a>独自の使用条件を使用する

標準契約を使用せずに独自の使用条件を指定することも、Power BI ビジュアル オファーに固有の EULA を使用することもできます。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

1. **[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する]** チェック ボックスの選択を解除します。
1. **EULA** フィールド (上記の画像を参照) に、ご自分の使用条件用の Web アドレスを 1 つ入力します。 または、`https://visuals.azureedge.net/app-store/Power%20BI%20-%20Default%20Custom%20Visual%20EULA.pdf` (PDF) の Power BI ビジュアル コントラクトをポイントします。 どちらの場合も、AppSource ではアクティブなリンクとして表示されます。

### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

自分の組織のプライバシー ポリシーへのリンク (URL) を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

### <a name="support-document-link"></a>サポート ドキュメントへのリンク

このリンクは、AppSource 上のユーザーに表示されます。 URL には、 http:// または https:// を含めます。

左側のナビゲーション メニューの次のタブである **[オファー登録情報]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [**オファーのリスト登録**](power-bi-visual-offer-listing.md)