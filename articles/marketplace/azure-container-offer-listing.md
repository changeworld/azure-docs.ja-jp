---
title: Microsoft AppSource で Azure Container のオファー登録情報の詳細を構成する
description: パートナー センターで Azure Container のオファー登録情報の詳細を構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 8d72d2ca68f3a874f0c954174a123d6665cd5ebd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065409"
---
# <a name="configure-azure-container-offer-listing-details"></a>Azure Container のオファー登録情報の詳細を構成する

このページでは、オファーの名前、説明、リンク、連絡先、ロゴ、スクリーンショットなど、オファーの詳細を定義できます。

> [!NOTE]
> 1 つの言語でのみ、オファー登録情報の詳細を入力します。 オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、英語にする必要はありません。 オファー登録情報の内容で使用されているもの以外の言語でコンテンツを提供するために、"*役に立つリンクの URL*" を指定することもできます。

## <a name="marketplace-details"></a>Marketplace の詳細

ここで入力する **[名前]** は、オファーのタイトルとしてお客様に表示されます。 このフィールドには、オファーの作成時に **[オファーの別名]** に入力した名前が事前に設定されていますが、それは変更できます。 この名前は、次のようになります。

- 商標および著作権の記号を含めることができます。
- 50 文字以下にする必要があります。
- 絵文字を含めることはできません。

**[検索結果の概要]** には、オファーの簡単な説明 (最大 100 文字) を入力します。 この説明は、マーケットプレースの検索結果で使用される場合があります。

オファーの **[簡単な説明]** を最大 256 文字で入力します。 これは、検索結果とオファーの詳細ページに表示されます。

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

説明がより関心を引くものになるように、HTML タグを使用して書式を設定します。 使用できるタグの一覧については、[サポートされている HTML タグ](supported-html-tags.md)に関するページを参照してください。

組織のプライバシー ポリシーの Web アドレス (URL) を入力します。 オファーがプライバシーに関する法律と規制に準拠していることを確認します。 また、Web サイトに有効なプライバシー ポリシーを掲示する必要もあります。

## <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 最大 25 個のリンクを追加できます。 リンクを追加するには、 **[+ リンクの追加]** を選択し、次のフィールドに入力します。

- **[名前]** - オファーの詳細ページでこれがお客様に表示されます。
- **[リンク]** (URL) – お客様がオンライン ドキュメントを閲覧するためのリンクを入力します。 リンクの先頭は http:// か https:// でなければなりません。

### <a name="contact-information"></a>連絡先情報

**[サポートの連絡先]** 、 **[エンジニアリングの連絡先]** 、および **[Cloud Solution Provider Program contact]\(クラウド ソリューション プロバイダー プログラムの連絡先\)** に、名前、メール アドレス、電話番号を入力します。 この情報はお客様には表示されませんが、Microsoft が利用できるようになり、CSP パートナーに提供される場合もあります。

**[サポートの連絡先]** セクションで、Azure Global と Azure Government (該当する場合) のお客様がサポート チームに連絡できる **[サポート Web サイト]** を指定します。

## <a name="marketplace-media"></a>Marketplace メディア

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 画像がぼやけていると、送信が拒否されます。

>[!NOTE]
>ファイルのアップロードで問題が発生した場合は、パートナー センターによって使用される https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

### <a name="logos"></a>ロゴ

**大** サイズのロゴに PNG ファイルを指定します。 パートナー センターではこれを使用して、その他の必要なサイズを作成します。 これは、必要に応じて、後で別の画像に置き換えることができます。

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを少なくとも 1 つ (最大で 5 つ) 追加します。 スクリーンショットはすべて 1280 x 720 ピクセルの PNG 形式にする必要があります。 各スクリーンショットのキャプションを追加します。

### <a name="videos"></a>ビデオ

オファーをデモンストレーションするオプションのビデオを最大で 5 つ追加します。 これらは、外部のビデオ サービスでホストされている必要があります。 各ビデオの名前、Web アドレス、ビデオのサムネイルの PNG 画像 (1280 x 720 ピクセル) を入力します。

マーケットプレースの登録情報に関するその他のリソースについては、[マーケットプレース オファーの登録情報のベスト プラクティス](gtm-offer-listing-best-practices.md)に関する記事をご覧ください。

左側のナビゲーション メニューの次のタブである **[プロレビュー対象ユーザー]** に進む前に、 **[下書きの保存]** を選択してください。
<!-- #### Offer examples

The following examples show how the offer listing fields appear in different places of the offer.

This shows search results in Azure Marketplace:

[![Illustrates search results in Azure Marketplace](media/azure-container/azure-create-7-search-results-mkt-plc-small.png)](media/azure-container/azure-create-7-search-results-mkt-plc.png#lightbox)

This shows the **Offer listing** page in the Azure portal:

:::image type="content" source="media/azure-container/azure-create-8-offer-listing-portal.png" alt-text="Illustrates the Offer listing page in the Azure portal.":::

This shows search results in the Azure portal:

[![Illustrates search results in the Azure portal.](media/azure-container/azure-create-9-search-results-portal-small.png)](media/azure-container/azure-create-9-search-results-portal.png#lightbox) -->

## <a name="next-steps"></a>次のステップ

- [オファーのプレビュー対象ユーザーを設定する](azure-container-preview-audience.md)
