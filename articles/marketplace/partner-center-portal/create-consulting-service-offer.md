---
title: パートナー センターでコンサルティング サービス オファーを作成する - Azure Marketplace
description: パートナー センターを使用して、コンサルティング サービス オファーを Azure Marketplace または AppSource に公開する方法について説明します。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869793"
---
# <a name="create-a-consulting-service-offer"></a>コンサルティング サービス オファーを作成する

> [!IMPORTANT]
> コンサルティング サービス オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、Cloud パートナー ポータル向けの「[Azure と Dynamics 365 のコンサルティング サービス プラン](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer)」の手順に従ってオファーを管理してください。

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com/) または [AppSource](https://appsource.microsoft.com/) のいずれかにコンサルティング サービス オファーを公開する方法について説明します。 AppSource 上で Microsoft [Dynamics 365](https://dynamics.microsoft.com/) および Power Platform に基づくコンサルティング サービス オファーを一覧表示します。 Azure Marketplace 上で Microsoft Azure に基づくコンサルティング サービス オファーを一覧表示します。

Azure Marketplace または AppSource コンサルティング サービスでコンサルティング サービス オファーを作成するには、最初に[パートナー センターに公開元アカウントを用意](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)し、ご利用のアカウントをコマーシャル マーケットプレース プログラムに登録する必要があります。 オファーを作成する前に、「[コンサルティング サービスの前提条件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)」にある前提条件を確認してください。

## <a name="publishing-benefits"></a>公開の利点

コマーシャル マーケットプレースに公開する利点: 

- Microsoft のブランドを使用して、会社の宣伝ができる。
- AppSource 上で 1 億を超える Office 365 および Dynamics 365 ユーザーに、さらに Azure Marketplace を通じて 20 万を超える組織にリーチできる可能性がある。
- これらのマーケット プレースから質の高い潜在顧客を得ることができる。
- Microsoft のフィールド チームとテレセールス チームによりサービスを宣伝してもらえる。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

前述の要件を満たした後、次の手順に従ってコンサルティング サービス オファーを作成します。

1. [パートナー センター](https://partner.microsoft.com)にログインし、次に上部のメニューから **[ダッシュボード]** を選択します。
2. 左側のナビゲーション バーで **[商業マーケットプレース]** を選択して、 **[概要]** を選択します。

    :::image type="content" source="media/cs-menu-overview.png" alt-text="コマーシャル マーケットプレースのメニューを示します":::

3. **[+ 新しいオファー]** を選択してから、 **[コンサルティング サービス]** を選択します。

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="新しいオファーを作成するためのボタンを示します。":::

4. **オファー ID** を入力します。 自分のアカウント内の各オファーに対する一意の識別子です。

    - この ID は、マーケットプレース オファーの Web アドレスで顧客に表示されます。
    - 小文字、数字、ダッシュ、およびアンダースコアのみを使用してください。スペースは使用できません。 長さは 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの URL は `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
    - **[作成]** を選択した後で、オファー ID を変更することはできません。

5. **オファーの別名**を入力します。 これは、パートナー センター内でオファーを参照するために使用する名前です。

    - この名前はマーケットプレースでは使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。 このフィールドを使用すると、オファーを内部で識別するのにより役立つ名前をオファーに割り当てることができます。これは顧客には表示されません。
    - **[作成]** を選択した後で、オファーの別名を変更することはできません。

この 2 つの値を入力したら、 **[作成]** を選択して、 **[オファーのセットアップ]** ページに進みます。

## <a name="offer-setup"></a>オファーのセットアップ

オファー ID とオファーの別名を入力すると、パートナー センターにオファーの下書きが作成され、 **[オファーのセットアップ]** ページが表示されます。 オファーを設定するには、次の手順に従います。

### <a name="connect-lead-management"></a>リード管理の接続

パートナー センターを使用してオファーをマーケットプレースに公開する場合は、それを顧客関係管理 (CRM) またはマーケティング自動化システムに接続する_必要があります_。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. **[接続]** を選択して、潜在顧客の送信先となる場所を指定します。 パートナー センターでは、次のシステムがサポートされています。

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > ご利用の CRM システムが上記に記載されていない場合は、[Azure テーブル](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)または [HTTPS エンドポイント](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)を使用して潜在顧客のデータを格納し、次にそのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで公開する際は、オファーを潜在顧客の宛先に接続します。
3. 潜在顧客の宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開したら、接続が検証され、潜在顧客のテストが送信されてきます。 オファーの公開前に行うプレビュー中には、プレビュー環境でオファーを自分で購入してみることで、潜在顧客の接続をテストすることもできます。
4. 潜在顧客を失わないように、潜在顧客への接続が最新の状態に保たれていることを確認します。

その他の潜在顧客管理リソースをいくつか次に示します。

- [潜在顧客管理の概要](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [リード管理に関する FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [一般的なリード構成エラー](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在顧客管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)

**[下書きの保存]** を選択してから、次のセクション「Properties」に進みます。

### <a name="properties"></a>Properties

このページでは、コンサルティング サービス オファーで最もよくカバーする主要な製品の設定、コンサルティング サービスの種類の設定、適用可能な製品の選択を行うことができます。

1. ドロップダウン リストから **[主要な製品]** を選択します。
2. ドロップダウン リストから **[コンサルティングサービスの種類]** を選択します。

    - **評価**: 顧客の環境を評価してソリューションの適用性を確認し、コストとタイミングの見積りを提供します。
    - **ブリーフィング**: ソリューションまたはコンサルティング サービスの概要を紹介し、フレームワーク、デモ、顧客事例を使って顧客の関心を引きます。
    - **実装**: 完全に機能するソリューションを生成する完全なインストール。 2 週間以内に実装できるソリューションに限定します。
    - **概念実証**: ソリューションがお客様の要件を満たすかどうかを判断するために、限られた範囲の実装を行います。
    - **ワークショップ**: 顧客の施設内で実施される対話形式のエンゲージメント。 これには、トレーニング、ブリーフィング、評価、または顧客のデータまたは環境に対して構築されたデモを含めることができます。

1. **Azure** の主要な製品を選択した場合は、**ソリューションの分野**を最大 3 つ選択します。 これにより、Azure Marketplace の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 Azure を選択しなかった場合は、この手順をスキップします。
2. Azure _以外の_主要製品を選択した場合は、**適用対象の製品**を最大 3 つ選択します。 これにより、AppSource の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 詳細については、「[Microsoft AppSource のコンサルティング サービスの掲載に関するガイドライン](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)」 (PDF) を参照してください。
3. 貴社のオファーが適用される最大 6 つの **業界**を選択します。 これにより、顧客は貴社のオファーをさらに容易に見つけられるようになります。
4. コンサルティング サービス オファーのリストに表示するために獲得した**コンピテンシー** を 3 つまで追加できます。 Azure Expert MSP および Azure Networking MSP を除き、少なくとも 1 つのコンピテンシーが必要です。

**[下書きの保存]** を選択してから、次のセクション「オファーのリスト登録」に進みます。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、マーケットプレースに表示されるオファーの詳細を定義します。 これには、オファー名、説明、画像などが含まれます。 このオファーを構成するときには、[Microsoft のポリシーに関するページ](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)に詳述されているポリシーに必ず従ってください。

> [!NOTE]
> オファーの詳細が「&quot;このアプリケーションは、[英語以外の言語] でのみ利用可能です&quot;」という文言で始まっていれば英語である必要はありません。また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録の詳細説明で使用されている言語とは異なる言語で提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファリングのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようにすることができます。

- 商標登録されている可能性があります (商標または著作権マークを含めることもできます)。
- 文字長が 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明を入力します。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="description"></a>説明

オファーの詳しい説明 (最大 3,000 文字) を入力します。 これは、マーケットプレースのリスト登録に関する概要で顧客に表示されます。

説明には、次の 1 つまたは複数を含めます。

- オファーによってもたらされる価値と主なメリット
- カテゴリまたは業界のアソシエーション、あるいはその両方
- アプリ内購入の機会
- 必要な開示

説明を記述するためのいくつかのヒントを次に示します。

- 説明の先頭のいくつかの文で、オファーの価値を明確に説明します。 次の項目を含めます。
  - プランの説明。
  - オファーから利益を得るユーザーの種類。
  - 顧客は、オファーのアドレスを必要とします。またはそれを発行します。
- 先頭のいくつかの文が検索結果に表示される場合があることに注意してください。
- 特徴や機能に頼って製品を販売しないでください。 代わりに、オファーによって提供される値に焦点を当ててください。
- 業界固有の語彙または利益に基づく表現を使用するようにしてください。

説明をより魅力的なものにするには、リッチ テキスト エディターを使用して説明を書式設定してください。 リッチ テキスト エディターを使用すると、数字、箇条書き、太字、斜体、およびインデントを追加して、説明を読みやすくすることができます。

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="オファーの説明を記述するリッチ テキスト エディターを示します。" border="false":::

### <a name="keywords"></a>Keywords

主要な製品およびコンサルティング サービスに関連する検索キーワードを最大で 3 つ入力します。 これらを使用することで、オファーをより容易に見つけることができます。

### <a name="duration"></a>Duration

顧客とのこの契約の想定期間を設定します。

### <a name="contact-information"></a>連絡先情報

**第 1** および**第 2** の連絡先として名前、電子メール アドレス、および電話番号を入力する必要があります。 この情報はお客様には表示されません。 これは Microsoft に提供されます。さらにクラウド ソリューション プロバイダー (CSP) パートナーに提供される場合があります。

### <a name="supporting-documents"></a>サポート ドキュメント

オファー用のサポート ドキュメント (PDF) を最大 3 つ (ただし、少なくとも 1 つ) を追加します。

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて .png 形式である必要があります。 ぼやけた画像は拒否されます。

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

オファーのロゴの .png ファイルを次の各ピクセル サイズで提供します。

- **小 (48 x 48)**
- **大 (216 x 216)**

すべてのロゴが必要であり、マーケットプレースの登録情報のさまざまな場所で使用されます。

#### <a name="screenshots-optional"></a>スクリーンショット (省略可能)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 それぞれを 1280 x 720 ピクセルのサイズおよび .png 形式とする必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 4 つ追加します。 ビデオの名前、Web アドレス (URL)、およびビデオのサムネイル .png 画像 (1280 x 720 ピクセルのサイズ)。

**[下書きの保存]** を選択してから、次のセクション「価格と可用性」に進みます。

## <a name="pricing-and-availability"></a>価格と可用性

ここでは、価格、市場、秘密キーなどの要素を定義します。

1. **市場**: オファーを利用可能にする市場を設定します。 オファーごとに市場を 1 つだけ選択できます。
    1. 米国またはカナダの市場では、 **[Edit states]\(州の編集\)** (または**州**) を選択して、オファーを利用可能にする場所を指定します。
2. **プレビュー対象ユーザー**: オファーのプライベート対象ユーザーを設定するには、 **[キーの非表示]** を構成します。
3. **価格**: オファーが**無料**オファーまたは**有料**オファーのいずれであるかを指定します。

    > [!NOTE]
    > コンサルティング サービス オファーは、登録情報のみを対象としています。 トランザクションは、コマーシャル マーケットプレースの外部で直接行われます。

4. 有料オファーの場合は、**価格と通貨**を指定すると共に、価格が**固定**であるか**見積もり**であるかを指定します。 見積もりの場合は、価格に影響を与える要因を説明に指定する必要があります。
5. **[下書きの保存]** を選択します。

## <a name="review-and-publish"></a>[レビューと公開]

オファーの必須セクションをすべて完了したら、レビューおよび公開のためにそのオファーを送信することができます。

1. コンサルティング サービス オファーを公開する準備ができたら、 **[レビューと公開]** をクリックします。
2. 最終送信ページで詳細を確認します。
3. オファーの詳細のいずれかで説明が必要だと思われる場合は、必要に応じて認定チームへのメモを書き込んでください。
4. 準備ができたら、 **[送信]** を選択します
5. **[オファーの概要]** ページに、オファーがどのような公開段階にあるかが示されます。

オファーの各公開ステージでの予想される所要期間の詳細については、「[Commercial Marketplace のオファーの発行状態を確認する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)」をご覧ください。

## <a name="update-your-existing-consulting-service-offers"></a>既存のコンサルティング サービス オファーを更新する

- [コマーシャル マーケットプレースで既存のオファーを更新する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
