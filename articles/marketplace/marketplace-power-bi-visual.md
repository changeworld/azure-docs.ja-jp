---
title: Microsoft AppSource 用にパートナー センターで Power BI ビジュアル オファーを計画する
description: パートナー センターで Power BI ビジュアル オファーを送信するために手元に必要な情報について説明します。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 898fcfd97e6c73b530717a99d82f27e0ed3cfe53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082381"
---
# <a name="plan-a-power-bi-visual-offer"></a>Power BI ビジュアル オファーを計画する

この記事では、パートナー センターで Microsoft [AppSource](https://appsource.microsoft.com) に Power BI ビジュアル オファーを公開するために準備または完了しておく必要があるコンテンツおよび要件について説明します。 Power BI ビジュアルは、提供されたデータをユーザーが視覚化できるようにするパッケージです。 カスタム ビジュアルは誰でも作成して、Power BI レポートにインポートできる 1 つの PBIVIZ ファイルとしてパッケージ化できます。

開始する前に、パートナー センターで[コマーシャル マーケットプレース アカウント](./create-account.md)を作成し、それをコマーシャル マーケットプレース プログラムに確実に登録します。 また、公開[プロセス](/office/dev/store/submit-to-appsource-via-partner-center)と[ガイドライン](/legal/marketplace/rating-review-policies)を確認します。

## <a name="publishing-benefits"></a>公開の利点

AppSource に公開することの利点:

- Microsoft のブランドを使用して、会社の宣伝ができる。
- AppSource 上では 1 億を超える Microsoft 365 および Dynamics 365 のユーザーに、さらに Azure Marketplace を通じて 20 万を超える組織に接触できる可能性がある。
- Microsoft のフィールドおよびテレセールス チームによりサービスを宣伝してもらえる。

## <a name="overview"></a>概要

以下をお読みになり、作成プロセスを開始する前の準備を確実かつ適切に行ってください。

以降のトピックでは、主な発行手順について説明します。

1. Power BI ビジュアルを[パッケージ化](/power-bi/developer/visuals/package-visual)します。
2. 公式名、説明、ロゴなどの[マーケティングの詳細](./gtm-offer-listing-best-practices.md#online-store-offer-details)を準備します。
3. 使用条件、プライバシー ポリシー、サポート ポリシー、ユーザー ヘルプなど、オファーの法的およびサポート ドキュメントへのリンクを収集します。
4. ビジュアルを作成する - パートナー センターを使用して、ビジュアルの説明、マーケティング資料、法的情報、サポート情報、資産の仕様などの詳細を入力します。
5. AppSource オンボーディング チームによって、ビジュアルのテスト、検証、および認定が行われる、パートナー センターでプロセスを監視します。 認定された後、アプリをそのテスト環境で確認してからリリースします。 この "公開" によって、AppSource の一覧に表示されるようになります。

## <a name="technical-requirements"></a>技術的な要件

Power BI ビジュアル オファーを公開するための技術的な要件の詳細については、[Power BI ビジュアル オファーの技術的な構成](power-bi-visual-technical-configuration.md)に関するページを参照してください。

Power BI ビジュアルを AppSource に送信する前に、確実に Power BI ビジュアルの[ガイドライン](/power-bi/developer/visuals/guidelines-powerbi-visuals)を読み、自分のビジュアルを[テスト](/power-bi/developer/visuals/submission-testing)しておいてください。

## <a name="legal-contracts"></a>法的契約

自分の Power BI ビジュアル用の **エンドユーザー使用許諾契約 (EULA)** ファイルを提供します。

## <a name="offer-listing-details"></a>オファー登録情報の詳細

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファー登録情報は英語である必要はありません。

オファーの作成をより簡単にするには、これらの項目をあらかじめ準備します。 特に明記されている場合を除き、すべて必須です。

最も効果的な Power BI ビジュアル オファーについては、これらの[ガイドライン](./power-bi-visual-offer-listing.md)を参照してください。

- **名前** - [名前](/office/dev/store/reserve-solution-name)は、コマーシャル マーケットプレースでオファー登録情報のタイトルとして表示されます。 この名前は商標登録されている場合があります。 これは、絵文字 (商標および著作権マークの場合を除く) を使用できず、50 文字に制限されています。
- **概要** – オファーの目的または機能を、100 文字以内で改行のない単一の文として記述します。 これは、コマーシャル マーケットプレースの登録情報の検索結果で使用されます。
- **説明** – これは、コマーシャル マーケットプレースの登録情報の概要に表示されます。 価値提案、主なメリット、対象となるユーザー ベース、カテゴリまたは業界との関連性、[アプリ内の購入](./power-bi-visual-offer-setup.md)機会、必要な情報開示、詳細情報へのリンクを含めることを検討してください。 このテキスト ボックスには、説明をより魅力的にするリッチ テキスト エディター コントロールが用意されています。 必要に応じて、書式設定に HTML タグを使用します。
- **ヘルプ/プライバシー ポリシーのリンク** – 会社のヘルプとプライバシー ポリシーの URL。 ご自身でオファーがプライバシーに関する法律および規則に確実に準拠するようにする必要があります。
- **メディア** – 
    - **ロゴ** – **大** ロゴ用の PNG ファイル。 パートナー センターではこれを使用して、その他の必要なロゴ サイズを作成します。 必要に応じて、別の画像に置き換えることもできます。
    - **スクリーンショット** – オファーがどのように機能するかを示す最小 1 つ、最大 5 つのスクリーンショット。 画像は、1280 x 720 ピクセル (PNG 形式) で、キャプションが含まれている必要があります。
    - **ビデオ** (省略可能) - オファーをデモンストレーションする最大 4 つのビデオ。 名前、YouTube または Vimeo の URL、1280 x 720 ピクセルの PNG サムネイルを含めます。

>[!NOTE]
> オファーを送信する前に、オファー登録情報の[ガイドライン](./marketplace-criteria-content-validation.md)と[ベスト プラクティス](./gtm-offer-listing-best-practices.md)を注意深く確認してください。
>
> コマーシャル マーケットプレースに公開するには、オファーが一般的な[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#100-general)に準拠している必要があります。

## <a name="next-steps"></a>次のステップ

- [Power BI ビジュアル オファーの作成](power-bi-visual-offer-setup.md)を開始する