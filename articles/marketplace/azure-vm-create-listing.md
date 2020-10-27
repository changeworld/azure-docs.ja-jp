---
title: Azure Marketplace で仮想マシンのオファー登録情報の詳細を構成する
description: Azure Marketplace で仮想マシンのオファー登録情報の詳細を構成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283400"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>仮想マシンのオファー登録情報の詳細を構成する方法

**[オファー登録情報]** ページでは、オファーの名前、説明、リンク、連絡先などの詳細を定義します。

> [!NOTE]
> オファー登録情報の内容 (説明、ドキュメント、スクリーンショット、使用条件など) は、オファーの説明が「このアプリケーションは、\<non-English language> でのみ利用可能です」という文言で始まっていれば、英語である必要はありません。 また、オファー登録情報の内容で使用されていない言語でコンテンツを提供するサイトへのリンク URL を提供することもできます。

## <a name="marketplace-details"></a>Marketplace の詳細

### <a name="name"></a>名前

ここで入力する名前は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーを作成したときに **[オファーのエイリアス]** ボックスに入力した名前が自動入力されます。 この名前は後で変更できます。 この名前は、次のようになります。

- 商標を使用できます。 商標および著作権の記号を含めることができます。
- 50 文字を超えることはできません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

Azure Marketplace の検索結果で表示される、お客様のオファーの簡単な説明を入力します。 最大で 100 文字まで使用できます。

### <a name="long-summary"></a>詳細な概要

Azure Marketplace の検索結果で表示される、お客様のオファーの詳細な説明を入力します。 最大で 256 文字まで使用できます。

### <a name="description"></a>説明

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

説明がより関心を引くものになるように、HTML タグを使用して書式を設定します。 使用できるタグの一覧については、[サポートされている HTML タグ](supported-html-tags.md)に関するページを参照してください。

### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

組織のプライバシー ポリシーの Web アドレス (URL) を入力します。 ご自分のオファーをプライバシーに関する法律と規制に確実に準拠させてください。 また、Web サイトに有効なプライバシー ポリシーを掲示する必要もあります。

## <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 リンクを追加するには、 **[リンクの追加]** を選択し、次のフィールドに入力します。

- **Name** :この名前は詳細ページで顧客に表示されます。
- **リンク (URL)** :顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。

## <a name="customer-support-links"></a>カスタマー サポート リンク

顧客がサポート チームに連絡できるサポート Web サイトを提供します。

- Azure グローバル サポート Web サイト
- Azure Government サポート Web サイト

## <a name="partner-support-contact"></a>パートナー サポート連絡先

顧客がサポート チケットを開いたときに Microsoft パートナーが使用する連絡先情報を提供します。 この情報は、Azure Marketplace に記載されません。

- 名前
- Email
- Phone

## <a name="engineering-contact"></a>エンジニアリングの連絡先

認定に関する問題など、オファーに問題がある場合に Microsoft が使用する連絡先情報を提供します。 この情報は、Azure Marketplace に記載されません。

- 名前
- Email
- Phone

## <a name="azure-marketplace-media"></a>Azure Marketplace のメディア

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 画像がぼやけていると、送信が拒否されます。

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>ファイルのアップロードで問題が発生した場合は、パートナー センターによって使用される https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

### <a name="azure-marketplace-logos"></a>Azure Marketplace のロゴ

**大** サイズのロゴに PNG ファイルを指定します。 パートナー センターでは、これを使用して、 **小** および **中** サイズのロゴを作成します。 必要に応じて、別の画像に置き換えることもできます。

- **大** (216 x 216 から 350 x 350 px、必須)
- **中** (90 x 90 px、省略可能)
- **小** (48 x 48 px、省略可能)

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 各スクリーンショットは、サイズが 1280 x 720 ピクセルの PNG 形式にする必要があります。 各スクリーンショットにはキャプションが含まれている必要があります。

### <a name="videos"></a>ビデオ

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオは外部のビデオ サービスでホストされている必要があります。 各ビデオの名前、Web アドレス、ビデオのサムネイルの PNG 画像 (1280 x 720 ピクセル) を入力します。

マーケットプレースの登録情報に関するその他のリソースについては、[マーケットプレース オファーの登録情報のベスト プラクティス](gtm-offer-listing-best-practices.md)に関する記事をご覧ください。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次の手順

- [プランを作成する](azure-vm-create-plans.md)
