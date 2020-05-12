---
title: Azure Container オファーを作成する - Azure Marketplace
description: この記事では、Container オファーを作成して Azure Marketplace に公開する方法について説明します。
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: f2a3b6355fdaa8cd90525fac9d29324270dfad02
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791937"
---
# <a name="create-an-azure-container-offer"></a>Azure Container オファーを作成する

> [!IMPORTANT]
> 現在、Azure Container オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、Cloud パートナー ポータル向けの「[Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)」の手順に従って、オファーを管理してください。

この記事では、Azure Marketplace 向けの Container オファーを作成して公開する方法について説明します。 開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)していなければ、作成します。 アカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure Container]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-azure-container.png)

> [!TIP]
> オファーを公開した後にパートナー センターで編集した内容は、オファーの再公開後にのみネットショップに表示されます。 変更後は必ず再公開してください。

### <a name="offer-id-and-alias"></a>オファーの ID と別名

**[オファー ID]** を入力します。 これは、ご自分のアカウント内の各オファーに対する一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** の選択後にこれを変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-overview"></a>オファーの概要

**[オファーの概要]** ページでは、このオファー (完了しているものと近日公開のものの両方) を公開するために必要な手順と各手順の完了に必要な時間が図で示されます。

このページには、オファーの現在の状態に基づくさまざまなリンクが示されます。 次に例を示します。

- オファーがドラフトの場合 - [ドラフトのオファーを削除する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- オファーが発行中の場合 - [オファーの販売を停止する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 公開元のサインアウトを完了していない場合 - [公開を取り消す](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>オファーのセットアップ

オファーを設定するには、次の手順に従います。

### <a name="connect-lead-management--optional"></a>リード管理の接続 - 省略可能

パートナー センターを使用してオファーをコマーシャル マーケットプレースに発行する場合、それを顧客関係管理 (CRM) システムに接続できます。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. **潜在顧客の送信先となるリードのターゲットを選択します**。 パートナー センターでは、次の CRM システムがサポートされています。

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> ご利用の CRM システムが上記に記載されていない場合は、[Azure テーブル](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)または [HTTPS エンドポイント](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)を使用して潜在顧客のデータを格納し、次にそのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで公開する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft により接続が検証され、テスト リードが送信されます。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [リード管理の概要](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [リード管理に関する FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [一般的なリード構成エラー](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)

続行する前に、 **[下書きの保存]** を選択します。

### <a name="properties"></a>Properties

このページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと、オファーがサポートされる法的契約を定義できます。

#### <a name="category"></a>カテゴリ

少なくとも 1 つ (最大 5 つ) のカテゴリを選択します。 これらのカテゴリは、オファーを適切なマーケットプレース検索領域に配置するために使用され、オファーの詳細ページに表示されます。 オファーの説明では、これらのカテゴリにオファーがどのように対応しているかを説明します。 コンテナーは、 **[コンテナー]** の下、そして **[コンテナー イメージ]** カテゴリに表示されます。

#### <a name="legal"></a>法的情報

オファーの使用条件を指定する必要があります。 2 つのオプションがあります。

- Microsoft 商業マーケットプレース向け標準契約を使用する。
- 独自の使用条件を指定する。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業マーケットプレースの標準契約

Microsoft では、商業マーケットプレースの取り引きに役立つ標準契約テンプレートをご用意しています。 [標準契約] の下でソリューションを提供することを選択できます。この場合、顧客には確認し、同意することが一度だけ求められます。 これは、独自の使用条件を作成しない場合に適しています。

標準契約の詳細については、「[Microsoft 商業マーケットプレースの標準契約](https://docs.microsoft.com/azure/marketplace/standard-contract)」を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF をダウンロードすることもできます (ポップアップ ブロッカーをオフにしてください)。

標準契約を使用するには、 **[Microsoft 商業マーケットプレース向け標準契約を使用する]** チェックボックスを選択し、 **[承諾]** をクリックします。

> [!NOTE]
> Microsoft 商業マーケットプレースの標準契約を使用してオファーを公開した後に、独自のカスタムの使用条件を使用することはできません。 ソリューションは、標準契約または独自の使用条件のどちらか一方の下で提供します。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="[Microsoft 商業マーケットプレース向け標準契約を使用する] チェックボックスの画像。":::

##### <a name="your-own-terms-and-conditions"></a>独自の使用条件

独自のカスタムの使用条件を指定するには、 **[使用条件]** ボックスにそれを入力します。 このボックスに入力できるテキスト文字数は無制限です。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

**[下書きの保存]** を選択してから、次のセクション「オファーのリスト登録」に進みます。

## <a name="offer-listing"></a>オファーのリスト登録

このページでは、商業マーケットプレースに表示されるオファーの詳細を定義できます。 これには、オファーの名前、説明、画像などが含まれます。

> [!NOTE]
> オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、オファーの詳細は英語である必要はありません。 また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録の詳細説明で使用されているものとは異なる言語で提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファーのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようになります。

- 商標を使用できます (商標や著作権の記号を含めても構いません)。
- 長さは 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明です。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="long-summary"></a>詳細な概要

オファーのさらに詳細な説明です。 これは最大 256 文字で、マーケットプレースの検索結果で使用されます。

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
  - オファーから利益を得るユーザーの種類
  - オファーが対応する顧客のニーズや問題。
- 先頭のいくつかの文が検索結果に表示される場合があることに注意してください。
- 特徴や機能に頼って製品を販売しないでください。 代わりに、オファーによって提供される価値に焦点を当てます。
- 業界固有の語彙または利益に基づく表現を使用するようにしてください。

オファーの**説明**をより魅力的なものにするには、リッチ テキスト エディターを使用して説明を書式設定してください。 番号付け、箇条書き、太字、斜体、およびインデントを使用して、説明を読みやすくします。

:::image type="content" source="media/text-editor2.png" alt-text="リッチ テキスト エディターの画像。" border="false" :::

- このドロップダウンを使用して、テキストに段落スタイルを適用します。

    :::image type="content" source="media/text-editor3.png" alt-text="リッチ テキスト エディターのテキスト スタイル コントロールの画像。" border="false":::

- これらのアイコンを使用して、テキストに番号付けまたは箇条書きを適用します。

     :::image type="content" source="media/text-editor4.png" alt-text="リッチ テキスト エディターの箇条書きと番号付きリストのコントロールの画像。" border="false":::

- これらのアイコンを使用して、テキストのインデントを追加または削除します。

    :::image type="content" source="media/text-editor5.png" alt-text="リッチ テキスト エディターのインデント コントロールの画像。" border="false":::

#### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

組織のプライバシー ポリシーの Web アドレスを入力します。 ご自身でオファーが確実にプライバシーに関する法律や規則に準拠するようにしていただく必要があります。 また、Web サイトに有効なプライバシー ポリシーを投稿する責任もあります。

#### <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 最大 25 個のリンクを追加できます。 リンクを追加するには、 **[+ Add a link]\(+ リンクを追加\)** を選択し、次のフィールドに入力します。

- **[タイトル]** - オファーの詳細ページでこれが顧客に表示されます。
- **[リンク (URL)]** - 顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。 リンクの先頭は http:// か https:// でなければなりません。

### <a name="contact-information"></a>連絡先情報

**サポートの連絡先**と**エンジニアリングの連絡先**の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されませんが、Microsoft で利用可能になります。 また、クラウド ソリューション プロバイダー (CSP) パートナーに提供される場合もあります。

- サポートの連絡先 (必須):サポートに関する一般的な質問用です。
- エンジニアリングの連絡先 (必須):技術的な質問と認定に関する問題用です。
- CSP プログラムの連絡先 (省略可能):リセラーからの CSP プログラムに関する質問用です。

**[サポートの連絡先]** セクションで、 **[サポート Web サイト]** を指定します。ここで、グローバル Azure、Azure Government、またはその両方におけるオファーの提供状況に基づき、パートナーはオファーのサポートを検索できます。

**[CSP プログラムの連絡先]** セクションで、リンク (**CSP プログラムのマーケティング資料**) を入力します。ここで、CSP パートナーはオファーのマーケティング資料を検索できます。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

オファーのリスト登録の作成について詳しくは、「[オファーの掲載のベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)」を参照してください

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 ぼやけた画像は拒否されます。

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

 オファーのロゴの PNG ファイルを次の 4 つのピクセル サイズで提供します。

- **小** (48 x 48)
- **中** (90 x 90)
- **大** (216 x 216)
- **ワイド** (255 x 115)

4 つすべてのロゴが必要であり、マーケットプレースのリスト登録のさまざまな場所で使用されます。

#### <a name="screenshots-optional"></a>スクリーンショット (省略可能)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 それぞれが 1280 x 720 ピクセルのサイズで PNG 形式である必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 5 つ追加します。 ビデオの名前、Web アドレス、ビデオのサムネイル PNG 画像 (1280 x 720 ピクセルのサイズ) を入力します。

#### <a name="offer-examples"></a>プランのサンプル

次の例は、オファーのリスト登録のフィールドが、オファーのさまざまな場所でどのように表示されるかを示しています。

これは、Azure Marketplace の **[オファーのリスト登録]** ページを示しています。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Azure Marketplace の [オファーのリスト登録] ページの画像。" :::

これは、Azure Marketplace の検索結果を示しています。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Azure Marketplace の検索結果の画像。":::

これは、Azure portal の **[オファーのリスト登録]** ページを示しています。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Azure portal の [オファーのリスト登録] ページの画像。":::

これは、Azure portal の検索結果を示しています。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Azure portal の検索結果の画像。":::

## <a name="preview"></a>プレビュー

[プレビュー] タブでは、一般公開する前にオファーを検証するため、限定の**プレビュー対象ユーザー**を選択できます。

> [!IMPORTANT]
> **[プレビュー]** でオファーを表示したら、 **[一般公開する]** を選択し、オファーを一般公開する必要があります。

Azure サブスクリプション ID GUID を使用してプレビュー対象ユーザーを指定し、省略可能な説明をそれぞれに付けます。 これらのフィールドのいずれも顧客は表示できません。

> [!NOTE]
> Azure サブスクリプション ID は、Azure portal の [サブスクリプション] ページにあります。

Azure サブスクリプション ID は少なくとも 1 つ追加します。個別に追加するか (最大 10 個)、CSV ファイルをアップロードします (最大 100 個)。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に一般公開されている場合、オファーの変更や更新をテストするためにプレビュー対象ユーザーを選択できます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 **プレビュー**対象ユーザーは、([可用性] タブで設定される) **プライベート**対象ユーザーにのみ公開されるものを含め、マーケットプレースで一般公開される前にすべてのオファー プランを閲覧し、確定できます。

続行する前に、 **[下書きの保存]** を選択してください。

### <a name="plan-overview"></a>プランの概要

このタブでは、同じオファー内でさまざまなプラン オプションを指定できます。 これらのプランは以前、SKU、つまり、Stock Keeping Unit と呼ばれていました。 プランは、利用できるクラウド (グローバル クラウド、Government クラウドなど) とプランで参照される画像に関して異なる場合があります。 商業マーケットプレースにオファーを登録するには、プランを少なくとも 1 つ設定する必要があります。

プランの作成後、 **[プランの概要]** タブが表示されます。

- プラン名
- 価格モデル
- クラウドの可用性 (グローバルまたは Government)
- 現在の公開状態
- 利用できるアクション

[プランの概要] で使用可能なアクションは、プランの現在の状態によって異なります。 具体的な内容を次に示します。

- **ドラフトを削除する** - プランの状態がドラフトの場合。
- **販売プランを停止する** - プランの状態が一般公開の場合。

#### <a name="create-new-plan"></a>新しいプランを作成する

**[新しいプランの作成]** を選択します。 **[新しいプラン]** ダイアログ ボックスが表示されます。

**[プラン ID]** ボックスでこのオファーのプランごとに一意のプラン識別子を作成します。 この ID は、製品の Web アドレスで顧客に表示されます。 小文字、数字、ダッシュ、アンダースコアのみを使用し、最大文字数は 50 です。

> [!NOTE]
> **[作成]** を選択した後で、プラン ID を変更することはできません。

**[プラン名]** ボックスに、このプランの名前を入力します。 この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 このオファーのプランごとに一意の名前を作成します。 たとえば、**Windows Server 2016** と **Windows Server 2019** のプランで **Windows Server** のオファー名を使用できます。

### <a name="plan-setup"></a>プランのセットアップ

このタブでは、プランが利用できるクラウドを選択できます。 このタブでの設定により、他のタブに表示されるフィールドが変わります。

#### <a name="cloud-availability"></a>クラウドでの利用可否

プランは、少なくとも 1 つのクラウドで利用できる必要があります。

**[Azure Global]** オプションを選択すると、商業マーケットプレースを使用するすべてのグローバル Azure リージョンで顧客がプランを使用できます。 詳細については、「[利用可能な地域と通貨サポート](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)」を参照してください。

[ **[Azure Government クラウド]** ](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) オプションを選択すると、ソリューションがここに表示されます。 これは政府機関コミュニティ クラウドであり、米国連邦、州、地方、部族の各政府機関の顧客と、彼らにサービスを提供する資格があるパートナー向けにアクセスが規制されています。 公開元はこのクラウド コミュニティのコンプライアンス制御、セキュリティ対策、ベスト プラクティスに責任を負います。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。

Azure Government に[公開](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)する前に、その領域内でソリューションをテストし、確認します。これは結果が異なる場合があるためです。 ソリューションを作成し、テストするには、[Microsoft Azure 政府機関向け試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に対する試用版アカウントの申請を行います。

> [!NOTE]
> プランが公開され、特定のクラウドで利用できるようになったら、そのクラウドは削除できません。

#### <a name="azure-government-cloud-certifications"></a>Azure Government クラウドの認定

このオプションは、 **[Cloud availability]\(クラウドの利用可否\)** で **[Azure Government クラウド]** を選択した場合にのみ表示されます。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。

これらのプログラムの認定資格を示すため、それらを説明するリンクを最大 100 個指定できます。 プログラムのリスト登録に直接リンクするか、独自の Web サイトにリンクできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

## <a name="plan-listing"></a>プランのリスト登録

このタブには、現在のオファー内の異なるプラン別に特定の情報が表示されます。

### <a name="plan-name"></a>プラン名

ここは、プランの作成時に付けた名前が事前入力されます。 この名前は、必要に応じて変更できます。 最大 50 文字にすることができます。 この名前は Azure Marketplace と Azure portal でこのプランのタイトルとして表示されます。 プランを使用する準備ができた後、既定のモジュール名として使用されます。

### <a name="plan-summary"></a>プラン概要

(オファーではなく) ソフトウェア プランの簡単な概要です。 この概要は Azure Marketplace の検索結果に表示されます。最大 100 文字を入力できます。

### <a name="plan-description"></a>プランの説明

このソフトウェア プラン独特の要素やオファーに含まれる他のプランとの違いを説明します。 オファーについては説明しないでください。プランの説明だけです。 この説明は Azure Marketplace と Azure portal の **[オファーのリスト登録]** ページに表示されます。 プラン概要に入力したものと同じ内容にすることができます。最大 2,000 文字を入力できます。

これらのフィールドに入力した後、 **[保存]** を選択します。

#### <a name="plan-examples"></a>プランの例

次の例は、プランのリスト登録フィールドが、さまざまなビューでどのように表示されるかを示しています。

以下はプラン詳細を閲覧するときの Azure Marketplace のフィールドです。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Azure Marketplace でプラン詳細を閲覧するときに表示されるフィールドの画像。":::

以下は Azure portal のプラン詳細です。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Azure portal のプラン詳細の画像。":::

## <a name="plan-availability"></a>プランの可用性

公開したオファーを非表示にし、マーケットプレースで顧客がそれを検索、閲覧、購入できないようにする場合、 **[可用性]** タブで **[プランの非表示]** チェックボックスを選択します。

このフィールドは、次の場合に使用します。

- オファーが別のアプリケーションを経由して参照されるときに間接的に使用されることを意図している。
- オファーは個別に購入できない。
- プランは最初のテストに使用されたが、無関係となった。
- プランは一時的または季節的なオファーに使用されたが、提供されなくなった。

## <a name="technical-configuration"></a>技術的な構成

コンテナー イメージは、プライベートの [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) でホストされる必要があります。 **[技術的な構成]** タブで、Azure Container Registry 内のコンテナー イメージ リポジトリの参照情報を指定します。

オファーの公開後、特定のパブリック コンテナー レジストリでコンテナー イメージが Azure Marketplace にコピーされます。 コンテナー イメージの使用要求はすべて、Azure Marketplace パブリック コンテナー レジストリからサービスが提供されます (プライベートのものからではありません)。 詳細については、[Azure Container の技術資産の準備](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)に関する記事を参照してください。

### <a name="image-repository-details"></a>イメージ リポジトリの詳細

**[イメージ リポジトリの詳細]** タブに次の情報を入力します。

**Azure サブスクリプション ID** - 使用状況が報告され、コンテナー イメージが含まれる Azure Container Registry に対してサービスが課金されるサブスクリプション ID を入力します。 この ID は Azure portal の[[サブスクリプション] ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にあります。

**Azure リソース グループ名** - Azure Container Registry とコンテナー イメージが含まれる[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)の名前を指定します。 リソース グループにはサブスクリプション ID (上記) でアクセスできる必要があります。 この名前は Azure portal の [[リソース グループ]](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ページにあります。

**Azure コンテナー レジストリ名** - コンテナー イメージが含まれる [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) の名前を指定します。 このコンテナー レジストリは、前に指定した Azure リソース グループに存在する必要があります。 完全なログイン サーバー名ではなく、レジストリ名のみを含めます。 名前からは必ず **azurecr.io** を除外してください。 レジストリ名は、Azure portal の[[コンテナー レジストリ] ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)にあります。

**Azure Container Registry の管理者ユーザー名** - コンテナー イメージが含まれる Azure Container Registry にリンクされている[管理者ユーザー名](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)を指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 管理者ユーザー名とパスワードを取得するには、Azure コマンド ライン インターフェイス (CLI) で **admin-enabled** プロパティを **[True]** に設定します。 必要に応じて、Azure portal で **[管理者ユーザー]** を **[有効にする]** に設定できます。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="[コンテナー レジストリの更新] ダイアログ ボックスの画像。":::

**Azure Container Registry のパスワード** - コンテナー イメージが含まれる Azure Container Registry に関連付けられている管理者ユーザー名のパスワードを指定します。 このユーザー名とパスワードは、自社からレジストリにアクセスするために必要です。 **[コンテナー レジストリ]**  >  **[アクセス キー]** に移動するか、Azure CLI で [show コマンド](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)を使用することで、Azure portal からパスワードを取得できます。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="[アクセス キー]メニューの画像。":::

**Azure Container Registry 内のリポジトリ名**。 イメージが含まれる Azure Container Registry リポジトリの名前を指定します。 レジストリにイメージをプッシュするときに、リポジトリの名前を含めます。 リポジトリの名前は、[[コンテナー レジストリ]](https://azure.microsoft.com/services/container-registry/) >  **[リポジトリ]** ページに移動すると見つかります。 詳細については、「[Azure Portal でコンテナー レジストリ リポジトリを表示する](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)」を参照してください。

> [!NOTE]
> 設定した名前は変更できません。 アカウント内のオファー別に一意の名前を使用します。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新しいバージョンのオファーのイメージ タグ

更新プログラムを公開したとき、顧客は Azure Marketplace から更新プログラムを自動的に取得できなければなりません。 彼らが更新しない場合、特定のバージョンのイメージを維持できなければなりません。 これを行うには、イメージを更新するたびに新しいイメージ タグを追加します。

### <a name="image-tag"></a>イメージ タグ

このフィールドには、サポートされているすべてのプラットフォームで最新バージョンのイメージを指す**最新**のタグを含める必要があります。 バージョン タグも含める必要があります (たとえば、xx.xx.xx から始まり、xx は数字です)。 複数のプラットフォームを対象とするには、顧客は[マニフェスト タグ](https://github.com/estesp/manifest-tool)を使用する必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。

マニフェスト タグ (最新のタグを除く) はすべて X.Y **-** か X.Y.Z- で始める必要があります。X、Y、Z は整数です。 たとえば、**最新**のタグが 1.0.1-linux-x64、1.0.1-linux-arm32、1.0.1-windows-arm32 を指す場合、これら 6 つのタグをこのフィールドに追加する必要があります。 詳細については、[Azure Container の技術資産の準備](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)に関する記事を参照してください。

> [!NOTE]
> テスト中にイメージを識別できるように、必ずテスト タグをイメージに追加してください。

## <a name="review-and-publish"></a>レビューと公開

オファーの必須セクションをすべて完了したら、レビューと公開のためにそれを送信することができます。

ポータルの右上隅で、 **[レビューと** **公開]** を選択します。

レビュー ページでは、次のことができます。

- オファーの各セクションの完了状態を確認する。 オファーの全セクションに完了の印が付くまで公開できません。
  - **開始前** - 開始されておらず、完了する必要があります。
  - **未完了** - 修正が必要なエラーがあり、追加情報を入力する必要があります。 ヘルプについては、このドキュメントの前のセクションを参照してください。
  - **完了** - エラーなしで必要なデータがすべて含まれています。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- オファーが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 また、オファーの理解に役立つ補足事項を提供します。

オファーを送信して公開するには、 **[公開]** を選択します。

オファーのプレビュー バージョンが確認および承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。

オファーを一般に (プライベート オファーの場合は、プライベート対象ユーザーに) 公開するには、パートナー センターに移動して、 **[一般公開する]** を選択します。

## <a name="next-step"></a>次のステップ

- [コマーシャル マーケットプレースで既存のオファーを更新する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
