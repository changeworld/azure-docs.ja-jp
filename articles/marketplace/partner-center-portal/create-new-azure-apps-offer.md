---
title: 商業マーケットプレースで新しい Azure アプリ オファーを作成する
description: Microsoft パートナー センターの商業マーケットプレース ポータルを使用して、Azure Marketplace、AppSource、クラウド ソリューション プロバイダー (CSP) プログラムでリスト登録または販売を行うために新しい Azure アプリ オファーを作成する方法。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 0ff1bbd976273a7d0cbfb22effebdf45c84d2f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277241"
---
# <a name="create-an-azure-application-offer"></a>Azure アプリケーション オファーを作成する

ここでは、商業マーケットプレースで Azure アプリケーション オファーを発行する手順の概要を説明します。

## <a name="azure-application-offer-type"></a>Azure アプリケーション オファーの種類

このトピックでは、Azure アプリケーション オファーの基礎について説明します。  マーケットプレースで新しい Azure アプリケーション オファーを発行するプロセスを始める前に、これらの概念について理解しておく必要があります。

### <a name="publishing-overview"></a>公開の概要

ビデオ「[Azure Marketplace 向けのソリューション テンプレートとマネージド アプリケーションの作成](https://channel9.msdn.com/Events/Build/2018/BRK3603)」を見ると、Azure アプリケーション オファーの種類の概要がわかります。

* 使用できるオファーの種類
* 必要な技術資産
* Azure Resource Manager テンプレートの作成方法
* アプリ UI の開発とテスト
* アプリ オファーを発行する方法
* アプリケーション レビュー プロセス

### <a name="types-of-azure-application-plans"></a>Azure アプリケーション プランの種類

Azure アプリケーション プランには、マネージド アプリケーションとソリューション テンプレートの 2 種類があります。

* **ソリューション テンプレート**は、マーケットプレースでソリューションを発行するための主要な手段の 1 つです。 このプランの種類は、単一の仮想マシン (VM) の範囲を超えてデプロイと構成の自動化を追加する必要があるソリューションで使用します。  ソリューション テンプレートを使うと、複雑な IaaS ソリューションを提供するための VM、ネットワーク、ストレージ リソースなどの複数のリソースの提供を自動化できます。  ソリューション テンプレートの作成について詳しくは、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」をご覧ください。

* **マネージド アプリケーション**はソリューション テンプレートと似ていますが、大きな違いが 1 つあります。 マネージド アプリケーションでは、アプリの発行元によって管理されているリソース グループが、リソースのデプロイ先となります。 リソース グループはユーザーのサブスクリプション内に存在しますが、そのリソース グループにアクセスできるのは、発行元のテナントの ID です。 発行元は、ソリューションの継続的なサポートにかかるコストを指定します。 フル マネージドのターンキー アプリケーションを簡単に構築して顧客に提供するには、マネージド アプリケーションを使用します。  マネージド アプリケーションの利点と種類について詳しくは、「[Azure マネージド アプリケーションの概要](https://docs.microsoft.com/azure/managed-applications/overview)」をご覧ください。

すべての Azure アプリケーションでは、`.zip` アーカイブのルート フォルダーに少なくとも 2 つのファイルが含まれています。

* [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) という名前の Resource Manager テンプレート ファイル。  これは、顧客の Azure サブスクリプションにデプロイするリソースが定義されているテンプレートです。  Resource Manager テンプレートの例については、[Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/)または対応する [GitHub: Azure Resource Manager クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates) リポジトリをご覧ください。

* [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview) という名前の、Azure アプリケーション作成エクスペリエンス用のユーザー インターフェイス定義。  ユーザー インターフェイスでは、コンシューマーがパラメーター値を入力できるようにする要素を指定します。

すべての新しい Azure アプリケーション オファーには、[Azure パートナーの顧客の使用状況の属性 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) が含まれている必要があります。

### <a name="before-you-begin"></a>開始する前に

クイック スタート、チュートリアル、サンプルが提供されている以下の Azure アプリケーションのドキュメントを確認してください。

* [Azure Resource Manager テンプレートについての理解](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* クイック スタート:

    * [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates)
    * [アプリケーション定義の発行](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [サービス カタログ アプリのデプロイ](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* チュートリアル:

    * [定義ファイルの作成](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [マーケットプレース アプリケーションの発行](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* サンプル:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [マネージド アプリケーション ソリューション](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>技術的な知識の基礎

こうした資産の設計と構築､テストには時間がかかり､Azure プラットフォームとその構築に使用する技術に関する知識が必要です｡

エンジニアリング チームには､Microsoft の次の技術に関する知識も必要です｡

* [Azure のサービス](https://azure.microsoft.com/services/)に関する基本知識。
* [Azure アプリケーションとそのアーキテクチャを設計する](https://azure.microsoft.com/solutions/architecture/)方法。
* [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)、[Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)、[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking#networking)に関する実用的な知識。
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) に関する実用的な知識。
* [JSON](https://www.json.org/) に関する実用的な知識。

### <a name="suggested-tools"></a>推奨ツール

Azure アプリケーションを管理するためのスクリプト環境として、次のいずれか一方または両方を選択します。

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

開発環境には次にツールを加えることを推奨します｡

* [Azure 記憶域エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能:
    * 拡張機能: [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 拡張機能: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 拡張機能: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

使用できるツールについては、[Azure 開発者ツール](https://azure.microsoft.com/tools/)に関するページをご覧ください。  また、Visual Studio を使用している場合は、[Visual Studio Marketplace](https://marketplace.visualstudio.com/) をご覧ください。

## <a name="create-an-azure-application-offer"></a>Azure アプリケーション オファーを作成する

Azure アプリケーション オファーを作成する前に、まず、[パートナー センター アカウントを作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)し、[商業マーケットプレース ダッシュボード](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)を開いて **[概要]** タブを選択する必要があります。

>[!Note]
>オファーが発行されると、パートナー センターで行われたオファーへの編集は、再発行後はシステム内およびネットショップでのみ更新されます。  変更を行った後に、発行のために必ずオファーを送信してください。

### <a name="create-a-new-offer"></a>新しいオファーを作成する

**[+ 新しいオファー]** ボタンを選択し、 **[Azure アプリケーション]** メニュー項目を選択します。 **[新しいオファー]** ダイアログ ボックスが表示されます。

### <a name="offer-id-and-alias"></a>オファーの ID と別名

* **プラン ID**: 自分のアカウントでの各オファーの一意識別子。 この ID は、マーケットプレース オファーの URL アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。 <br> <br> オファー ID は、小文字の英数字である必要があります (ハイフンとアンダースコアは使用できますが、空白は使用できません)。 文字数は 50 文字に制限されており、[作成] を選択した後は変更できません。 <br> <br> たとえば、ここに「`test-offer-1`」と入力すると、オファーの URL は `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。 

* **オファーの別名**:パートナー センター内でオファーを参照するために使用される名前。 この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。 **[作成]** の選択後にこの値を変更することはできません。

**[オファー ID]** と **[オファーのエイリアス]** を入力したら、 **[作成]** を選択します。 これで、オファーの他のすべての部分に関する作業を行うことができるようになります。

## <a name="offer-setup"></a>オファーのセットアップ

**[オファーのセットアップ]** ページでは、次の情報を求められます。 これらのフィールドに入力したら、忘れずに **[保存]** を選択します。

### <a name="test-drive"></a>体験版

体験版は、購入前に試用するオプションを与えることで潜在顧客へのオファーを披露し、その結果、会話が増加し、見込みの高いリードが生成される優れた方法です。 [体験版について詳しくご確認ください。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

体験版を有効にするには、 **[体験版を有効にする]** チェックボックスをオンにします。 その後、顧客が一定期間オファーを試すことができるように、「[体験版の技術的な構成](#types-of-azure-application-plans)」に従って、デモ環境を構成する必要があります。 

>[!Note]
>すべての Azure アプリケーションは Azure Resource Manager テンプレートを使用して実装されるため、Azure アプリケーション用に構成できる体験版の種類は [Azure Resource Manager ベースの体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)だけです。

#### <a name="additional-test-drive-resources"></a>体験版に関するその他のリソース

- [体験版の技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体験版のマーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [1 ページにまとめた体験版の概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>リード管理の接続

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

詳しくは、[リード管理の概要](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。

次のセクションに進む前に、必ず**保存**してください。

## <a name="properties"></a>Properties

**[プロパティ]** ページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと業界、アプリのバージョン、オファーがサポートされる法的契約を定義できます。 このフィールドに入力した後、 **[保存]** を選択します。

### <a name="category"></a>カテゴリ

オファーを適切なマーケットプレース検索区分にグループ分けするために使用されるカテゴリを、最小で 1 つ、最大で 3 つ選択します。 これらのカテゴリにオファーがどのように対応しているかを、オファーの説明に含めるようにします。 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業マーケットプレースの標準契約

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>オファーのリスト登録

オファー登録情報ページに、オファーが登録される言語が表示されます。 現時点では、使用可能なオプションは **[英語 (米国)]** のみです。

言語/市場ごとにマーケットプレースの詳細 (オファーの名前、説明、画像など) を定義する必要があります。 この情報を提供する言語/市場名を選択します。

> [!NOTE]
> オファー登録情報のコンテンツ (説明、ドキュメント、スクリーンショット、使用条件など) は、オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です。」という文言で始まっていれば英語である必要はありません。 また、オファー登録情報のコンテンツで使用されている言語以外の言語でコンテンツを提供するための*役に立つリンクの URL* を提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーの作成時に **[オファーの別名]** に入力したテキストが事前に設定されていますが、この値は変更できます。 この名前は商標の場合もあります (商標または著作権マークを含めることもできます)。 名前は 50 文字以下にする必要があります。絵文字を含めることはできません。

### <a name="summary"></a>まとめ

マーケットプレースの検索結果で使用できる、お客様のオファーの簡単な説明を入力します (最大 100 文字)。

### <a name="long-summary"></a>詳細な概要

オファーの詳しい説明 (最大 256 文字) を入力します。 説明は、マーケットプレースの検索結果で使用される場合があります。

### <a name="description"></a>説明

オファーの詳しい説明 (最大 3,000 文字) を入力します。 この説明は、マーケットプレースの登録情報の概要で顧客に表示されます。 オファーの価値提案、主なメリット、カテゴリまたは業界との関連性、アプリ内の購入機会、必要な情報開示を含めます。 

説明を記述するためのヒントを次に示します。  

- 説明の先頭の数文で、オファーの価値提案を明確に説明します。 価値提案には次の項目を含めます。
  - 製品の説明
  - 製品から利益を得られるユーザーの種類
  - 製品が対応する顧客のニーズや問題
- 先頭の数文は、検索エンジンの結果に表示される可能性があることを留意してください。  
- 特徴や機能に頼って製品を販売しようとせずに、 提供する価値に焦点を当ててください。  
- できるだけ業界固有の語彙や利益に基づく表現を使用します。 
- HTML タグを使用して、説明を書式設定し、より魅力的なものにすることを検討してください。

### <a name="search-keywords"></a>キーワード検索

オファーを顧客がマーケットプレースで検索できるように、必要に応じて最大 3 つの検索キーワードを入力することができます。 最良の結果を得るには、これらのキーワードを説明にも使用するようにします。

### <a name="support-urls"></a>サポート URL

このセクションには、顧客がオファーの詳細を理解するのに役立つリンクを入力できます。

#### <a name="privacy-policy-url"></a>[プライバシー ポリシーの URL]

自分の組織のプライバシー ポリシーへの URL を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

#### <a name="useful-links"></a>便利なリンク

必要に応じて、ソリューションに関する追加のオンライン ドキュメントを提供します。  **[+ Add a link]\(+ リンクを追加\)** をクリックして、その他の役に立つリンクを追加します。

### <a name="contacts"></a>連絡先

このセクションでは、**サポートの連絡先**と**エンジニアリングの連絡先**の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されませんが、Microsoft で利用できるようになり、CSP パートナーに提供される場合もあります。

**[サポートの連絡先]** セクションでは、CSP パートナーがオファーのサポートを確認できる**サポート URL** も指定する必要があります。

### <a name="marketplace-images"></a>マーケットプレースの画像

このセクションでは、顧客にオファーを表示するときに使用されるロゴや画像を指定できます。 画像はすべて .png 形式である必要があります。

#### <a name="store-logos"></a>ストア ロゴ

オファーのロゴを次の 3 つのサイズで提供します: **S (48 x 48)** 、**M (90 x 90)** 、**L (216 x 216)** 。

#### <a name="hero"></a>ヒーロー

ヒーローの画像は省略可能です。 指定する場合は、815 x 290 ピクセルのサイズにする必要があります。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを追加します。 最大で 5 つのスクリーンショットを追加できます。 スクリーンショットはすべて、1280 x 720 ピクセルにする必要があります。

#### <a name="videos"></a>ビデオ

必要に応じて、オファーをデモンストレーションするビデオを最大 5 つ追加することもできます。 これらのビデオは、YouTube または Vimeo でホストされている必要があります。 それぞれのビデオについて、ビデオの名前、URL、ビデオのサムネイル画像 (1280 x 720 ピクセル) を入力します。

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

- [マーケットプレース オファーのリスト登録に関するベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>プレビュー

**[プレビュー]** タブでは、マーケットプレースの幅広い対象ユーザーに対して一般公開する前にオファーを検証するため、限定の**プレビュー対象ユーザー**を定義できます。

> [!IMPORTANT]
> プレビューで自分のオファーを確認した後、マーケットプレースのパブリック対象ユーザーに対して一般公開する前に、 **[一般公開する]** を選択する必要があります。

プレビュー対象ユーザーは、Azure サブスクリプション ID GUID によって識別され、必要に応じてそれぞれの説明を指定します。  これらのフィールドはどちらも顧客に表示されません。

追加できる Azure サブスクリプション ID は、手動の場合で最大 10 個、CSV ファイルをアップロードする場合で最大 100 個です。  これらのサブスクリプションを追加することで、完全に公開される前のオファーへのプレビュー アクセスを許可される対象ユーザーを定義します。  オファーが既に公開されている場合も、オファーの変更や更新をテストするためにプレビュー対象ユーザーを定義することができます。

>[!Note]
>プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 プレビュー対象ユーザーは、マーケットプレースで一般公開される "*前に*" オファーにアクセスすることを許可されます。 また、プランを作成し、プライベートな対象ユーザーだけがそれを使用できるようにすることもできます (プランの [利用可否] タブを使用)。  プレビュー対象ユーザーは、プランがマーケットプレースに完全に公開された後でプライベートな対象ユーザーだけが使用できるプランも含めて、すべてのプランを表示し、検証することができます。

## <a name="plan-overview"></a>プランの概要

**[プランの概要]** タブでは、同じオファーでさまざまなプラン オプションを提供することができます。 これらのプラン (Cloud パートナー ポータルでは SKU と呼ばれます) は、プランの種類 (ソリューション テンプレートとマネージド アプリケーション)、収益化、または対象ユーザーが異なっていてもかまいません。  オファーがマーケットプレースの一覧に表示されるためには、少なくとも 1 つのプランを構成します。

作成後、オファーの名前、ID、プランの種類、利用可否 (パブリックまたはプライベート)、現在の公開状態、使用可能なアクションが、このタブに表示されます。

**[プランの概要]** で使用可能な**アクション**は、プランの現在の状態によって異なり、以下を含む場合があります。

* プランの状態が **[ドラフト]** の場合 – ドラフトの削除。
* プランの状態が **[ライブ]** の場合 – プランの販売の停止、またはプライベート対象ユーザーの同期。

### <a name="create-new-plan"></a>新しいプランを作成する

***[プラン ID]*** - このオファーのプランごとに一意のプラン ID を作成します。 この ID は、製品の URL で顧客に表示されます。  小文字の英数字、ダッシュ、アンダースコアのみを使用してください。 このプラン ID は最大で 50 文字にできます。 作成を選択した後で、この ID を変更することはできません。

***[プラン名]*** - この名前は、顧客がオファー内のどのプランを選択するかを決めるときに表示されます。 このオファーのプランごとに一意のオファー名を作成します。 プラン名は、同じオファーに属するソフトウェア プランを区別するために使用されます (たとえば、 オファー名: Windows Server、プラン: Windows Server 2016 と Windows Server 2019)。

### <a name="plan-setup"></a>プランのセットアップ

**[プランのセットアップ]** タブでは、プランの種類に対する高レベル構成、別のプランのパッケージを再利用するかどうか、およびプランを使用可能にする必要のあるクラウドを設定できます。  このタブでの設定により、同じプランの他のタブに表示されるフィールドが変わります。

#### <a name="plan-type"></a>プランの種類

「[Azure アプリケーション プランの種類](#types-of-azure-application-plans)」で説明されているように、プランにソリューション テンプレートまたはマネージド アプリケーションのどちらを含めるかを選択します。

#### <a name="this-plan-reuses-packages"></a>このプランでパッケージを再利用する

同じ種類のプランが複数あり、それらの間でパッケージが同一である場合は、 **[this plan reuses packages from another plan]\(このプランで別のプランのパッケージを再利用する\)** を選択できます。  このオプションを選択すると、このオファーでパッケージを再利用するために、同じ種類の他のプランのいずれかを選択できるようになります。 

>[!Note]
>別のプランのパッケージを再利用すると、[技術的な構成] タブ全体がこのプランに対して表示されなくなります。  今後行う更新も含めて、他のプランの技術的な構成の詳細が、このプランにも使用されます。 <br> <br> また、この設定は、このプランを発行した後は変更できません。

#### <a name="cloud-availability"></a>クラウドでの利用可否

このプランは、少なくとも 1 つのクラウドで利用できる必要があります。 

マーケットプレースが統合されているすべてのパブリック Azure リージョンの顧客にソリューションをデプロイできるようにするには、 **[パブリック Azure]** オプションを選択します。  詳しくは、[利用可能な地域](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)に関する記事をご覧ください。

[Azure Government クラウド](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)にソリューションをデプロイできるようにするには、 **[Azure Government クラウド]** オプションを選択します。Azure Government クラウドは政府用クラウドであり、米国連邦政府、州、地域または部族の顧客、およびこれらの団体にサービスを提供する資格を持つパートナー向けにアクセスが規制されています。  発行元は、このクラウド コミュニティにサービスを提供するためのコンプライアンス管理、セキュリティ対策、ベスト プラクティスについて責任を持ちます。  Azure Government では、物理的に切り離されたデータ センターとネットワーク (米国のみに存在) が使用されます。  特定のエンドポイントが異なる可能性があるため、[Azure Government](https://aka.ms/azuregovpublish) に発行する前に、環境でソリューションをテストおよび検証することをお勧めします。 ソリューションをステージングしてテストするには、こちらの[リンク](https://azure.microsoft.com/global-infrastructure/government/request/)から試用版のアカウントを要求してください。

>[!Note]
>特定のクラウドで利用可能としてプランを発行した後で、そのクラウドを削除することはできません。

**Azure Government クラウドの認定**

このオプションは、 **[Cloud availability]\(クラウドの利用可否\)** で **[Azure Government クラウド]** を選択した場合にのみ表示されます。

Azure Government サービスでは、FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS など、特定の政府の規制および要件の対象となるデータが処理されます。  これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。  これらのリンクでは、プログラムでの一覧に直接リンクすることも、独自の Web サイトでのそれらについてのコンプライアンスに関する説明にリンクすることもできます。  これらのリンクは、Azure Government クラウドの顧客にのみ表示されます。

## <a name="plan-listing"></a>プランのリスト登録

**[プランのリスト]** タブには、プラン固有の一覧情報が表示されます。この情報は、同じオファーの異なるプラン間で異なる可能性があります。

### <a name="name"></a>名前

作成時にプランに割り当てた名前に基づいて事前設定されます。  この名前は、マーケットプレースに表示されるこの "ソフトウェア プラン" のタイトルとして表示されます。  最大で 100 文字まで使用できます。

### <a name="summary"></a>まとめ

ソフトウェア プランの簡単な概要を設定します。  最大で 100 文字まで使用できます。

### <a name="description"></a>説明

この説明では、このソフトウェア プランに固有な点や、オファー内の他のソフトウェア プランとの違いを説明できます。 最大で 2,000 文字まで使用できます。

これらのフィールドに入力した後、 **[保存]** を選択します。

## <a name="availability"></a>可用性

**[利用可否]** タブは、ソリューション テンプレート プランに対してのみ表示されます。  プランを、すべてのユーザーに表示するか、または特定の顧客 (プライベート対象ユーザー) のみに表示するかを指定できます。また、プランを他のソリューション テンプレートまたはマネージド アプリケーションでのみ使用されるように非表示にするかどうかを指定できます。

### <a name="plan-audience"></a>[Plan Audience]\(プランの対象ユーザー\)

各プランがすべてのユーザーに表示されるようにするのか、それとも自分が選択した特定の対象ユーザーだけに表示されるようにするのかを構成できます。 Azure サブスクリプション ID を使用して、この限定対象ユーザーにメンバーシップを割り当てることができます。

**[Privacy / This is a private plan]\(プライバシー/これはプライベート プランです\)** (省略可能なチェック ボックス) - プランをプライベートにして、自分が選択した限定対象ユーザーにのみ表示されるようにするには、このチェック ボックスをオンにします。 プライベート プランとして公開すると、対象ユーザーを更新したり、そのプランをすべてのユーザーが利用できる状態にしたりできます。 プランは、すべてのユーザーに表示されるものとして公開すると、すべてのユーザーに表示したままにしなければなりません (プランをもう一度プライベート プランとして構成することはできません)。

**[制限付きの対象ユーザー (Azure サブスクリプション ID)]** - このプライベート プランにアクセスできる対象ユーザーを割り当てます。 アクセス権は Azure サブスクリプション ID を使用して割り当てられます。必要に応じて、割り当て先の各 Azure サブスクリプション ID の説明を含めることができます。 最大 10 個のサブスクリプション ID を追加できます (.csv スプレッドシート ファイルをインポートする場合は、20,000 個の顧客サブスクリプション ID)。  Azure サブスクリプション ID は GUID として表されます。文字は小文字にする必要があります。

>[!Note]
>プライベート対象ユーザー (または限定対象ユーザー) は、 **[[プレビュー](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)]** タブで定義したプレビュー対象ユーザーとは異なります。プレビュー対象ユーザーは、お客様のオファーがマーケットプレースで一般公開される "*前に*" オファーにアクセスすることを許可されます。 プライベート対象ユーザーの指定は特定のプランにのみ適用されますが、プレビュー対象ユーザーは (プライベートであるかどうかに関係なく) 検証のためすべてのプランを表示できます。

### <a name="hide-plan"></a>プランの非表示

別のソリューション テンプレートまたはマネージド アプリケーションによって参照されることにより間接的にのみデプロイされることを意図されているソリューションテン プレートの場合、ソリューション テンプレートを発行しても、顧客が検索して直接参照できないようにするには、このチェック ボックスをオンにします。

## <a name="pricing-and-availability"></a>価格と可用性

**[価格と利用可否]** タブは、マネージド アプリケーション プランの場合にのみ表示されます。  このプランを利用可能にする市場、ソリューション管理の月単位の料金、およびプランをすべてのユーザーに対して表示するか、特定の顧客に対してのみ表示するか (プライベート ユーザー) を構成できます。

### <a name="markets"></a>市場

すべてのプランは、少なくとも 1 つの市場で利用できる必要があります。 このプランを利用可能にしたい市場の場所について、このチェック ボックスを選択します。 (Microsoft が公開元に代わって消費税と使用税を送金する) "税送金" 国を選択するための検索ボックスおよびボタンが、補助として含まれています。

米国ドル (USD) でプランの価格を既に設定していて、別の市場の場所を追加する場合、新しい市場の価格は現時点の為替レートに従って計算されます。 常に、各市場の価格を発行前に確認します。 価格は、変更の保存後に "価格のエクスポート (xlsx)" リンクを使用して確認できます。

### <a name="pricing"></a>価格

このプランの月ごとの価格を指定します。  このソリューションによってデプロイされるリソースによって発生する Azure インフラストラクチャまたは従量課金制ソフトウェアのコストに、この価格が追加されます。

現地通貨 (USD = 米国ドル) で設定された価格は、セットアップ中に使用可能な現時点の為替レートを使用して、選択されたすべての市場の現地通貨に変換されます。 これらの価格は公開前に検証してください。そのためには、価格スプレッドシートをエクスポートして各市場の価格を確認します。 個々の市場でカスタム価格を設定したい場合は、価格スプレッドシートを修正してインポートします。 

>[!Note]
>価格データのエクスポートを有効にするには、先に価格の変更を保存する必要があります。

プランの発行後に変更できる内容には一部制限があるため、価格は公開前に注意深く確認してください。  

>[!Note]
>プランの市場の価格は、公開されると後で変更することができません。

### <a name="plan-audience"></a>[Plan Audience]\(プランの対象ユーザー\)

各プランがすべてのユーザーに表示されるようにするのか、それとも自分が選択した特定の対象ユーザーだけに表示されるようにするのかを構成できます。 Azure サブスクリプション ID を使用して、この限定対象ユーザーにメンバーシップを割り当てることができます。

**[Privacy / This is a private plan]\(プライバシー/これはプライベート プランです\)** (省略可能なチェック ボックス) - プランをプライベートにして、自分が選択した限定対象ユーザーにのみ表示されるようにするには、このチェック ボックスをオンにします。 プライベート プランとして公開すると、対象ユーザーを更新したり、そのプランをすべてのユーザーが利用できる状態にしたりできます。 プランは、すべてのユーザーに表示されるものとして公開すると、すべてのユーザーに表示したままにしなければなりません (プランをもう一度プライベート プランとして構成することはできません)。

**[制限付きの対象ユーザー (Azure サブスクリプション ID)]** - このプライベート プランにアクセスできる対象ユーザーを割り当てます。 アクセス権は Azure サブスクリプション ID を使用して割り当てられます。必要に応じて、割り当て先の各 Azure サブスクリプション ID の説明を含めることができます。 最大 10 個のサブスクリプション ID を追加できます (.csv スプレッドシート ファイルをインポートする場合は、20,000 個の顧客サブスクリプション ID)。  Azure サブスクリプション ID は GUID として表されます。文字は小文字にする必要があります。

>[!Note]
>プライベート対象ユーザー (または限定対象ユーザー) は、 **[[プレビュー](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)]** タブで定義したプレビュー対象ユーザーとは異なります。プレビュー対象ユーザーは、お客様のオファーがマーケットプレースで一般公開される "*前に*" オファーにアクセスすることを許可されます。 プライベート対象ユーザーの指定は特定のプランにのみ適用されますが、プレビュー対象ユーザーは (プライベートであるかどうかに関係なく) 検証のためすべてのプランを表示できます。

## <a name="technical-configuration"></a>技術的な構成 

**[技術的な構成]** タブでは、顧客によるプランのデプロイを可能にするデプロイ パッケージをアップロードできます。

>[!Note]
>**[プランのセットアップ]** タブで別のプランからパッケージを再利用するようにこのプランを構成した場合、このタブは表示されません。

### <a name="package-details"></a>パッケージの詳細

**[パッケージの詳細]** サブタブでは、技術的な構成のドラフト バージョンを編集できます。

***[バージョン]*** - 技術的な構成の現在のバージョンを割り当てます。  このページに変更を発行するたびに、このバージョンをインクリメントします。 バージョンは `{integer}.{integer}.{integer}` の形式でなければなりません。

***[パッケージ ファイル (`.zip`)]*** - このパッケージには、このプランに必要なすべてのテンプレート ファイルとすべての追加リソースが、`.zip` ファイルとしてパッケージ化されて含まれます。

すべての Azure アプリケーション プラン パッケージの `.zip` アーカイブのルート フォルダーには、次の 2 つのファイルが含まれている必要があります。

* [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) という名前の Resource Manager テンプレート ファイル。  このテンプレートにより、顧客の Azure サブスクリプションへのリソースのデプロイが自動化されます。  Resource Manager テンプレートの例については、[Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/)または対応する [GitHub: Azure Resource Manager クイックスタート テンプレート](https://github.com/azure/azure-quickstart-templates) リポジトリをご覧ください。

* [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview) という名前の、Azure アプリケーション作成エクスペリエンス用のユーザー インターフェイス定義。

すべての新しい Azure アプリケーション オファーには、[Azure パートナーの顧客の使用状況の属性 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) も含まれている必要があります。

### <a name="previously-published-packages"></a>発行済みのパッケージ 

**[発行済みのパッケージ]** サブタブでは、技術的な構成のすべての発行済みバージョンを見ることができます。

## <a name="technical-configuration-managed-application-plans-only"></a>技術的な構成 (マネージド アプリケーション プランのみ)

マネージド アプリケーション プランの **[技術的な構成]** タブには、上で説明した **[バージョン]** および **[パッケージ ファイル]** フィールド以外に、複雑な構成があります。 

### <a name="enable-just-in-time-jit-access"></a>Just-In-Time (JIT) アクセスの有効化

このプランに対して Just-In-Time (JIT) アクセスを有効にするには、このオプションを選択します。  JIT アクセスによって、発行元は、トラブルシューティングやメンテナンスを行うために、マネージド アプリケーションのリソースに対して昇格されたアクセス権を要求することができます。 発行元は、リソースへの読み取り専用アクセス権を常に所有していますが、特定の期間にさらに大きなアクセス権を持つことができます。  詳しくは、「[Azure Managed Applications でジャストインタイム アクセスを有効にして要求する](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)」をご覧ください。  マネージド アプリケーションの顧客に対し、発行元のアカウントに永続アクセス権を付与するよう要求するには、このオプションをオフのままにします。

>[!Note]
>この機能をサポートするには、`createUiDefinition.json` ファイルを更新する必要があります。  

### <a name="deployment-mode"></a>展開モード

このプランをデプロイするときに、**完全デプロイ モード**または**増分デプロイ モード**を構成するかどうかを選択します。 

* **完全モード**では、顧客がアプリケーションを再デプロイすると、`mainTemplate.json` で定義されていない管理対象リソース グループのリソースは削除されます。 
* **増分モード**では、アプリケーションを再デプロイしても、既存のリソースは変更されずに残ります。

デプロイ モードの詳細については、「[Azure Resource Manager のデプロイ モード](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)」をご覧ください。

### <a name="notification-endpoint-url"></a>通知エンドポイント URL

このプラン バージョンのマネージド アプリケーション インスタンスでのすべての CRUD 操作に関する通知を受け取るための、HTTPS Webhook エンドポイントを指定します。

### <a name="customize-allowed-customer-actions"></a>許可されている顧客アクションのカスタマイズ

既定で使用できる "`*/read`" アクションに加えて、顧客が管理対象リソースに対して実行できるアクションを指定するには、このオプションを選択します。 

ここでは、顧客が実行できるようにする追加のアクションを、セミコロンで区切って列挙します。  詳しくは、「[Azure リソースの拒否割り当ての概要](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)」をご覧ください。  使用できるアクションについては、「[Azure Resource Manager のリソース プロバイダー操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)」を参照してください。 たとえば、仮想マシンの再起動をコンシューマーに許可するには、許可されているアクションに `Microsoft.Compute/virtualMachines/restart/action` を追加します。

### <a name="global-azure--azure-government-cloud"></a>グローバル Azure/Azure Government クラウド

サポートされている各クラウドで、このマネージド アプリケーションに対する管理アクセス権を持つユーザーを指定します。  管理対象リソース グループに対するアクセス許可を付与するユーザー、グループ、またはアプリケーションは、Azure Active Directory (AAD) の ID を使用して識別されます。

***[Azure Active Directory テナント ID]*** - アクセス許可を付与するユーザー、グループ、またはアプリケーションの ID が含まれる AAD テナントの ID (ディレクトリ ID とも呼ばれます)。  AAD テナント ID は、Azure portal の [Azure Active Directory のプロパティ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)で確認できます。

***[承認]*** - 管理対象リソース グループへのアクセス許可を付与するユーザー、グループ、アプリケーションの Azure Active Directory のオブジェクト ID を追加します。 プリンシパル ID でユーザーを識別します。プリンシパル ID は、[Azure portal の Azure Active Directory ユーザー ブレード](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)にあります。

プリンシパルごとに、一覧から Azure AD 組み込みロールのいずれかを選択します (所有者または共同作成者)。 選択したロールによって、顧客のサブスクリプションのリソースに対してプリンシパルが持つアクセス許可が記述されます。 詳細については、「[Azure リソースの組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)」を参照してください。  ロールベースのアクセス制御 (RBAC) について詳しくは、[Azure portal での RBAC の概要](https://docs.microsoft.com/azure/role-based-access-control/overview)に関する記事をご覧ください。

>[!Note]
>クラウドあたり最大 100 個の承認を追加できますが、一般に、Active Directory のユーザー グループを作成し、その ID を "プリンシパル ID" で指定する方が簡単です。  これにより、プランをデプロイした後で管理グループにより多くのユーザーを追加でき、承認を追加するためだけにプランを更新する必要がなくなります。

### <a name="policy-settings"></a>ポリシー設定

マネージド アプリケーションに [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) を適用して、デプロイしたソリューションのコンプライアンス要件を指定します。  ポリシーの定義とパラメーター値の形式については、「[Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/index)」を参照してください。  最大 5 つのポリシーと、各ポリシー オプションのただ 1 つのインスタンスを構成できます。  一部のポリシーでは、追加のパラメーターが必要です。  監査ポリシーには Standard SKU が必要です。  ポリシーの名前は 50 文字に制限されています。

## <a name="co-sell"></a>共同販売

[共同販売] タブでの情報提供は、オファーの発行に対してすべて省略可能です。 共同販売の準備完了と IP 共同販売の準備完了の状態になっている必要があります。 指定した情報は、お客様のニーズに合わせてソリューションを評価するときに、Microsoft のセールス チームによって使用されます。 お客様が直接使用することはできません。

このタブの設定について詳しくは、「[パートナー センターの [共同販売] オプション](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)」をご覧ください。

## <a name="test-drive"></a>体験版

**[体験版]** タブでは、デモ (つまり "体験版") を設定して、顧客が購入をコミットする前にオファーを試せるようにすることができます。 詳細については、記事「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください  オファーの体験版を提供する必要がなくなった場合は、 **[オファーのセットアップ]** ページに戻って、 **[体験版を有効にする]** をオフにします。

### <a name="technical-configuration"></a>技術的な構成

Azure アプリケーションでは、本質的に Azure Resource Manager の体験版の種類が使用されます。  詳しくは、「[Azure Resource Manager の体験版の技術的な構成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)」をご覧ください。

### <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

体験版を自動的にデプロイするには、固有の Azure サブスクリプションを別個に作成し、指定してください (Power BI の体験版では不要です)。

- **Azure サブスクリプション ID** (Azure Resource Manager とロジック アプリでは必須): リソースの使用状況レポート用および課金用の Azure アカウント サービスへのアクセス権を付与するサブスクリプション ID を入力します。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプションの作成](https://docs.microsoft.com/azure/billing/billing-create-subscription)を検討することをお勧めします。 Azure サブスクリプション ID は、[Azure portal](https://portal.azure.com/) にログインし、左側にあるメニューの **[サブスクリプション]** タブに移動して見つけることができます。 このタブを選択すると、自分のサブスクリプション ID (例: "a83645ac-1234-5ab6-6789-1h234g764ghty") が表示されます。

- **Azure AD テナント ID** (必須): 自分の Azure Active Directory (AD) [テナント ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択し、[プロパティ] を選択してから、表示される**ディレクトリ ID** 番号 (例: 50c464d3-4930-494c-963c-1e951d15360e) を探します。 また、[https://www.whatismytenantid.com](https://www.whatismytenantid.com) で URL のドメイン名を使用して、組織のテナント ID を検索することもできます。

- **Azure AD tenant name (Azure AD テナント名)** (Dynamic 365 で必須): 自分の Azure Active Directory (AD) 名を入力します。 この名前を見つけるには、[Azure portal](https://portal.azure.com/) にサインインします。右上隅にある自分のアカウント名の下に、テナント名が表示されます。

- **Azure AD app ID (Azure AD アプリ ID)** (必須): 自分の Azure Active Directory (AD) [アプリケーション ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択し、 **[アプリの登録]** を選択してから、表示される**アプリケーション ID** 番号 (例: 50c464d3-4930-494c-963c-1e951d15360e) を探します。

- **Azure AD アプリ クライアントのシークレット** (必須): Azure AD アプリケーションの[クライアント シークレット](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)を入力します。 この値を探すには、[Azure portal](https://portal.azure.com/) にサインインします。 左側のメニューにある **[Azure Active Directory]** タブを選択し、 **[アプリの登録]** を選択してから、体験版アプリを選択します。 次に、 **[Certificates and secrets]\(証明書とシークレット\)** 、 **[New client secret]\(新しいクライアント シークレット\)** の順に選択し、説明を入力し、 **[Expires]\(有効期限\)** で **[Never]\(なし\)** を選択してから、 **[追加]** を選択します。 必ず値をコピーしておいてください。 (値をコピーする前にページから移動しないでください。移動すると、値にアクセスできなくなります。)

次のセクションに進む前に、必ず**保存**してください。

### <a name="test-drive-listings-optional"></a>体験版のリスト登録 (省略可能)

**[体験版]** タブの下にある **[Test Drive listings]\(体験版のリスト登録\)** オプションには、体験版が利用可能な言語 (と市場) が表示されます。現在、利用可能な唯一の場所は英語 (米国) です。 さらに、このページには、言語固有のリスト登録の状態と、それが追加された日付および時刻が表示されます。 言語と市場ごとに体験版の詳細 (説明、ユーザー マニュアル、ビデオなど) を定義する必要があります。

- **説明** (必須): ユーザーがお客様のオファーを購入するかどうかを決定する助けとなるように、体験版、デモの内容、試用するユーザーの目的、試せる機能、関連情報について説明します。 このフィールドには、最大で 3,000 文字のテキストを入力できます。 

- **[アクセス情報]** (Azure Resource Manager とロジックの体験版では必須): 顧客がこの体験版にアクセスして使用するうえで知っておく必要がある情報を説明します。 お客様のオファーの使用シナリオのほか、顧客が体験版を通じて機能にアクセスするために知っておくべき正確な情報を説明します。 このフィールドには、最大で 10,000 文字のテキストを入力できます。

- **ユーザー マニュアル** (必須): 体験版エクスペリエンスの詳細なチュートリアル。 ユーザー マニュアルは、体験版のエクスペリエンスで顧客が使用できる内容を正確にカバーし、疑問がある場合に参照するものとして機能する必要があります。 ファイルは PDF 形式にして、アップロード後に名前 (最大 255 文字) を付ける必要があります。

- **ビデオ: ビデオの追加** (省略可能): ビデオは YouTube または Vimeo にアップロードすることができ、リンクとサムネイル画像 (533 x 324 ピクセル) を使用してここで参照できます。これにより顧客は、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、体験版への理解を深めるのに役立つ一連の情報を確認できます。
  - **名前** (必須)
  - **URL (YouTube または Vimeo のみ)** (必須)
  - **サムネイル (533 x 324 ピクセル)** : 画像ファイルは PNG 形式である必要があります。

これらのフィールドに入力した後、 **[保存]** を選択します。

## <a name="publish"></a>発行

### <a name="submit-offer-to-preview"></a>プレビューへのオファーの送信

オファーの必須セクションをすべて入力したら、ポータルの右上隅にある **[公開]** を選択します。 **[Review and publish]\(確認と公開\)** ページにリダイレクトされます。 

このオファーを発行するのが初めての場合、以下のことが可能です。

- オファーの各セクションの完了状態を確認する。
    - *[未開始]* - セクションは着手されておらず、完了する必要があることを意味します。
    - *[未完了]* - 修正が必要なエラーがセクションにあり、追加の情報を入力する必要があることを意味します。 セクションに戻って更新してください。
    - *[完了]* - セクションが完了していることを意味します。必須のデータはすべて入力済みであり、エラーはありません。 オファーを送信するには、オファーのセクションがすべて完了状態でなければなりません。
- アプリの理解に役立つ補足事項に加えて、テストの指示を認定チームに提供し、アプリが正しくテストされるようにする。
- **[送信]** を選択して、公開するためにオファーを送信する。 お客様が確認して承認できるようにオファーのプレビュー バージョンが利用可能になったら、それを知らせるメールが Microsoft から届きます。 パートナー センターに戻り、そのオファーに対して **[一般公開する]** を選択して、自分のオファーを一般に公開します (プライベート オファーの場合はプライベート対象ユーザーに公開されます)。

### <a name="errors-and-review-feedback"></a>エラーとフィードバックの確認

発行プロセスの**手動検証**ステップには、オファーの広範なレビューとそれに関連する技術資産 (特に Azure Resource Manager テンプレートの場合) が含まれるため、通常、問題は pull request (PR) のリンクとして示されます。 これらの PR を表示してそれに対応する方法については、「[レビュー フィードバックの処理](./azure-apps-review-feedback.md)」をご覧ください。

発行手順のどこかでエラーが発生した場合は、それらを修正し、オファーを再発行する必要があります。

## <a name="next-steps"></a>次のステップ

- [商業マーケットプレースで既存のオファーを更新する](./update-existing-offer.md)
