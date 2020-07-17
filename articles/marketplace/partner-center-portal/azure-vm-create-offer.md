---
title: Azure Marketplace で Azure 仮想マシン オファーを作成する
description: 必要な SKU を使用して、Azure Marketplace で仮想マシン オファーを作成する方法について説明します。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 31b8960f5617566a72545510cf03771f7a3bfcbd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82745006"
---
# <a name="create-an-azure-virtual-machine-offer-in-the-azure-marketplace"></a>Azure Marketplace で Azure 仮想マシン オファーを作成する

> [!IMPORTANT]
> 現在、Azure VM オファーの管理を Cloud パートナー ポータルからパートナー センターに移行しています。 オファーが移行されるまでは、引き続き「[仮想マシン オファーを作成する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer)」の手順に従って、Cloud パートナー ポータルでオファーを管理してください。

この記事では、Azure 仮想マシン オファーを作成して [Azure Marketplace](https://azuremarketplace.microsoft.com/) に発行する方法について説明します。 オペレーティング システム、仮想ハード ディスク (VHD)、最大 16 個のデータ ディスクが含まれる Windows ベースと Linux ベースの両方の仮想マシンに対応しています。 開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)していなければ、作成します。 アカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="introduction"></a>はじめに

### <a name="publishing-benefits"></a>公開の利点

Azure Marketplace への発行には、次の利点があります。

- Microsoft のブランドを使用して、会社の宣伝ができます
- Azure Marketplace を通して、1 億人を超える Office 365 と Dynamics 365 のユーザーおよび 20 万を超える組織にリーチできます
- これらのマーケットプレースから質の高いリードを獲得できます
- Microsoft のフィールド チームとテレセールス チームによりサービスを宣伝してもらえます

### <a name="before-you-begin"></a>開始する前に

まだ行っていない場合は、「[仮想マシンのオファー発行ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)」と、次の Azure 仮想マシンの資料を参照してください。

- クイックスタート ガイド
  - [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates)
- チュートリアル
  - [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- サンプル
  - [Linux VM 用 Azure CLI サンプル](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Linux VM 用 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Windows VM 用 Azure CLI サンプル](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Windows VM 用 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です。

エンジニアリング チームは､Microsoft の次のテクノロジについて理解している必要があります｡

- [Azure Services](https://azure.microsoft.com/services/) に関する基本知識
- [Azure アプリケーションそのものとそのアーキテクチャを](https://azure.microsoft.com/solutions/architecture/)設計する方法
- [Azure 仮想マシン](https://azure.microsoft.com/services/virtual-machines/)、[Azure ストレージ](https://azure.microsoft.com/services/?filter=storage#storage)､[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking#networking)に関する実用的な知識

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure 仮想マシン]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-azure-vm.png)

> [!NOTE]
> オファーを公開した後にパートナー センターで編集した内容は、オファーの再公開後にのみネットショップに表示されます。 変更後は必ず再公開してください。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと、Azure PowerShell および Azure CLI (該当する場合) で、顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、ここに「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-setup"></a>オファーのセットアップ

### <a name="test-drive"></a>体験版

体験版は、"購入前に試用する" オプションを提供することで潜在顧客へのオファーを紹介し、その結果、コンバージョンが増加し、見込みの高いリードが生成される優れた方法です。 [体験版の詳細についてはこちらを参照してください](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

一定期間、体験版を有効にするには、 **[体験版を有効にする]** チェック ボックスをオンにします。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

体験版に関するその他のリソース:

- [技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [マーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [体験版の概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ポップアップ ブロックがオフになっていることを確認してください)

### <a name="lead-management"></a>リードの管理

パートナー センターを使用してオファーをコマーシャル マーケットプレースに発行する場合、それを顧客関係管理 (CRM) システムに接続します。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。 **体験版**を有効にする場合は、CRM に接続する必要があります (前のセクションを参照)。それ以外の場合は任意です。

1. 顧客リードの送信先となるリードのターゲットを選択します。 パートナー センターでは、次の CRM システムがサポートされています。
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > ご利用の CRM システムが上記の一覧にない場合は、[Azure テーブル](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)または [HTTPS エンドポイント](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)を使用して顧客リードのデータを格納します。 その後、そのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで発行する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで発行すると、Microsoft で接続を検証して、テスト リードを送信します。 オファーの発行前に行うプレビュー中には、プレビュー環境でオファーを自分でデプロイしてみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="properties"></a>Properties

このページでは、マーケットプレースでのオファーのグループ化に使用されるカテゴリと業界、アプリのバージョン、オファーをサポートする法的契約を定義できます。

### <a name="categories"></a>Categories

少なくとも 1 つ (最大 5 つ) のカテゴリを選択します。 これらのカテゴリは、オファーを適切なマーケットプレース検索領域に配置するために使用されます。 オファーの説明では、これらのカテゴリにオファーがどのように対応しているかを説明します。 仮想マシン オファーは、Azure Marketplace では **[計算]** カテゴリに表示されます。

### <a name="legal"></a>法的情報

オファーの使用条件を指定する必要があります。 2 つのオプションがあります。

- 独自の使用条件を使用する
- Microsoft コマーシャル マーケットプレース向けの標準契約を使用する

#### <a name="use-your-own-terms-and-conditions"></a>独自の使用条件を使用する

独自のカスタム使用条件を指定するには、 **[使用条件]** ボックスに最大 10,000 文字のテキストを入力します。 さらに長い説明が必要な場合は、使用条件を確認できる場所を指し示す 1 つの Web アドレスを入力します。 これはアクティブなリンクとして顧客に表示されます。

顧客は、オファーを試す前にこれらの条件に同意する必要があります。

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレース向けの標準契約を使用する

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法的な複雑さを軽減するため、Microsoft ではコマーシャル マーケットプレース用に標準契約を用意しています。 標準契約でソフトウェアを提供すると、顧客はそれを読んで一度承認するだけで済み、パートナーは独自の使用条件を作成する必要はありません。

標準契約を使用するには、 **[Microsoft のコマーシャル マーケットプレース向け標準契約を使用しますか?]** チェック ボックスをオンにして、ポップアップ ウィンドウで **[受け入れる]** チェック ボックスをオンにします (表示するには、上へのスクロールが必要な場合があります)。

![[新しいオファー] ボタンとコンサルティング サービス オファーが選択されているパートナー センターの [概要] ページの図。](media/use-standard-contract.png)

> [!NOTE]
> コマーシャル マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタム使用条件を使用することはできません。 ソリューションは、標準契約**または**独自の使用条件の下で提供します。

標準契約の詳細については、Microsoft [コマーシャル マーケットプレース](https://docs.microsoft.com/azure/marketplace/standard-contract)の標準契約を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178)は PDF としてダウンロードできます (ポップアップ ブロッカーをオフにしてください)。

##### <a name="standard-contract-amendments"></a>Standard Contract Amendments (標準契約の修正)

標準契約の修正を使用すると、標準契約条件を選択する簡便さを利用しながら、製品やビジネスに合わせた使用条件を作成することができます。 Microsoft 標準契約を既に確認し、同意している場合、お客様に必要なことは契約の修正の確認のみです。 利用可能な修正には、ユニバーサルとカスタムの 2 種類があります。

**ユニバーサル修正** – すべての顧客に対する標準契約に例外なく適用されます。 購入フローにおいてオファーのすべての顧客に示されます。 お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。 オファーごとに 1 つのユニバーサル修正を提供できます。 このボックスに入力できる文字数は無制限です。 これらの条項は、AppSource、Azure Marketplace、Azure portal で、発見と購入のフロー中に顧客に表示されます。

**カスタム修正** – Azure テナント ID を通して特定の顧客を対象にした、標準契約への特殊な修正です。 対象にするテナントを選択できます。 カスタム修正の使用条件は、オファーの購入フローでこのテナントのお客様にのみ示されます。 お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。

最初に、 **[カスタム変更条件の追加 (最大 10)]** を選択します。 オファーごとに 10 件までのカスタム修正条項を提供できます。

- **[カスタム変更条件]** – 独自の変更条件をカスタム変更条件ボックスに入力します。 入力できる文字数は無制限です。 これらのカスタム条件に対して指定したテナント ID の顧客に対してのみ、Azure portal でのオファーの購入フローでこれらの条件が表示されます。
- **[テナント ID]** (必須) – それぞれのカスタム修正で対象にできるテナント ID は 20 個までです。 カスタム修正を追加する場合、Azure での顧客を示すテナント ID を少なくとも 1 つ指定する必要があります。 顧客は、Azure の [プロパティ] でパートナーを検索できます。 ディレクトリ ID の値はテナント ID です (例: 50c464d3-4930-494c-963c-1e951d15360e)。 「[Microsoft Azure と Office 365 のテナント ID の確認](https://www.whatismytenantid.com/)」で、顧客のドメイン名の Web アドレスを使用して顧客の組織のテナント ID を検索することもできます。
- **[説明]** (省略可能) – 修正の対象となる顧客を特定しやすくするために、テナント ID のわかりやすい説明を入力します。

> [!NOTE]
> これらの 2 種類の修正は、互いに重なり合っています。 カスタム修正の対象となるお客様には、購入中に標準契約へのユニバーサル修正も示されます。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="offer-listing"></a>オファーのリスト登録

このページでは、オファーの名前、説明、リンク、連絡先などの詳細を定義できます。

> [!NOTE]
> オファー登録情報の内容 (説明、ドキュメント、スクリーンショット、使用条件など) は、オファーの説明が "このアプリケーションは、<英語以外の言語> でのみ利用可能です" という文言で始まっていれば、英語である必要はありません。 また、オファー登録情報の内容で使用されている言語以外の言語でコンテンツを提供するために、"_役に立つリンクの Web アドレス_" を提供することもできます。

### <a name="marketplace-details"></a>Marketplace の詳細

#### <a name="name"></a>名前

ここで入力する名前は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーを作成したときに **[オファーのエイリアス]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようにすることができます。

- 商標を使用できます (商標や著作権の記号を含めてもかまいません)
- 文字長が 50 文字を超えてはなりません
- 絵文字を含めることはできません。

#### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明です。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

#### <a name="long-summary"></a>詳細な概要

オファーの長い説明を入力します。 これは最大 256 文字長とすることができ、マーケットプレースの検索結果で使用されます。

#### <a name="description"></a>説明

オファーの詳しい説明 (最大 3,000 文字) を入力します。 これは、コマーシャル マーケットプレースの登録情報に関する概要で顧客に表示されます。

説明には、次の 1 つまたは複数を含めます。

- オファーの価値と主な利益
- カテゴリまたは業界の関連付け、あるいはその両方
- アプリ内購入の機会
- 必要な開示

説明を記述するためのいくつかのヒントを次に示します。

- 説明の先頭の数文で、プランの価値提案を明確に説明します。 次の項目を含めます。
  - オファーの説明。
  - オファーから利益を得るユーザーの種類。
  - オファーが対応する顧客のニーズや問題。
- 先頭のいくつかの文が検索エンジンの結果に表示される場合があることに注意してください。
- 特徴や機能に頼ってオファーを販売しないでください。 代わりに、オファーによって提供される価値に焦点を当てます。
- 業界固有または特典ベースの表現を使用します。

オファーの説明をより魅力的なものにするには、リッチ テキスト エディターを使用して書式設定を適用してください。

![リッチ テキスト エディターの使用](./media/rich-text-editor.png)

| <center>テキスト形式を変更します | <center>箇条書きの黒丸または段落番号を追加します | <center>テキスト インデントを追加するか削除します |
| --- | --- | --- |
| <center>![リッチ テキスト エディターを使用してテキスト形式を変更する](./media/text-editor3.png) |  <center>![リッチ テキスト エディターを使用してリストを追加する](./media/text-editor4.png) |  <center>![リッチ テキスト エディターを使用したインデントの設定](./media/text-editor5.png) |

#### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

組織のプライバシー ポリシーへの Web アドレス (URL) を入力します。 オファーがプライバシーに関する法律と規制に準拠していることを確認します。 また、Web サイトに有効なプライバシー ポリシーを掲示する必要もあります。

### <a name="useful-links"></a>便利なリンク

オファーに関する補足のオンライン ドキュメントを提供します。 リンクを追加するには、 **[+ リンクの追加]** を選択し、次のフィールドに入力します。

- **[名前]** – 顧客のために、詳細ページに名前が表示されます。
- **[リンク (URL)]** – 顧客がオンライン ドキュメントを閲覧するためのリンクを入力します。

### <a name="customer-support-links"></a>カスタマー サポート リンク

顧客がサポート チームに連絡できるサポート Web サイトを提供します。

- Azure グローバル サポート Web サイト
- Azure Government サポート Web サイト

### <a name="partner-support-contact"></a>パートナー サポート連絡先

顧客がサポート チケットを開いたときに Microsoft パートナーが使用する連絡先情報を提供します。 これはマーケットプレースには表示されません。

- 名前
- Email
- Phone

### <a name="engineering-contact"></a>エンジニアリングの連絡先

認定に関する問題など、オファーに問題がある場合に Microsoft が使用する連絡先情報を提供します。 これはマーケットプレースには表示されません。

- 名前
- Email
- Phone

### <a name="marketplace-media"></a>Marketplace メディア

オファーで使用するロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 画像がぼやけていると、送信が拒否されます。

>[!Note]
>ファイルのアップロードで問題が発生した場合は、確実に、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされないようにしてください。

#### <a name="marketplace-logos"></a>Marketplace のロゴ

オファーのロゴの PNG ファイルを次の 4 つのピクセル サイズで提供します。

- **小** (48 x 48)
- **中** (90 x 90)
- **大** (216 x 216)
- **ワイド** (255 x 115)

4 つすべてのロゴが必要であり、マーケットプレースの登録情報のさまざまな場所で使用されます。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 各スクリーンショットは、サイズが 1280 x 720 ピクセルの PNG 形式にする必要があります。 各スクリーンショットにはキャプションが含まれている必要があります。

#### <a name="videos"></a>ビデオ

オファーをデモンストレーションするビデオを最大 5 つ追加します。 これらは、外部のビデオ サービスでホストされている必要があります。 各ビデオの名前、Web アドレス、ビデオのサムネイルの PNG 画像 (1280 x 720 ピクセル) を入力します。

マーケットプレースの登録情報に関するその他のリソースについては、[マーケットプレース オファーの登録情報のベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)に関する記事をご覧ください。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="preview"></a>プレビュー

[プレビュー] タブでは、マーケットプレースの幅広い対象ユーザーに対して一般公開する前にオファーを検証するため、限られた**プレビュー対象ユーザー**を選択します。

> [!IMPORTANT]
> プレビューでオファーを確認した後、 **[一般公開する]** を選択して、マーケットプレースのパブリック対象ユーザーにオファーを発行します。

プレビュー対象ユーザーは、Azure サブスクリプション ID の GUID と、それぞれの対するオプションの説明によって識別されます。 いずれのフィールドも顧客は表示できません。 Azure サブスクリプション ID は、Azure portal の **[サブスクリプション]** ページにあります。

少なくとも 1 つの Azure サブスクリプション ID を、個別に (最大 10 個)、または CSV ファイルをアップロードすることによって (最大 100 個) 追加します。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に公開されている場合も、オファーの変更や更新をテストするために、プレビュー対象ユーザーを定義することができます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 プレビュー対象ユーザーは、マーケットプレースで一般公開される "_前に_"、オファーにアクセスすることができます。 オファーがマーケットプレースに完全に公開された後でプライベート対象ユーザーだけが使用できるプランも含めて、すべてのプランを表示し、検証することができます。 プライベート対象ユーザーは ( **[価格と利用可否]** タブで定義します)、特定のプランに排他的にアクセスできます。

**[下書きの保存]** を選択してから、次のセクション「プランの概要」に進みます。

## <a name="plan-overview"></a>プランの概要

パートナー センターの同じオファー内で、異なるプラン オプションを提供できます。 このようなプランは、以前は SKU と呼ばれていました。 オファーには少なくとも 1 つのプランが必要です。プランごとに、収益化対象ユーザー、Azure リージョン、機能、または VM イメージを変えることができます。

プランの作成後、 **[プランの概要]** タブには次の情報が表示されます。

- プラン名
- ライセンス モデル
- 対象ユーザー (パブリックまたはプライベート)
- 現在の発行状態
- 使用可能なアクション

[プランの概要] で使用可能なアクションは、プランの現在の状態によって異なります。 具体的な内容を次に示します。

- **ドラフトの削除** - プランの状態がドラフトの場合
- **プランの販売の停止**または**プライベート対象ユーザーの同期** – プランの状態が公開済みの場合

### <a name="create-new-plan"></a>新しいプランを作成する

上部の **[+ 新しいプランの作成]** を選択します。 **[新しいプラン]** ダイアログ ボックスが表示されます。

**[プラン ID]** ボックスでこのオファーのプランごとに一意のプラン ID を作成します。 この ID は、製品の Web アドレスで顧客に表示されます。 小文字、数字、ダッシュ、アンダースコアのみを使用し、最大文字数は 50 です。

> [!NOTE]
> **[作成]** を選択した後で、プラン ID を変更することはできません。

**[プラン名]** ボックスに、このプランの名前を入力します。 この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 各プランの違いがはっきりわかる一意の名前を作成します。 たとえば、**Windows Server** は、**従量課金制**、**BYOL**、**高度**、**エンタープライズ**などのプランで使用することがあります。

**［作成］** を選択します

### <a name="plan-setup"></a>プランのセットアップ

プランの種類に対する高レベルの構成、別のプランの技術的構成を再利用するかどうか、およびプランを使用可能にする必要のある Azure リージョンを設定します。 ここでの選択により、同じプランの他のタブに表示されるフィールドが決まります。

#### <a name="re-use-technical-configuration"></a>技術的な構成を再利用する

同じ種類のプランが複数あり、それらの間でパッケージが同一である場合は、 **[this plan reuses technical configuration from another plan]\(このプランで別のプランの技術的構成を再利用する\)** を選択できます。 このオプションを使用すると、このオファーに対して同じ種類の他のプランのいずれかを選択し、その技術的構成を再利用できます。

> [!NOTE]
> 別のプランの技術的構成を再利用すると、 **[技術的な構成]** タブ全体がこのプランに対して表示されなくなります。 今後行う更新も含めて、他のプランの技術的構成の詳細が、このプランにも使用されます。 この設定は、このプランを発行した後では変更できません。

#### <a name="azure-regions"></a>Azure Azure リージョン

プランは、少なくとも 1 つの Azure リージョンで利用できる必要があります。

商業マーケットプレースが統合されているすべての Azure グローバル リージョンの顧客がプランを使用できるようにするには、 **[Azure Global]\(Azure グローバル\)** オプションを選択します。 詳細については、「[利用可能な地域と通貨サポート](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)」を参照してください。

[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) リージョンでプランを使用できるようにするには、 **[Azure Government]** オプションを選択しします。 このリージョンは、アメリカの連邦政府顧客、州政府顧客、地方自治体顧客、部族政府顧客と、そのような顧客にサービスを提供する資格があるパートナーに、制御されたアクセスを提供します。 発行元は、コンプライアンス管理、セキュリティ対策、ベスト プラクティスについて責任を持ちます。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。

特定のエンドポイントが異なる可能性があるため、[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) に発行する前に、環境でプランをテストおよび検証します。 プランを設定してテストするには、[Microsoft Azure Government 試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に試用版アカウントを申請します。

> [!NOTE]
> プランが発行され、特定の Azure リージョンで利用できるようになったら、そのリージョンは削除できません。

#### <a name="azure-government-certifications"></a>Azure Government の認定資格

このオプションが表示されるのは、 **[Azure Government]** を選択した場合のみです。

Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。 これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。 これらでは、プログラムでの一覧に直接リンクすることも、独自の Web サイトでのそれらについてのコンプライアンスに関する説明にリンクすることもできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

続行する前に、 **[下書きの保存]** を選択します。

### <a name="plan-listing"></a>プランのリスト登録

ここでは、プランの一覧の詳細を構成します。 このタブには、同じオファーのプラン間で異なる可能性がある特定の情報が表示されます。

#### <a name="plan-name"></a>プラン名

ここには、登録時にプランに付けた名前が事前入力されます。 この名前は、このプランのタイトルとしてマーケットプレースに表示され、100 文字に制限されます。

#### <a name="plan-summary"></a>プラン概要

(オファーではなく) プランについて簡単にまとめた説明です。 この概要は、100 文字までに制限されています。

#### <a name="plan-description"></a>プラン説明

このソフトウェア プラン独特の要素やオファーに含まれる他のプランとの違いを説明します。 オファーについては説明しないでください。プランの説明だけです。 プランの説明は、最大 2,000 文字まで許されます。

続行する前に、 **[下書きの保存]** を選択します。

### <a name="pricing-and-availability"></a>価格と可用性

このタブでは、次の構成を行います。

- このプランを利用できる市場
- 1 時間あたりの価格
- プランをすべてのユーザーに表示するか、それとも特定の顧客 (プライベート対象ユーザー) だけに表示するか

#### <a name="markets"></a>市場

すべてのプランは、少なくとも 1 つの市場で利用できる必要があります。 このプランを購入できるようにするすべての市場の場所のチェック ボックスをオンにします (これらの市場のユーザーは、 **[[プランのセットアップ]](#plan-setup)** で選択されているすべての Azure リージョンにオファーをデプロイできます)。 **[税送金済み]** ボタンは、Microsoft がパートナーに代わって消費税および使用税を送金する国を示します。 中国への発行は、**無料**または**ライセンス持ち込み** (BYOL) のプランに限定されます。

米国ドル (USD) でプランの価格を既に設定していて、別の市場の場所を追加する場合、新しい市場の価格は現時点の為替レートに従って計算されます。 常に、各市場の価格を発行前に確認します。 変更を保存した後、 **[Export prices (xlsx)]\(価格のエクスポート (xlsx)\)** リンクを使用して、価格を確認します。

市場を削除すると、アクティブなデプロイを使用しているその市場の顧客は、新しいデプロイを作成したり、既存のデプロイをスケールアップしたりすることができなくなります。 既存のデプロイへの影響はありません。

#### <a name="pricing"></a>価格

**[License model]\(ライセンス モデル\)** – このプランの価格を構成する場合は **[使用量ベースの月次請求プラン]** を選択し、顧客が既存のライセンスでこのプランを使用できるようにするには **[ライセンス持ち込み]** を選択します。

使用量ベースの月次請求プランについては、次の 3 つの価格設定オプションのいずれかを使用します。

- **[コアあたり]** – 米国ドル (USD) でコアあたりの価格を指定します。 コア サイズあたりの価格が計算され、現在の為替レートを使用して現地通貨に変換されます。
- **[Per core size]\(コア サイズあたり\)** – コア サイズあたりの価格を USD で指定します。 価格は、現在の為替レートを使用して、現地通貨に変換されます。
- **[Per market and core size]\(市場とコア サイズあたり\)** – すべての市場についてコア サイズあたりの価格を指定します。 価格はスプレッドシートからインポートできます。

> [!NOTE]
> 価格データのエクスポートを有効にするには、価格設定の変更を保存します。 プランの市場の価格は、発行した後では変更できません。 価格スプレッドシートをエクスポートして各市場の価格を確認することで、発行前に価格が正しいことを確認してください。

#### <a name="free-trial"></a>無料試用版

1 か月または 3 か月の無料試用版を顧客に提供できます。

#### <a name="visibility"></a>表示

各プランは、すべての対象ユーザーまたは事前に選択した対象ユーザーのみに表示されるように設計できます。 Azure サブスクリプション ID を使用して、この限定対象ユーザーにメンバーシップを割り当てます。

**[パブリック]** – すべての対象ユーザーがプランを見ることができます。

**[プライベート ユーザー]** – 事前に選択した対象ユーザーだけが、プランを見ることができます。 プライベート プランとして発行した後で、対象ユーザーを更新したり、パブリックに変更したりできます。 プランをパブリックにした後は、パブリックのままにしておく必要があります。プライベートに変更することはできません。

**[制限付きの対象ユーザー (Azure サブスクリプション ID)]** – Azure サブスクリプション ID を使用して、このプライベート プランにアクセスできる対象ユーザーを割り当てます。 必要に応じて、割り当てた各 Azure サブスクリプション ID の説明を含めます。 サブスクリプション ID を、手動で (最大 10 個)、または CSV スプレッドシートをインポートして (最大 2 万個) 追加します。 Azure サブスクリプション ID は GUID として表されます。文字は小文字にする必要があります。

> [!NOTE]
> プライベートまたは制限付きの対象ユーザーは、 **[プレビュー]** タブで定義したプレビュー対象ユーザーとは異なります。プレビュー対象ユーザーは、マーケットプレースで公開される "_前の_" オファーにアクセスできます。 プライベート対象ユーザーの選択は特定のプランにのみ適用されますが、プレビュー対象ユーザーは (プライベートであるかどうかに関係なく) 検証のためすべてのプランを表示できます。

#### <a name="hide-plan"></a>プランの非表示

別のソリューション テンプレートまたはマネージド アプリケーションによって参照されることにより間接的にのみ使用されることを意図されている仮想マシンの場合、仮想マシンを発行しても、顧客がそれを検索して直接参照できないようにするには、このチェック ボックスをオンにします。

> [!NOTE]
> 非表示のプランでは、プレビュー リンクはサポートされません。

続行する前に、 **[下書きの保存]** を選択します。

### <a name="technical-configuration"></a>技術的な構成

この計画に関連付けられているイメージとその他の技術プロパティを指定します。 詳しくは、「[Azure VM の技術資産を作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)」をご覧ください。

> [!NOTE]
> **[プランのセットアップ]** タブで別のプランからパッケージを再利用するようにこのプランを構成した場合、このタブは表示されません。

#### <a name="operating-system"></a>オペレーティング システム

- **[オペレーティング システム ファミリ]** – オペレーティングシステムを **[Windows]** または **[Linux]** から選択します
- **[リリース]** または **[ベンダー]** – Windows のリリースまたは Linux のベンダーを選択します
- **[OS フレンドリ名]** – オペレーティング システムのフレンドリ名を選択します。 この名前は顧客に表示されます

#### <a name="recommended-vm-sizes"></a>推奨される VM サイズ

Azure Marketplace に表示する、最大 6 個の推奨される仮想マシン サイズを選択します。

#### <a name="open-ports"></a>ポートを開く

デプロイされた仮想マシンで、パブリック ポートまたはプライベート ポートを開きます。

#### <a name="storage-option-for-deployment"></a>デプロイのストレージ オプション

**[Disk deployment option]\(ディスク デプロイ オプション\)** – 仮想マシンを使用するときにユーザーが使用できるディスクのデプロイの種類を選択します。 マネージド ディスクのデプロイだけにデプロイを制限することをお勧めします。

#### <a name="properties"></a>Properties

**[Support accelerated networking]\(高速ネットワークのサポート\)** – VM で[高速ネットワーク](https://go.microsoft.com/fwlink/?linkid=2124513)をサポートするかどうかを選択します。

#### <a name="vm-images"></a>VM イメージ

仮想マシン イメージのディスク バージョンと SAS URI を指定します。 VM イメージごとに最大 16 個のデータ ディスクを追加します。 特定の送信では、プランごとに新しいイメージ バージョンを 1 つだけ指定します。 イメージを発行した後で、イメージを編集することはできませんが、イメージを削除することはできます。 バージョンを削除すると、新規ユーザーと既存ユーザーのどちらも、削除されたバージョンの新しいインスタンスをデプロイできなくなります。

- **[Disc version]\(ディスクのバージョン\)** は、提供するイメージのバージョンです。
- **[SAS URI]** は、オペレーティング システムの VHD を格納した Azure Storage 内の場所です。
- データ ディスク イメージは、Azure Storage に格納されている VHD の SAS URI でもあります。
- プランでの送信ごとに、イメージを 1 つだけ追加します。

使用するオペレーティング システムに関係なく、ソリューションに必要な最小数のデータ ディスクのみを追加します。 顧客はデプロイ時にイメージの一部であるディスクを削除できませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。

続行する前に **[下書きの保存]** を選択し、 **[プランの概要]** に戻ります。

## <a name="resell-through-csps"></a>CSP を通して再販する

[クラウド ソリューション プロバイダー](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) プログラムでパートナーがオファーを利用できるようにすることで、オファーのリーチを拡大します。 すべてのライセンス持ち込み (BYOL) プランは、自動的にオプトインされます。BYOL ではないプランのオプトインを選択できます。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="test-drive"></a>体験版

購入前に顧客がオファーを試すことができるデモ (体験版) を設定します。 顧客が一定期間オファーを試すことができるデモ環境を作成するには、[コマーシャル マーケットプレースでのオファーの体験版](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)に関する記事をご覧ください。

体験版を有効にするには、[[オファーのセットアップ]](#test-drive) タブで [体験版を有効にする] チェック ボックスをオンにします。オファーから体験版を削除するには、このチェック ボックスをオフにします。

体験版に関するその他のリソース:

- [マーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF、ポップアップ ブロックがオフになっていることを確認してください)

続行する前に、 **[下書きの保存]** を選択します。

## <a name="review-and-publish"></a>レビューと発行

オファーの必須セクションをすべて完了したら、レビューと発行のためにそれを送信することができます。

ポータルの右上隅で、 **[レビューと公開]** を選択します。

このページでは、次のことを行うことができます。

- オファーの各セクションの完了状態を確認する。
  - **[未開始]** – セクションは開始されておらず、完了する必要があります。
  - **[未完了]** – 修正が必要なエラーがセクションにあり、追加の情報を入力する必要があります。 このセクションを更新する方法のガイダンスについては、このドキュメントの前のセクションを参照してください。
  - **[完了]** – セクションは完了しており、エラーはありません。 オファーを送信するには、オファーのすべてのセクションが完了している必要があります。
- **[認定の注意書き]** – アプリが確実に正しくテストされるよう、認定チームにはテストの指示を提供します。 アプリの理解に役立つ補足事項を提供します。

オファーを送信して発行するには、 **[レビューと公開]** を選択します。

オファーのプレビュー バージョンが確認して承認できるようになったら、その旨をお知らせするメールを Microsoft からお送りします。 オファーを一般に (プライベート オファーの場合は、プライベート対象ユーザーに) 発行するには、パートナー センターに移動し、オファーの **[概要]** ページで **[一般公開する]** を選択します。 発行が行われている間、オファーの状態がページの上部に表示されます。

### <a name="errors-and-review-feedback"></a>エラーとフィードバックの確認

発行プロセスの**手動検証**ステップでは、オファーとそれに関連付けられている技術資産の詳細なレビューが行われます。 このプロセスでオファーのエラーが発見された場合は、問題の詳細を示す認定レポートを受け取ります。 必要な修正を行い、オファーを再発行してください。

## <a name="offer-overview"></a>オファーの概要

**[オファーの概要]** ページでは、このオファー (完了しているものと近日発行のものの両方) を発行するために必要な手順と各手順の完了に必要な時間が図で示されます。

このページには、ユーザーの選択に基づき、このオファーで操作を実行するためのリンクが含まれています。 次に例を示します。

- オファーがドラフトの場合 - [ドラフトのオファーを削除する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- オファーが発行中の場合 - [オファーの販売を停止する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- オファーがプレビューの場合 - [公開する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 発行元のサインオフを完了していない場合 - [発行をキャンセルする](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace の例

以下では、Azure Marketplace でのオファーの表示例を示します。

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace でのオファーの詳細

![Azure Marketplace でのオファー詳細画面の例](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure Marketplace での検索結果

![Azure Marketplace での検索詳細画面の例](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure Marketplace でのプランの詳細

![Azure Marketplace でのプラン詳細画面の例](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure portal でのオファーの詳細

![Azure portal でのオファー詳細画面の例](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure portal での検索結果

![Azure portal での検索結果画面の例](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure portal でのプランの詳細

![Azure portal でのプラン詳細画面の例](media/avm-create6.png)

## <a name="next-step"></a>次のステップ

- [コマーシャル マーケットプレースで既存のオファーを更新する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
