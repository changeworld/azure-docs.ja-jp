---
title: Power BI アプリ オファーを作成する - Azure Marketplace
description: Power BI アプリ オファーを作成し、Microsoft AppSource に公開する方法について学習します。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867597"
---
# <a name="create-a-power-bi-app"></a>Power BI アプリを作成する

> [!IMPORTANT]
> Power BI アプリ オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、Cloud パートナー ポータル向けの「[Power BI アプリ オファーを作成する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer)」の手順に従って、オファーを管理してください。

この記事では、Power BI アプリ オファーを作成し、Microsoft [AppSource](https://appsource.microsoft.com/) に公開する方法について説明します。

Power BI アプリ オファーを作成する前に、パートナー センターに商業マーケットプレース アカウントが必要です。 まだ作成していない場合は、「[商業マーケットプレース アカウントをパートナー センターに作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)」を参照してください。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="商業マーケットプレースの概要メニュー" border="false":::

3. **[+ 新しいオファー]**  >  **[Power BI アプリ]** の順に選択します。 **[新しいオファー]** ダイアログ ボックスが表示されます。

> [!IMPORTANT]
> **[Power BI アプリ]** オプションが表示されない場合、または有効にならない場合は、このオファーの種類を作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、このオファーの種類に対するすべての[要件](create-power-bi-app-overview.md)を満たしていることを確認してください

### <a name="offer-id-and-alias"></a>オファーの ID と別名

**[オファー ID]** を入力します。 これは、ご自分のアカウント内の各オファーに対する一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。

- **[作成]** を選択した後で、オファー ID を変更することはできません。

**オファーの別名**を入力します。 これは、パートナー センター内でオファーを参照するために使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** の選択後にこれを変更することはできません。

これら 2 つの値を入力した後、 **[作成]** を選択し、[オファーの概要] ページに進みます。

## <a name="offer-overview"></a>オファーの概要

**[オファーの概要]** ページでは、このオファー (完了しているものと近日公開のものの両方) を公開するために必要な手順と各手順の完了に必要な時間が図で示されます。

これには、選択した内容に基づき、このオファーで操作を実行するためのリンクが含まれています。 次に例を示します。

- オファーがドラフトの場合 - [ドラフトのオファーを削除する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- オファーが発行中の場合 - [オファーの販売を停止する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 公開元のサインアウトを完了していない場合 - [公開を取り消す](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>オファーのセットアップ

オファーを設定するには、次の手順に従います。

### <a name="connect-lead-management"></a>リード管理の接続

パートナー センターを使用してオファーをマーケットプレースに公開する場合、それを顧客関係管理 (CRM) システムに接続する必要があります。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. 顧客リードの送信先となるリードのターゲットを選択します。 パートナー センターでは、次の CRM システムがサポートされています。

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > ご利用の CRM システムが上記の一覧にない場合は、[Azure テーブル](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)または [HTTPS エンドポイント](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)を使用して顧客リードのデータを格納します。 その後、そのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで発行する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft で接続を検証して、テスト リードを送信します。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リード接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [潜在顧客管理の概要](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [リード管理に関する FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [一般的なリード構成エラー](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)

**[下書きの保存]** を選択してから、次の「Properties」セクションに進みます。

## <a name="properties"></a>Properties

このページでは、マーケットプレースでのオファーのグループ化に使用されるカテゴリと業界、アプリのバージョン、オファーをサポートする法的契約を定義できます。

### <a name="category"></a>カテゴリ

少なくとも 1 つ (最大 3 つ) のカテゴリを選択します。 これらのカテゴリは、オファーを適切なマーケットプレース検索領域に配置するために使用され、オファーの詳細ページに表示されます。 オファーの説明では、これらのカテゴリにオファーがどのように対応しているかを説明します。

### <a name="industry"></a>業界

必要に応じて、各業界で最大 2 つの業界と 2 つの業種を選択します。 カテゴリはオファーを表示するために使用されますが、業界および業種は検索フィルターで使用され、ネットショップで適用されます。 オファーのターゲットが特定の業界や業種である場合は、オファーの説明を使用して、選択された業界または業種がオファーでどのようにサポートされるかを説明します。 オファーが業界に固有でない場合は、このセクションを空白のままにします。

> [!NOTE]
> オファー検出エクスペリエンスを向上させるために新しい業界と業種の導入に取り組んでいるため、一部の業界や業種はまだネットショップでは表示されない場合があります。 (*) でマークされた業界と業種は、将来の日付で利用できるようになります。 公開されているすべてのオファーはキーワード検索を使用して検出できます。
<p>&nbsp;

| **業界** | **下位業界** |
| --- | --- |
| *自動車 | *自動車 |
| 農業 | *その他 - 区分なし |
| Distribution | *卸売<br>小包の発送 |
| 教育 | *高等教育<br>* 初等および中等教育/K-12<br>*ライブラリと美術館 |
| 金融サービス | *銀行と証券<br>* 保険 |
| Government | *防御とインテリジェンス (以前の国家安全保障と公安)<br>* 公安と司法<br>*民間政府 |
| 医療 (以前の "衛生") | *医療保険<br>* 医療サービス<br>*製薬 |
| 製造と資源 (以前の製造) | *化学と農薬<br>* 個別製造<br>*エネルギー |
| 小売と消費財 (以前の小売) | *消費財*<br>小売 |
| *メディアと通信 (以前のメディアと娯楽) | *メディアと娯楽<br>* 電気通信 |
| プロフェッショナル サービス | *司法<br>* パートナー専門サービス業 |
| *建築と建設 (以前の建築と工学) | *その他 - 区分なし |
| *接客と旅行 | *ホテルとレジャー<br>* 旅行と輸送<br>*レストランと食品サービス |
| *その他の公共セクター産業 | *林業と漁業<br>* 非営利 |
| *不動産 | *その他 - 区分なし |

### <a name="legal"></a>法的情報

#### <a name="terms-and-conditions"></a>使用条件

独自のカスタム使用条件を指定するには、 **[使用条件]** ボックスに最大 10,000 文字を入力します。 使用条件により長い説明が必要な場合は、その場所への Web リンクを 1 つ入力します。 これはアクティブなリンクとして顧客に表示されます。

顧客は、オファーを試す前にこれらの条件に同意する必要があります。

**[下書きの保存]** を選択してから、次の「オファーのリスト登録」セクションに進みます。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、マーケットプレースに表示されるオファーの詳細を定義します。 これには、オファーの名前、説明、画像などが含まれます。

### <a name="language"></a>Language

オファーがリスト登録される言語を選択します。 現時点では、使用可能なオプションは **[英語 (米国)]** のみです。

言語と市場ごとにマーケットプレースの詳細 (オファーの名前、説明、画像など) を定義します。 この情報を提供する言語/市場名を選択します。

> [!NOTE]
> オファーの説明が「&quot;このアプリケーションは、[英語以外の言語] でのみ利用可能です&quot;」という文言で始まっている場合、オファーの詳細は英語である必要はありません。また、オファーのリスト登録で使用されているものとは異なる言語でコンテンツを提供するのに役立つリンクを提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファーのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようにすることができます。

- 商標登録されている可能性があります (商標または著作権マークを含めることもできます)。
- 文字長が 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明を入力します。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="description"></a>説明

オファーの詳しい説明 (最大 3,000 文字) を入力します。 これは、マーケットプレースのリスト登録に関する概要で顧客に表示されます。

説明には、次の 1 つまたは複数を含めます。

- オファーによってもたらされる価値と主な利益。
- カテゴリまたは業界のアソシエーション、あるいはその両方。
- アプリ内購入の営業案件。
- 必要な開示。

説明を記述するためのいくつかのヒントを次に示します。

- 説明の先頭のいくつかの文で、オファーの価値を明確に説明します。 次の項目を含めます。
  - プランの説明。
  - オファーから利益を得るユーザーの種類。
  - 顧客は、オファーのアドレスを必要とします。またはそれを発行します。
- 先頭のいくつかの文が検索結果に表示される場合があることに注意してください。
- 特徴や機能に頼って製品を販売しないでください。 代わりに、オファーによって提供される値に焦点を当てます。
- 業界固有の語彙または利益に基づく表現を使用してください。

プランの**説明**をより魅力的なものにするには、リッチ テキスト エディターを使用して説明を書式設定してください。 リッチ テキスト エディターを使用すると、数字、箇条書き、太字、斜体、およびインデントを追加して、説明を読みやすくすることができます。

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="リッチ テキスト エディターの画像" border="false":::

### <a name="search-keywords"></a>キーワード検索

顧客がマーケットプレースでオファーを検索するのに役立つように、最大 3 つのオプションの検索キーワードを入力します。 最良の結果を得るために、これらのキーワードを説明にも使用します。

### <a name="helpprivacy-web-addresses"></a>ヘルプおよびプライバシー Web アドレス

顧客がオファーの理解を深めるのに役立つリンクを提供します。

#### <a name="help-link"></a>ヘルプ リンク

顧客がオファーの詳細について確認できる Web アドレスを入力します。

#### <a name="privacy-policy-url"></a>[プライバシー ポリシーの URL]

組織のプライバシー ポリシーへの Web アドレスを入力します。 オファーが確実にプライバシーに関する法律や規則に準拠しているようにする責任があります。 また、Web サイトに有効なプライバシー ポリシーを投稿する責任もあります。

### <a name="contact-information"></a>連絡先情報

**サポートの連絡先**と**エンジニアリングの連絡先**の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されません。 これは Microsoft で利用することができ、クラウド ソリューション プロバイダー (CSP) パートナーに提供される場合があります。

- サポートの連絡先 (必須):サポートに関する一般的な質問用です。
- エンジニアリングの連絡先 (必須):技術的な質問と認定に関する問題用です。
- CSP プログラムの連絡先 (省略可能):リセラーからの CSP プログラムに関する質問用です。

**[サポートの連絡先]** セクションに、パートナーがオファーのサポートを確認できる**サポート Web サイト**の Web アドレスを指定します。

### <a name="supporting-documents"></a>サポート ドキュメント

少なくとも 1 つ、最大で 3 つの関連するマーケティング ドキュメントを PDF 形式で指定します。 たとえば、ホワイト ペーパー、パンフレット、チェックリスト、プレゼンテーションなどです。

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて .png 形式である必要があります。 ぼやけた画像は拒否されます。

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

オファーのロゴの .png ファイルは、次の 2 つのサイズで提供します: **小** (48 x 48 ピクセル) と **大** (216 x 216 ピクセル)。

両方のロゴが必要であり、マーケットプレースの登録情報のさまざまな場所で使用されます。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを 1 つ以上、最大 5 つ追加します。 それぞれを 1280 x 720 ピクセルのサイズおよび .png 形式とする必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオの名前、Web アドレス、ビデオのサムネイル .png 画像 (1280 x 720 ピクセルのサイズ) を入力します。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

オファーのリスト登録の作成について詳しくは、「[オファーの掲載のベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)」を参照してください。

## <a name="technical-configuration"></a>技術的な構成

Power BI サービスでアプリを運用環境に昇格させ、顧客がアプリをインストールできるようにするための Power BI アプリ インストーラー リンクを提供します。 詳しくは、「[Power BI でダッシュボードとレポートを含むアプリを発行する](https://docs.microsoft.com/power-bi/service-create-distribute-apps)」をご覧ください。

## <a name="supplemental-content"></a>補足コンテンツ

検証に役立つオファーに関する追加情報を提供します。 この情報は顧客に表示されることも、マーケットプレースに公開されることもありません。

### <a name="validation-assets"></a>検証アセット

Microsoft 検証チームでのアプリの構成、接続、テストに役立つように、必要に応じて、手順 (最大 3,000 文字) を追加します。 [データに接続] オプションをテストするために使用できる一般的な構成設定、アカウント、パラメーター、またはその他の情報を含めます。 この情報は、検証チームだけに表示され、検証目的のみに使用されます。

## <a name="review-and-publish"></a>レビューと公開

オファーの必須セクションをすべて完了したら、レビューと公開のためにそのオファーを送信することができます。

ポータルの右上隅で、 **[レビューと公開]** を選択します。

レビュー ページでは、次のことができます。

- オファーの各セクションの完了状態を確認する。 オファーのすべてのセクションに完了のマークが付くまで公開できません。
  - **未開始** - セクションは開始されておらず、完了する必要があります。
  - **未完了** - 修正が必要なエラーがセクションにあり、追加情報を入力する必要があります。 ガイダンスについては、このドキュメントの前のセクションを参照してください。
  - **完了** - セクションには必要なすべてのデータがあり、エラーはありません。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- アプリが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 また、オファーの理解に役立つ補足事項を提供します。

オファーを送信して公開するには、 **[公開]** を選択します。

オファーのプレビュー バージョンが確認して承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。 オファーを一般に (プライベート オファーの場合は、プライベート対象ユーザーに) 公開するには、パートナー センターに移動して、 **[一般公開する]** を選択します。
