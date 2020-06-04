---
title: Dynamics 365 Business Central オファーを作成する - Microsoft コマーシャル マーケットプレース
description: パートナー センターのコマーシャル マーケットプレース ポータルで、新しい Dynamics 365 Business Central オファーを作成するための手順と考慮事項について説明します。 Azure Marketplace で、またはクラウド ソリューション プロバイダー (CSP) プログラムを使用して、オファーを一覧表示したり、販売したりすることができます。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 04de89624dd0e6857e96327bb408cf8700a1f6a2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848883"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Dynamics 365 Business Central オファーの作成

この記事では、新しい Dynamics 365 Business Central オファーの作成方法について説明します。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) はエンタープライズ リソース プランニング (ERP) システムであり、財務、業務、サプライ チェーン、CRM、プロジェクト管理と電子商取引など、さまざまなビジネス プロセスを処理します。 Premium パッケージでは、クラシック デプロイ モデルと製造もサポートされています。 Dynamics 365 Business Central のすべてのオファーは、Microsoft の認定プロセスを通過する必要があります。

開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)していなければ、作成します。 お使いのアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Dynamics 365 Business Central]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-dynamics-365-bc.png)

> [!NOTE]
> オファーを公開した後にパートナー センターで編集した内容は、オファーの再公開後にのみネットショップに表示されます。 変更後は必ず再公開してください。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、ご自分のアカウント内の各オファーに対する一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスと Azure Resource Manager テンプレート (該当する場合) で顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後で、オファーの別名を変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-setup"></a>オファーのセットアップ

オファーを設定するには、次の手順に従います。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>潜在顧客がこの登録オファーとやり取りする方法について選択してください。

このオファーに使用するオプションを選択します。

#### <a name="get-it-now-free"></a>Get it now (今すぐ入手する) (無料)

顧客に対して、プランを無料で使用できるものとしてリスト登録します。そのために、アプリにアクセスできる有効な (*http* または *https* で始まる) URL を指定します。  たとえば、「 `https://contoso.com/my-app` 」のように入力します。

#### <a name="free-trial-listing"></a>Free trial (無料試用版) (一覧)

顧客が試用版を入手できる有効な (*http* または *https* で始まる) URL を示すことにより、無料試用版へのリンクを使用して顧客にオファーを一覧表示します。  たとえば、「 `https://contoso.com/trial/my-app` 」のように入力します。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。

> [!NOTE]
> 試用版リンクからアプリケーションが受信するトークンは、そのアプリのアカウント作成を自動化するためのユーザー情報を Azure Active Directory (Azure AD) を介して取得するためだけに使用できます。 このトークンを使用した認証には、Microsoft アカウントはサポートされません。

#### <a name="contact-me"></a>[Contact me (お問い合わせ)]

顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、「[潜在顧客](#customer-leads)」を参照してください。

### <a name="test-drive"></a>体験版

体験版は、購入前に試用するオプションを提供することで潜在顧客へのオファーを披露し、その結果、コンバージョンが増加し、見込みの高いリードが生成される優れた方法です。 [体験版の詳細を確認してください](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

一定期間、体験版を有効にするには、 **[体験版を有効にする]** チェック ボックスをオンにします。 オファーから体験版を削除するには、このチェック ボックスをオフにします。 このトピックの後の方の「[体験版の技術的な構成](#test-drive-technical-configuration)」セクションで、体験版環境を構成します。

詳細については、[商業マーケットプレースでのオファーの体験版](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)に関するページを参照してください。

#### <a name="type-of-test-drive"></a>体験版の種類

次のオプションから選択します。

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – ご自身のソリューションを構成するすべての Azure リソースが含まれたデプロイ テンプレート。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft が Business Central エンタープライズ リソース プランニング システム (財務、運用、サプライ チェーン、CRM など) 用の体験版サービスをホストおよび維持します。これには、プロビジョニングとデプロイも含まれます。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft が Customer Engagement システム (販売、サービス、プロジェクト サービス、フィールド サービスなど) 用の体験版サービスをホストおよび維持します。これには、プロビジョニングとデプロイも含まれます。  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft が Finance and Operations エンタープライズ リソース プランニング システム (財務、運用、製造、サプライ チェーンなど) 用の体験版サービスをホストおよび維持します。これには、プロビジョニングとデプロイも含まれます。 
- **[ロジック アプリ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – あらゆる複雑なソリューション アーキテクチャに対応するデプロイ テンプレート。 すべてのカスタム製品には、この種類の体験版を使用する必要があります。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – カスタムビルドされたダッシュボードへの埋め込みリンク。 インタラクティブな Power BI の視覚化のデモンストレーションを行う製品には、この種類の体験版を使用する必要があります。 ここで必要なことは、埋め込み Power BI の URL をアップロードすることだけです。

#### <a name="additional-test-drive-resources"></a>体験版に関するその他のリソース

- [体験版の技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体験版のマーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [体験版の概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ポップアップ ブロックがオフになっていることを確認してください)

### <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

詳しくは、[リード管理の概要](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="properties"></a>Properties

このページでは、マーケットプレース上でのオファーのグループ分けに使用するカテゴリと業界、アプリのバージョン、オファーがサポートされる法的契約を定義できます。

### <a name="category"></a>カテゴリ

オファーを適切なマーケットプレース検索区分にグループ分けするために使用されるカテゴリを、最小で 1 つ、最大で 3 つ選択します。 これらのカテゴリにオファーがどのように対応しているかを、オファーの説明に含めるようにします。 

### <a name="industry"></a>業界

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>アプリのバージョン

オファーのバージョン番号を入力します。 顧客にはオファーの詳細ページにこのバージョンが表示されます。

### <a name="terms-and-conditions"></a>使用条件

**[使用条件]** フィールドに、独自の法的な使用条件を入力します。 使用条件を見つけることができる URL を指定することもできます。 顧客は、オファーを試す前にこれらの条件を承諾する必要があります。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、オファーの詳細 (名前、説明、画像など) を定義します。

> [!NOTE]
> オファー登録情報の詳細を入力する言語は 1 つに限られています。 オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、英語にする必要はありません。 また、オファー登録情報のコンテンツで使用されている言語以外の言語でコンテンツを提供するための "*ヘルプ リンクの URL*" を提供することもできます。

### <a name="name"></a>名前

ここで入力する名前は、オファー登録情報のタイトルとして顧客に表示されます。 このフィールドには、オファーの作成時に **[オファーの別名]** に入力したテキストが事前に設定されていますが、この値は変更できます。 この名前は商標の場合もあります (商標または著作権マークを含めることもできます)。 名前は 50 文字以下にする必要があります。絵文字を含めることはできません。

### <a name="short-description"></a>簡単な説明

オファーの簡単な説明を最大 100 文字で入力します。 この説明は、マーケットプレースの検索結果で使用される場合があります。

### <a name="description"></a>説明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>キーワード検索

オファーを顧客がマーケットプレースで検索できるように、必要に応じて最大 3 つの検索キーワードを入力することができます。 最良の結果を得るには、これらのキーワードを説明にも使用するようにします。

### <a name="products-your-app-works-with"></a>アプリが対応する製品

アプリが特定の製品で動作することを顧客に知らせる場合は、ここに製品名を 3 つまで入力します。

### <a name="helpprivacy-urls"></a>ヘルプまたはプライバシー URL

このセクションには、顧客がオファーの詳細を理解するのに役立つリンクを入力できます。

#### <a name="help-link"></a>ヘルプ リンク

顧客がオファーの詳細について確認できる URL を入力します。 **ヘルプ リンク**は**サポート URL** と同じにすることはできません (以下で説明します)。

#### <a name="privacy-policy-link"></a>プライバシー ポリシーのリンク

自分の組織のプライバシー ポリシーへの URL を入力します。 プライバシーに関する法律および規制にアプリが準拠していることを保証し、有効なプライバシー ポリシーを提供する責任があります。

### <a name="contact-information"></a>連絡先情報

このセクションでは、**サポートの連絡先**と**エンジニアリングの連絡先**の名前、メール、電話番号を入力する必要があります。 この情報は顧客には表示されませんが、Microsoft で利用できるようになり、CSP パートナーに提供される場合もあります。

**[サポートの連絡先]** セクションでは、CSP パートナーがオファーのサポートを確認できる**サポート URL** も指定する必要があります。 サポート URL を**ヘルプ リンク**と同じにすることはできません。

### <a name="supporting-documents"></a>サポート ドキュメント

ここには、関連するマーケティング ドキュメント (ホワイト ペーパー、パンフレット、チェックリスト、プレゼンテーションなど) を 1 つ以上 (3 つまで) 入力します。 これらのドキュメントは、.pdf 形式である必要があります。

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーのロゴと画像を提供します。 画像はすべて PNG 形式である必要があります。 オファーのロゴを 2 つのサイズでアップロードします。

* **小** (48 x 48 ピクセル)
* **大** (216 x 216 ピクセル)

>[!NOTE]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている `https://upload.xboxlive.com` サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="screenshots"></a>Screenshots (スクリーンショット)

オファーがどのように動作するかを示すスクリーンショットを追加します。 少なくとも 3 つのスクリーンショットが必要です。最大 5 つまで追加できます。 スクリーンショットはすべて、1280 x 720 ピクセルにする必要があります。

#### <a name="videos"></a>ビデオ

必要に応じて、オファーをデモンストレーションするビデオを最大 5 つ追加することもできます。 これらのビデオは、YouTube または Vimeo でホストされている必要があります。 それぞれのビデオについて、ビデオの名前、URL、ビデオのサムネイル画像 (1280 x 720 ピクセル) を入力します

#### <a name="additional-marketplace-listing-resources"></a>マーケットプレースのリスト登録に関するその他のリソース

[マーケットプレース オファーのリスト登録に関するベスト プラクティス](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

続行する前に、 **[下書きの保存]** を選択します。

## <a name="availability"></a>可用性

このページには、オファーを利用できる場所と方法に関するオプションが表示されます。

### <a name="markets"></a>市場

このセクションでは、オファーを利用できる市場を指定します。 これを行うには、 **[市場の編集]** を選択します。 **[市場の選択]** ポップアップ ウィンドウが表示されます。

オファーを発行するには、少なくとも 1 つの市場を選択してください。 **[すべて選択]** を選択して、利用可能なすべての市場でオファーを利用できるようにするか、追加する特定の市場を選択します。

ここで選択した内容は、新規購入にのみ適用されます。ユーザーが既に特定の市場でアプリを入手しているときに、後でその市場が削除された場合、その市場で既にオファーを入手したユーザーは引き続きそれを使用できますが、その市場で新しいユーザーがオファーを入手することはできません。

> [!IMPORTANT]
> ここやパートナー センターにこれらの要件が記載されていない場合でも、あらゆる地域の法的要件を満たす必要があります。

すべての市場を選択した場合でも、国や地域によっては、地域の法律や規制、その他の要因により、特定のオファーが一覧表示されない場合があることに注意してください。

### <a name="preview-audience"></a>プレビュー対象ユーザー

オファーをより広範なマーケットプレース オファーに公開する前に、まず、限定された**プレビュー対象ユーザー**に対してオファーを利用できるようにする必要があります。 ここでは、**非表示キー** (小文字と数字のみを使用する任意の文字列) を入力します。 プレビュー対象ユーザーのメンバーは、この非表示キーをトークンとして使用して、マーケットプレースでオファーのプレビューを表示できます。

オファーを利用可能にしてプレビューの制限を解除する準備ができたら、**非表示キー**を削除して再度発行する必要があります。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="technical-configuration"></a>技術的な構成

このページでは、オファーに接続するために使用される技術的な詳細を定義します。 この接続によって、最終顧客がオファーを取得することを選択した場合、Microsoft は最終顧客向けにオファーをプロビジョニングできます。

### <a name="package-type"></a>パッケージの種類

オファーに適用するオプションを選択します。

* **アドオン** – アドオン アプリは、Dynamics 365 Business Central のエクスペリエンスと既存の機能を拡張します。 詳細については、「[アドオン アプリ](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)」を参照してください。
* **接続** – 接続アプリは、Dynamics 365 Business Central とサードパーティのソリューションまたはサービスとの間にポイントツーポイント接続を確立する必要があるシナリオで使用できます。 詳細については、「[接続アプリ](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)」を参照してください。

### <a name="file-upload"></a>ファイルのアップロード

上記の **[アドオン]** を選択した場合、オファーのパッケージ ファイルと、依存関係があるすべての拡張機能のパッケージ ファイルをアップロードする場所。

#### <a name="extensions-package-file"></a>拡張機能パッケージ ファイル

オファーの拡張機能パッケージ ファイル (.app) をアップロードします。

#### <a name="library-package-file"></a>ライブラリ パッケージ ファイル

マーケットプレースに公開されない別の拡張機能と共にオファーをインストールする必要がある場合に必要です。 その場合は、その .app ファイルをここにアップロードします。

#### <a name="dependency-package-file"></a>依存関係パッケージ ファイル

マーケットプレースに既に公開されている別の拡張機能と共にオファーをインストールする必要がある場合に必要です。 その場合は、その `.app` または `.zip` ファイルをここにアップロードします。

### <a name="url-to-app-installation"></a>アプリのインストールの URL

上記の **[接続]** を選択した場合、ここでアプリのインストール用のアドレスを入力します。 インストールを必要としない接続済みサービスの場合は、サービスのランディング ページまたはサインアップ ページのアドレスを指定します。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="test-drive-technical-configuration"></a>体験版の技術的な構成

このページでは、購入前に顧客がオファーを試すことができるデモ (体験版) を設定します。 詳細については、記事「[体験版とは](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)」を参照してください。

体験版を有効にするには、[[オファーのセットアップ]](#test-drive) タブで **[体験版を有効にする]** チェック ボックスをオンにします。オファーから体験版を削除するには、このチェック ボックスをオフにします。

利用可能な体験版の種類は次のとおりで、それぞれに独自の技術的な構成要件があります。

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [ロジック アプリ](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (技術的な構成は不要)

体験版に関するその他のリソース:

- [マーケティングのベスト プラクティス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技術的なベスト プラクティス](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概要](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF、ポップアップ ブロックがオフになっていることを確認してください)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager の体験版の技術的な構成

お客様のソリューションを構成しているすべての Azure リソースが含まれたデプロイ テンプレート。 このシナリオに適合するのは、Azure リソースしか使用されていない製品です。 [Azure Resource Manager の体験版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)の設定について詳しくご確認ください。

- **リージョン** (必須) – 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 通常、顧客が最も近いリージョンを選択して最高のパフォーマンスを実現できるように、顧客の数が最も多いと予測されるリージョンで体験版を利用可能にする必要があります。 選択中の各リージョンで必要なすべてのリソースのデプロイが自分のサブスクリプションで許可されていることを確認する必要があります。

- **インスタンス** – 使用可能なインスタンスの種類 (ホットまたはコールド) と数を選択します。この数に、ご自身のオファーが利用可能なリージョンの数が乗算されます。

    **[ホット]** – この種類のインスタンスは選択したリージョンごとにデプロイされ、アクセスを待ちます。 顧客は、デプロイを待つことなく体験版の "*ホット*" インスタンスにすぐにアクセスできます。 トレードオフとして、これらのインスタンスは Azure サブスクリプションで常に実行しているので、大きな稼働時間コストが発生します。 使用できる "*ホット*" インスタンスがない場合、ほとんどの顧客がフル デプロイを待つのを望まず、その結果顧客の利用が減るので、少なくとも 1 つの "*ホット*" インスタンスを用意することを強くお勧めします。

    **コールド** – この種類のインスタンスは、リージョンごとにデプロイできる可能性があるインスタンスの総数を表します。 コールド インスタンスでは、顧客が体験版を要求したときのデプロイ用に体験版の完全な Resource Manager テンプレートが必要です。そのため、"*コールド*" インスタンスでは、読み込みが "*ホット*" インスタンスよりもはるかに遅くなります。 トレードオフとして、体験版の期間のみの支払いで済みます。"*ホット*" インスタンスのように Azure サブスクリプションで常に実行されていることは "*ありません*"。

- **体験版の Azure Resource Manager テンプレート** – 目的の Azure Resource Manager テンプレートが含まれている .zip をアップロードします。  Azure Resource Manager テンプレートの作成については、記事「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)」を参照してください。

- **体験版の期間** (必須) – 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。 整数のみを使用します (たとえば、"2" 時間は有効ですが、"1.5" は無効です)。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 の体験版の技術的な構成

Microsoft は、この種類の体験版を使用してサービスのプロビジョニングとデプロイをホストおよび維持することで、体験版の設定の複雑さを排除できます。 この種類のホストされた体験版では、体験版がターゲットとしている対象ユーザーが Business Central、Customer Engagement、Operations のいずれであっても、構成は同じです。

- **同時実行体験版の最大数** (必須) – 体験版を同時に使用できる顧客の最大数を設定します。 体験版がアクティブになっている間、各同時ユーザーは Dynamics 365 のライセンスを消費します。そのため、設定された上限に対応するうえで十分な数のライセンスを使用可能にする必要があります。 推奨値は 3 ～ 5 です。

- **体験版の期間** (必須) – 時間数を定義して、体験版がアクティブな状態であり続ける期間の長さを入力します。 この時間が経過すると、セッションが終了し、ライセンスが消費されなくなります。 オファーの複雑さに応じて、2 時間から 24 時間までの値にすることをお勧めします。 この期間は、整数の時間でのみ設定できます (例: "2" 時間。"1.5" は有効ではありません)。  ユーザーは、時間を使い切った後に体験版にもう一度アクセスしたい場合、新しいセッションを要求できます。

- **インスタンス URL** (必須) – 顧客が体験版を開始する URL。 通常、インストールされたサンプル データを使用してアプリを実行する Dynamics 365 インスタンスの URL です (例: `https://testdrive.crm.dynamics.com`)。

- **インスタンスの Web API URL** (必須) – お使いの Microsoft 365 アカウントにログインし、 **[設定]** \&gt; **[カスタマイズ]** \&gt; **[開発者リソース]** \&gt; **[インスタンスの Web API] ([サービスのルート URL])** の順に移動することで、お使いの Dynamics 365 インスタンスの Web API URL を取得し、その URL (例: `https://testdrive.crm.dynamics.com/api/data/v9.0`) をここにコピーします。

- **ロール名** (必須) – カスタム Dynamics 365 体験版に定義したセキュリティ ロール名を指定します。体験版の間、これがユーザーに割り当てられます。(例: test-drive-role)。

### <a name="technical-configuration-for-logic-app-test-drive"></a>ロジック アプリの体験版の技術的な構成

すべてのカスタム製品には、この種類の体験版デプロイ テンプレートを使用する必要があります。これは、さまざまで複雑なソリューション アーキテクチャに対応します。 ロジック アプリの体験版の設定については、GitHub 上の [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) と [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) に関するページをご覧ください。

- **リージョン** (必須、単一選択のドロップダウン リスト) – 現在、体験版を利用可能にできるサポート対象の Azure リージョンは 26 か所です。 お客様のロジック アプリのリソースは、自分が選択するリージョンにデプロイされます。 お客様のロジック アプリに、特定のリージョンに格納されるカスタム リソースがある場合は、ここで必ずそのリージョンを選択します。 最善の方法は、ポータルで自分の Azure サブスクリプションにローカルにロジック アプリを完全にデプロイし、それが正常に動作するのを確認したうえでこの選択を行うことです。

- **同時実行体験版の最大数** (必須) – 体験版を同時に使用できる顧客の最大数を設定します。 これらの体験版は既にデプロイされており、顧客はデプロイを待つことなくそれらにすぐにアクセスできます。

- **体験版の期間** (必須) – 体験版がアクティブな状態であり続ける期間の長さを時間数で入力します。 この期間が終わると、体験版は自動的に終了します。

- **Azure リソース グループ名** (必須) – ロジック アプリの体験版が保存される [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名を入力します。

- **Azure logic app name (Azure ロジック アプリ名)** (必須) – 体験版をユーザーに割り当てるロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

- **ロジック アプリ名のプロビジョニング解除** (必須) – 顧客の完了後に体験版をプロビジョニング解除するロジック アプリの名前を入力します。 このロジック アプリは、上記の Azure リソース グループに保存される必要があります。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI の体験版では技術的な構成は不要

インタラクティブな Power BI の視覚化を示したい製品では、埋め込みリンクを使用して、カスタムビルドされたダッシュボードを体験版として共有できます。それ以上の技術的な構成は不要です。 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) テンプレート アプリの設定について詳しくご確認ください。

### <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

体験版を自動的にデプロイするには、固有の Azure サブスクリプションを別に作成し、それを指定してください (Power BI の体験版では不要です)。

- **Azure サブスクリプション ID** (Azure Resource Manager およびロジック アプリでは必須) – リソースの使用状況レポート用および課金用の Azure アカウント サービスへのアクセス権を付与するサブスクリプション ID を入力します。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプションの作成](https://docs.microsoft.com/azure/billing/billing-create-subscription)を検討することをお勧めします。 Azure サブスクリプション ID は、[Azure portal](https://portal.azure.com/) にログインし、左側にあるメニューの **[サブスクリプション]** タブに移動して見つけることができます。 このタブを選択すると、自分のサブスクリプション ID (例: "a83645ac-1234-5ab6-6789-1h234g764ghty") が表示されます。

- **Azure AD テナント ID** (必須) – お使いの Azure Active Directory (AD) [テナント ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインし、左側のメニューで [Active Directory] タブを選択します。**[プロパティ] を選択した後、表示される**ディレクトリ ID** 番号を確認します (例: 50c464d3-4930-494c-963c-1e951d15360e)。 また、[https://www.whatismytenantid.com](https://www.whatismytenantid.com) でドメイン名アドレスを使用して、組織のテナント ID を検索することもできます。

- **Azure AD テナント名** (Dynamic 365 では必須) – お使いの Azure Active Directory (AD) 名を入力します。 この名前を見つけるには、[Azure portal](https://portal.azure.com/) にサインインします。右上隅にある自分のアカウント名の下に、テナント名が表示されます。

- **Azure AD アプリ ID** (必須) – お使いの Azure Active Directory (AD) [アプリケーション ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) を入力します。 この ID を見つけるには、[Azure portal](https://portal.azure.com/) にサインインして左側のメニューで [Active Directory] タブを選択します。 **[アプリの登録]** を選択した後、表示される**アプリケーション ID** 番号を確認します (例: 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD アプリ クライアントのシークレット** (必須) – Azure AD アプリケーションの[クライアント シークレット](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)を入力します。 この値を探すには、[Azure portal](https://portal.azure.com/) にサインインします。 左側のメニューにある **[Azure Active Directory]** タブを選択し、 **[アプリの登録]** を選択してから、体験版アプリを選択します。 次に、 **[Certificates and secrets]\(証明書とシークレット\)** 、 **[New client secret]\(新しいクライアント シークレット\)** の順に選択し、説明を入力し、 **[Expires]\(有効期限\)** で **[Never]\(なし\)** を選択してから、 **[追加]** を選択します。 必ず値をコピーしておいてください。 (これを行う前にページから移動しないでください。移動すると、値にアクセスできなくなります。)

### <a name="test-drive-marketplace-listings"></a>体験版のマーケットプレースの登録情報

**[体験版]** タブの下にある **[マーケットプレースの登録情報]** オプションは、体験版エクスペリエンスの詳細を定義する場所です。

> [!NOTE]
> テスト ドライブの登録情報の詳細を入力する言語は 1 つに限られています。 オファーの説明が「このアプリケーションは、[英語以外の言語] でのみ利用可能です」という文言で始まっている場合、英語にする必要はありません。 また、テスト ドライブの登録情報コンテンツで使用されている言語以外の言語でコンテンツを提供するための "*ヘルプ リンクの URL*" を提供することもできます。

- **説明** (必須) – ユーザーがあなたのオファーを購入するかどうかを決定するのに役立つように、体験版、デモの内容、試用するユーザーの目的、試せる機能、関連情報について説明します。 このフィールドには、最大で 3,000 文字のテキストを入力できます。 

- **アクセス情報** (Azure Resource Manager およびロジックの体験版では必須) – 顧客がこの体験版にアクセスして使用するうえで知っておく必要がある情報を説明します。 お客様のオファーの使用シナリオのほか、顧客が体験版を通じて機能にアクセスするために知っておくべき正確な情報を説明します。 このフィールドには、最大で 10,000 文字のテキストを入力できます。

- **ユーザー マニュアル** (必須) – 体験版エクスペリエンスの詳細なチュートリアル。 ユーザー マニュアルは、体験版のエクスペリエンスで顧客が使用できる内容を正確にカバーし、疑問がある場合に参照するものとして機能する必要があります。 ファイルは PDF 形式にして、アップロード後に名前 (最大 255 文字) を付ける必要があります。

- **ビデオ** (省略可能) – ビデオは YouTube または Vimeo にアップロードすることができ、リンクとサムネイル画像 (533 x 324 ピクセル) を使用してここで参照できます。これにより顧客は、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、体験版への理解を深めるのに役立つ一連の情報を確認できます。
  - **名前** (必須)
  - **URL (YouTube または Vimeo のみ)** (必須)
  - **サムネイル**画像 (ファイルは PNG 形式で、533 x 324 ピクセルである必要があります)

続行する前に、 **[下書きの保存]** を選択します。

## <a name="supplemental-content"></a>補足コンテンツ

このページでは、オファーの検証に役立つ、オファーに関する追加情報を提供できます。 この情報は顧客に表示されることも、マーケットプレースに公開されることもありません。

### <a name="target-release"></a>ターゲット リリース

ソリューションが対象とする Microsoft Dynamics Business Central のリリースを示します。 **[Current]\(現行\)** 、 **[Next major]\(次回のメジャー\)** 、または **[Next minor]\(次回のマイナー\)** 。 この情報によって、弊社でソリューションを適切にテストすることができます。

### <a name="supported-editions"></a>サポートされているエディション

オファーに Premium エディションの Microsoft Dynamics 365 Business Central が必要な場合、 **[Premium]** だけを選択します。 それ以外の場合、 **[Essentials]** と **[Premium]** の両方を選択します。

### <a name="key-usage-scenario"></a>主な使用シナリオ

ドキュメント (.pdf 形式) に一覧表示されているオファーの主な使用シナリオを一覧表示する PDF ファイルをアップロードする必要があります。 ここに一覧表示されているすべてのシナリオは、マーケットプレースにオファーを承認する前に、弊社の検証チームによって検証される場合があります。

### <a name="app-tests-automation"></a>アプリ テストの自動化

オファーがアドオン アプリの場合は、**アプリ テストの自動化**ファイル (.app) をアップロードする必要があります。 このファイルは、接続アプリには適用されません。

### <a name="test-accounts"></a>テスト アカウント

弊社の認定チームがオファーを適切に確認するためにテスト アカウントが必要な場合は、**テスト アカウント**情報と共に .pdf、.doc、または .docx ファイルをアップロードしてください。

## <a name="publish"></a>発行

### <a name="submit-offer-to-preview"></a>プレビューへのオファーの送信

オファーの必須セクションをすべて入力したら、ポータルの右上隅にある **[公開]** を選択します。 **[Review and publish]\(確認と公開\)** ページにリダイレクトされます。 

このオファーを公開するのが初めての場合、以下のことが可能です。

- オファーの各セクションの完了状態を確認する。
    - *[未開始]* - セクションは着手されておらず、完了する必要があることを意味します。
    - *[未完了]* - 修正が必要なエラーがセクションにあり、追加の情報を入力する必要があることを意味します。 セクションに戻って更新してください。
    - *[完了]* - セクションが完了していることを意味します。必須のデータはすべて入力済みであり、エラーはありません。 オファーを送信するには、オファーのセクションがすべて完了状態でなければなりません。
- **[認定の注意書き]** セクションで、アプリの理解に役立つ補足事項に加えて、テストの指示を認定チームに提供し、アプリが確実に正しくテストされるようにします。
- **[送信]** を選択して、公開するためにオファーを送信する。 お客様が確認して承認できるように、オファーのプレビュー バージョンが利用可能になったら、Microsoft から電子メールが 届きます。 パートナー センターに戻り、そのオファーに対して **[一般公開する]** を選択して、自分のオファーを一般に公開します (プライベート オファーの場合はプライベート対象ユーザーに公開されます)。

## <a name="next-steps"></a>次のステップ

- [商業マーケットプレースで既存のオファーを更新する](./update-existing-offer.md)
