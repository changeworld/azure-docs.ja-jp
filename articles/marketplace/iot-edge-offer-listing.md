---
title: Azure Marketplace で IoT Edge モジュール オファー登録情報の詳細を構成する
description: Azure Marketplace で IoT Edge モジュール オファー登録情報の詳細を構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 285b57d5d4094f51611b36794584a83d3d115b66
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542085"
---
# <a name="configure-iot-edge-module-offer-listing-details"></a>IoT Edge モジュール オファーの詳細を構成する

このページでは、オファーの名前、説明、リンク、連絡先、ロゴ、スクリーンショットなど、オファーの詳細を定義できます。

> [!NOTE]
> 1 つの言語でのみ、オファー登録情報の詳細を入力します。 オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、英語にする必要はありません。 オファー登録情報の内容で使用されているもの以外の言語でコンテンツを提供するために、"*役に立つリンクの URL*" を指定することもできます。

Azure Marketplace でのオファー情報の表示例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer.png" alt-text="Azure Marketplace でこのオファーがどのように表示されるかを示しています。":::

##### <a name="call-out-descriptions"></a>コールアウトの説明

1. ロゴ
1. Categories
1. サポートのアドレス (リンク)
1. 使用条件
1. プライバシー ポリシー (リンク)
1. プラン名
1. オファーの概要
1. 説明
1. その他のリンクの説明
1. スクリーンショット、ビデオ

<br>Azure Marketplace の検索結果におけるオファー情報の表示例を次に示します。

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-offer-search-results.png" alt-text="Azure Marketplace の検索結果でこのオファーがどのように表示されるかを示しています。":::

##### <a name="call-out-descriptions"></a>コールアウトの説明

1. 小型のロゴ
2. プラン名
3. 検索結果の概要

<br>Azure portal でのオファー情報の表示例を次に示します。

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer.png" alt-text="Azure portal でこのオファーがどのように表示されるかを示しています。":::

##### <a name="call-out-descriptions"></a>コールアウトの説明

1. 名前
2. 説明
3. 便利なリンク
4. Screenshots (スクリーンショット)

<br>Azure portal の検索結果におけるオファー情報の表示例を次に示します。

:::image type="content" source="media/iot-edge/example-iot-azure-portal-offer-search-results.png" alt-text="Azure portal の検索結果でこのオファーがどのように表示されるかを示しています。":::

##### <a name="call-out-descriptions"></a>コールアウトの説明

1. 小型のロゴ
2. プラン名
3. 検索結果の概要

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

自分の組織のプライバシー ポリシーへの **[プライバシー ポリシー リンク]** (URL) を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

## <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 最大 25 個のリンクを追加できます。 リンクを追加するには、 **[+ リンクの追加]** を選択し、次のフィールドに入力します。

- **[タイトル]** - オファーの詳細ページでタイトルが顧客に表示されます。
- **[リンク (URL)]** - 顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。 リンクは `http://` または `https://` で始まっている必要があります。

少なくともドキュメントへのリンクを 1 つと、 [Azure IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)に記載されている互換性のある IoT Edge デバイスへのリンクを 1 つ追加してください。

### <a name="contact-information"></a>連絡先情報

**[サポートの連絡先]** 、 **[エンジニアリングの連絡先]** 、および **[Cloud Solution Provider Program contact]\(クラウド ソリューション プロバイダー プログラムの連絡先\)** に、名前、メール アドレス、電話番号を入力します。 この情報は顧客には表示されませんが、Microsoft で利用できるようになり、CSP パートナーに提供される場合もあります。

**[クラウド ソリューション プロバイダー Programの連絡先]** セクションで、**Azure グローバル顧客向けサポート Web サイトアドレス** を指定します。パートナーは、オファーが Azure Global、Azure Government、または両方で利用できるかどうかに基づいて、オファーのサポートを検索できます。

**[サポート連絡先]** セクションで、(**CSP プログラムのマーケティング資料**) アドレスを入力します。ここで、CSP パートナーはオファーのマーケティング資料を検索してサポートできます。

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

オファーがどのように動作するかを示すオプションのスクリーンショットを最大 5 つ追加します。 スクリーンショットは 1280 x 720 ピクセルの PNG 形式にする必要があります。 各スクリーンショットのキャプションを追加します。

### <a name="videos"></a>ビデオ

オファーをデモンストレーションするオプションのビデオを最大で 5 つ追加します。 これらは、外部のビデオ サービスでホストされている必要があります。 各ビデオの名前、Web アドレス、ビデオのサムネイルの PNG 画像 (1280 x 720 ピクセル) を入力します。

マーケットプレースの登録情報に関するその他のリソースについては、[マーケットプレース オファーの登録情報のベスト プラクティス](gtm-offer-listing-best-practices.md)に関する記事をご覧ください。

左側のナビゲーション メニューの次のタブである **[プロレビュー対象ユーザー]** に進む前に、 **[下書きの保存]** を選択してください。

## <a name="next-steps"></a>次のステップ

- [プレビュー対象ユーザーの設定](iot-edge-preview-audience.md)
