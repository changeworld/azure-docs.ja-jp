---
title: Microsoft コマーシャル マーケットプレースで Power BI アプリ オファーを作成する
description: Power BI アプリ オファーを作成し、Microsoft AppSource に公開する方法について学習します。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839149"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Microsoft AppSource 用の Power BI アプリを作成する

この記事では、Power BI アプリ オファーを作成し、Microsoft [AppSource](https://appsource.microsoft.com/) に公開する方法について説明します。

開始する前に、[パートナー センターでコマーシャル マーケットプレース アカウントを作成します](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) (まだ作成していない場合)。 アカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Power BI サービス アプリ]** を選択します。

   ![左側のナビゲーション メニューの画像。](./media/new-offer-pbi-app.png)

> [!NOTE]
> オファーを公開した後にパートナー センターで編集した内容は、オファーの再公開後にのみネットショップに表示されます。 変更後は必ず再公開してください。

> [!IMPORTANT]
> **[Power BI サービス アプリ]** が表示されない場合、または有効にならない場合は、この種類のオファーを作成するためのアクセス許可がアカウントにありません。 開発者アカウントの登録など、この種類のオファーに対するすべての[要件](create-power-bi-app-overview.md)を満たしていることを確認してください。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、ご自分のアカウント内の各オファーに対する一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、ここに「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後で、オファーの別名を変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-overview"></a>オファーの概要

このページでは、このオファー (完了しているものと近日公開のものの両方) を公開するために必要な手順と各手順の完了に必要な時間を図で示します。

これには、選択した内容に基づき、このオファーで操作を実行するためのリンクが含まれています。 次に例を示します。

- オファーがドラフトの場合 - [ドラフトのオファーを削除する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- オファーが発行中の場合 - [オファーの販売を停止する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 公開元のサインアウトを完了していない場合 - [公開を取り消す](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>オファーのセットアップ

### <a name="connect-lead-management"></a>リード管理の接続

パートナー センターを使用してオファーをマーケットプレースに公開する場合、それを顧客関係管理 (CRM) システムに接続する必要があります。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. 顧客リードの送信先となるリードのターゲットを選択します。 パートナー センターでは、次の CRM システムがサポートされています。

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > ご利用の CRM システムが上記の一覧にない場合は、[Azure テーブル](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)または [HTTPS エンドポイント](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)を使用して顧客リードのデータを格納します。 その後、そのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで発行する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft で接続を検証して、テスト リードを送信します。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [リード管理の概要](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [リード管理に関する FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [一般的なリード構成エラー](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)

続行する前に、 **[下書きの保存]** を選択します。

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

**[下書きの保存]** を選択してから、次のセクション「オファーのリスト登録」に進みます。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、マーケットプレースに表示されるオファーの詳細を定義します。 これには、オファーの名前、説明、画像などが含まれます。

### <a name="language"></a>Language

オファーがリスト登録される言語を選択します。 現時点では、使用可能なオプションは **[英語 (米国)]** のみです。

言語と市場ごとにマーケットプレースの詳細 (オファーの名前、説明、画像など) を定義します。 この情報を提供する言語/市場名を選択します。

> [!NOTE]
> オファーの詳細が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっていれば英語である必要はありません。 また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録で使用されているものとは異なる言語で提供することもできます。

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
- 特徴や機能に頼って製品を販売しないでください。 代わりに、オファーによって提供される価値に焦点を当てます。
- 業界固有の語彙または利益に基づく表現を使用するようにしてください。

オファーの説明をより魅力的なものにするには、リッチ テキスト エディターを使用して書式設定を適用してください。

![リッチ テキスト エディターの使用](./media/rich-text-editor.png)

| <center>テキスト形式を変更する | <center>箇条書きの黒丸または段落番号を追加する | <center>テキスト インデントを追加または削除する |
| --- | --- | --- |
| <center>![リッチ テキスト エディターを使用してテキスト形式を変更する](./media/text-editor3.png) |  <center>![リッチ テキスト エディターを使用してリストを追加する](./media/text-editor4.png) |  <center>![リッチ テキスト エディターを使用してインデントを設定する](./media/text-editor5.png) |

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

>[!NOTE]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

オファーのロゴの .png ファイルは、次の 2 つのピクセル サイズで提供します。
- **小** (48 x 48)
- **大** (216 x 216)

両方のロゴが必要であり、マーケットプレースの登録情報のさまざまな場所で使用されます。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを 1 つ以上、最大 5 つ追加します。 それぞれを 1280 x 720 ピクセルのサイズおよび .png 形式とする必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオの名前、Web アドレス、ビデオのサムネイル .png 画像 (サイズは 1280 x 720 ピクセル) を入力します。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

オファーのリスト登録の作成について詳しくは、「[オファーの掲載のベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)」を参照してください。

## <a name="technical-configuration"></a>技術的な構成

Power BI サービスでアプリを運用環境に昇格させ、顧客がアプリをインストールできるようにするための Power BI アプリ インストーラー リンクを提供します。 詳しくは、「[Power BI でダッシュボードとレポートを含むアプリを発行する](https://docs.microsoft.com/power-bi/service-create-distribute-apps)」をご覧ください。

## <a name="supplemental-content"></a>補足コンテンツ

検証に役立つオファーに関する追加情報を提供します。 この情報は顧客に表示されることも、マーケットプレースに公開されることもありません。

### <a name="validation-assets"></a>検証アセット

Microsoft 検証チームでのアプリの構成、接続、テストに役立つように、必要に応じて、手順 (最大 3,000 文字) を追加します。 [データに接続] オプションをテストするために使用できる一般的な構成設定、アカウント、パラメーター、またはその他の情報を含めます。 この情報は、検証チームだけに表示され、検証目的のみに使用されます。

## <a name="review-and-publish"></a>レビューと発行

オファーの必須セクションをすべて完了したら、レビューおよび公開のためにそのオファーを送信することができます。

ポータルの右上隅で、 **[レビューと公開]** を選択します。

レビュー ページでは、次のことができます。

- オファーの各セクションの完了状態を確認する。 オファーの全セクションに完了の印が付くまで公開できません。
  - **[未開始]** - セクションは着手されておらず、完了する必要があります。
  - **未完了** - 修正が必要なエラーがセクションにあり、追加情報を入力する必要があります。 ガイダンスについては、このドキュメントの前のセクションを参照してください。
  - **完了** - セクションには必要なすべてのデータがあり、エラーはありません。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- アプリが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 また、オファーの理解に役立つ補足事項を提供します。

オファーを送信して公開するには、 **[公開]** を選択します。

オファーのプレビュー バージョンが確認および承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。 オファーを一般に (プライベート オファーの場合は、プライベート対象ユーザーに) 公開するには、パートナー センターに移動して、 **[一般公開する]** を選択します。
