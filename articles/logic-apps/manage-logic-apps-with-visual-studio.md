---
title: Visual Studio と Cloud Explorer を使用してロジック アプリを編集および管理する
description: Visual Studio と Cloud Explorer を使用して、ロジック アプリの編集、更新、管理、ソース管理への追加、デプロイを行います
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 6319d2d72df69cc9633bd2b2ff8e777c2a48966a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982533"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Visual Studio でロジック アプリを管理する

ロジック アプリは [Azure Portal](https://portal.azure.com) で作成、編集、管理、およびデプロイできますが、ロジック アプリをソース管理に追加したり、各種のバージョンを発行したり、さまざまなデプロイメント環境用の [Azure Resource Manager](../azure-resource-manager/management/overview.md) テンプレートを作成したりする場合は Visual Studio も使用できます。 Visual Studio Cloud Explorer では、他の Azure リソースと共にロジック アプリを検索して管理できます。 たとえば、Azure Portal に既に展開されているロジック アプリのオープン、ダウンロード、編集、実行、実行履歴の表示、無効化、有効化を行うことができます。 初めて Visual Studio で Azure Logic Apps を使う場合は、[Visual Studio でロジック アプリを作成する方法](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)を学習してください。

> [!IMPORTANT]
> Visual Studio からロジック アプリを展開または発行すると、Azure Portal にあるそのアプリのバージョンが上書きされます。 したがって、Azure Portal で行った変更を残しておきたい場合は、次に Visual Studio から展開または発行する前に、Azure Portal から [Visual Studio のロジック アプリを更新する](#refresh)必要があります。

<a name="requirements"></a>

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* まだお持ちでない場合は、以下のツールをダウンロードしてインストールしてください。

  * [Visual Studio 2019、2017、または 2015 - Community Edition 以降](https://aka.ms/download-visual-studio)。 このクイックスタートでは、無料の Visual Studio Community 2017 を使用します。

    > [!IMPORTANT]
    > Visual Studio 2019 または 2017 をインストールする場合は、 **[Azure の開発]** ワークロードを選択してください。
    > 詳細については、「[Visual Studio Cloud Explorer で Azure アカウントに関連付けられているリソースを管理する](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)」を参照してください。

    Cloud Explorer for Visual Studio 2015 をインストールするには、[Visual Studio Marketplace から Cloud Explorer をダウンロード](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)します。 詳細については、[Visual Studio Cloud Explorer での Azure アカウントに関連付けられているリソースの管理 (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015) に関するページをご覧ください。

  * [Azure SDK (2.9.1 以降)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 必要なバージョンの Visual Studio 拡張機能用の最新の Azure Logic Apps ツール。

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Tools は、Visual Studio Marketplace から直接ダウンロードしてインストールできます。または、[この拡張機能を Visual Studio 内からインストールする方法](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)を確認できます。 インストールが完了したら、必ず Visual Studio を再起動してください。

* 組み込みの Logic Apps デザイナーを使用する際の Web へのアクセス

  デザイナーが Azure でリソースを作成し、ロジック アプリでコネクタからプロパティとデータを読み取るためには、インターネット接続が必要です。 たとえば、Dynamics CRM Online コネクタを使用する場合、デザイナーは CRM インスタンスの使用可能な既定およびカスタムのプロパティをチェックします。

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>ロジック アプリを検索する

Visual Studio では、Azure サブスクリプションに関連付けられていて、Cloud Explorer を使って Azure Portal に展開されたすべてのロジック アプリを検索できます。

1. Visual Studio を開きます。 **[表示]** メニューで **[Cloud Explorer]** を選びます。

1. Cloud Explorer で **[アカウント管理]** を選びます。 ロジック アプリに関連付けられている Azure サブスクリプションを選び、 **[適用]** を選びます。 次に例を示します。

   ![[アカウント管理] を選ぶ](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. **[リソース グループ]** または **[リソースの種類]** のどちらで検索するかに応じて、次のいずれかの手順に従います。

   * **リソース グループ**:Cloud Explorer では、Azure サブスクリプションの下に、そのサブスクリプションと関連付けられているすべてのリソース グループが表示されます。 目的のロジック アプリを含むリソース グループを展開して、ロジック アプリを選びます。

   * **リソースの種類**:Azure サブスクリプションの **[Logic Apps]** を展開します。 サブスクリプションに関連付けられているすべてのロジック アプリが表示されるので、目的のロジック アプリを選びます。

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Visual Studio で開く

Visual Studio では、Azure portal から直接デプロイされているか、Visual Studio から Azure リソース グループ プロジェクトとしてデプロイされている作成済みのロジック アプリを開くことができます。

1. Cloud Explorer を開いて、ロジック アプリを検索します。

1. ロジック アプリのショートカット メニューで、 **[Logic App Editor で開く]** を選びます。

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

   この例では、リソースの種類ごとに示されているので、ロジック アプリは **[Logic Apps]** セクションに表示されます。

   ![Azure Portal から展開済みのロジック アプリを開く](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   ロジック アプリ デザイナーでロジック アプリを開いた後、デザイナーの下端で **[コード ビュー]** を選んで、基になっているロジック アプリの定義構造を確認することができます。 ロジック アプリの配置テンプレートを作成する場合は、そのロジック アプリの [Azure Resource Manager テンプレートをダウンロードする方法](#download-logic-app)を学習してください。 詳しくは、[Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)に関するページをご覧ください。

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Azure からのダウンロード

[Azure Portal](https://portal.azure.com) からロジック アプリをダウンロードし、[Azure Resource Manager](../azure-resource-manager/management/overview.md) テンプレートとして保存することができます。 その後、Visual Studio でそのテンプレートをローカルに編集し、さまざまな展開環境用にロジック アプリをカスタマイズできます。  ロジック アプリをダウンロードすると、[Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)内のその定義は自動的に "*パラメーター化*" され、JavaScript Object Notation (JSON) も使用されます。

1. Visual Studio で Cloud Explorer を開きます。 Azure からダウンロードするロジック アプリを検索して選びます。

1. アプリのショートカット メニューで、 **[Logic App Editor で開く]** を選びます。

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio の最新の更新プログラムが適用されていることを確認してください。

   ロジック アプリ デザイナーが開き、ロジック アプリが表示されます。 ロジック アプリの基になっている定義と構造を確認するには、デザイナーの下端で **[コード ビュー]** を選びます。

1. デザイナーのツール バーで、 **[ダウンロード]** を選びます。

   ![Azure portal からロジック アプリをダウンロードする](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 場所の指定を求められたら、その場所を参照し、ロジック アプリの定義の Resource Manager テンプレートを JSON (.json) ファイル形式で保存します。

   ロジック アプリの定義は、Resource Manager テンプレートの `resources` サブセクションにあります。 Visual Studio でロジック アプリの定義と Resource Manager テンプレートを編集できるようになります。 また、このテンプレートを [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)として Visual Studio ソリューションに追加することもできます。 [Visual Studio でのロジック アプリ用の Azure リソース グループ プロジェクト](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)について確認してください。

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>統合アカウントへのリンク

企業間 (B2B) エンタープライズ統合シナリオ向けのロジック アプリを構築するには、お使いのロジック アプリを、そのロジック アプリと同じリージョンにある以前作成した[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)にリンクさせます。 統合アカウントには取引先、契約、スキーマ、マップなどの B2B 成果物が含まれているため、これを使用すると、お使いのロジック アプリでは XML 検証やフラット ファイルのエンコードとデコードに B2B コネクタを使用できます。 [このリンクは Azure portal を使用して作成](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)できますが、[前提条件](#requirements)を満たした後には Visual Studio を使用することもできます。また、ロジック アプリは [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)内に JSON (.json) ファイルとして存在することになります。 [Visual Studio でのロジック アプリ用の Azure リソース グループ プロジェクト](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)について確認してください。

1. Visual Studio で、お使いのロジック アプリを含む Azure リソース グループ プロジェクトを開きます。

1. ソリューション エクスプローラーで **<ロジック アプリ名>.json** ファイルのショートカット メニューを開き、 **[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します。 (Ctrl + L)

   ![ロジック アプリ デザイナーでロジック アプリの .json ファイルを開く](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio に最新の更新プログラムが適用されており、Azure Logic Apps ツール拡張機能がインストールされていることを確認してください。

1. [プロパティ] ウィンドウにお使いのロジック アプリの **[統合アカウント]** プロパティが表示されるよう、デザイナーのタブまたはサーフェスを選択し、ロジック アプリ デザイナーにフォーカスがあることを確認します。

   ![[プロパティ] ウィンドウ - [統合アカウント] プロパティ](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > [プロパティ] ウィンドウがまだ開いていない場合、 **[表示]** メニューから、 **[プロパティ ウィンドウ]** を選択します。 (F4 を押す)

1. **[統合アカウント]** プロパティの一覧を開き、次のように、ロジック アプリにリンクする統合アカウントを選択します。

   ![[統合アカウント] プロパティの一覧を開く](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 完了したら、忘れずにその Visual Studio ソリューションを保存します。

Visual Studio で **[統合アカウント]** プロパティを設定し、そのロジック アプリを Azure Resource Manager テンプレートとして保存すると、そのテンプレートには、選択した統合アカウントのパラメーター宣言も含まれます。 テンプレート パラメーターとロジック アプリの詳細については、[ロジック アプリの自動デプロイの概要](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)に関する記事を参照してください。

<a name="change-location"></a>

## <a name="change-deployment-location"></a>デプロイメントの場所を変更する

Visual Studio で、デプロイの自動化に利用する [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)内に JSON (.json) ファイルとしてロジック アプリが存在する場合、そのロジック アプリには場所の種類と特定の場所が設定されます。 この場所は、Azure リージョンまたは既存の[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment.md) です。

ロジック アプリの場所の種類や場所を変更するには、ロジック アプリ デザイナーを利用し、ソリューション エクスプローラーからロジック アプリのワークフロー定義 (.json) ファイルを開く必要があります。 Cloud Explorer を使用してこれらのプロパティを変更することはできません。

> [!IMPORTANT]
> 場所の種類を **[リージョン]** から [**統合サービス環境**](connect-virtual-network-vnet-isolated-environment-overview.md)に変更すると、課金、[上限](logic-apps-limits-and-config.md#integration-account-limits)、[統合アカウント サポート](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)などに使用されるロジック アプリの[価格設定モデル](logic-apps-pricing.md#fixed-pricing)に影響します。 場所に別の種類を選択する前に、ロジック アプリに与える影響を理解しておいてください。

1. Visual Studio で、お使いのロジック アプリを含む Azure リソース グループ プロジェクトを開きます。

1. ソリューション エクスプローラーで `<logic-app-name>.json` ファイルのショートカット メニューを開き、 **[Open With Logic App Designer]\(ロジック アプリ デザイナーで開く\)** を選択します。 (Ctrl + L)

   ![ロジック アプリ デザイナーでロジック アプリの .json ファイルを開く](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > このコマンドが Visual Studio 2019 にない場合は、Visual Studio に最新の更新プログラムが適用されており、Azure Logic Apps ツール拡張機能がインストールされていることを確認してください。

1. [プロパティ] ウィンドウにお使いのロジック アプリの **[Choose Location Type]\(場所の種類の選択\)** プロパティと **[場所]** プロパティが表示されるよう、デザイナーのタブまたはサーフェスを選択し、ロジック アプリ デザイナーにフォーカスがあることを確認します。 プロジェクトの場所の種類は **[リージョン]** または **[統合サービス環境]** に設定されています。

   ![[プロパティ ウィンドウ] - [Choose Location Type]\(場所の種類の選択\) プロパティと [場所] プロパティ](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > [プロパティ] ウィンドウがまだ開いていない場合、 **[表示]** メニューから、 **[プロパティ ウィンドウ]** を選択します。 (F4 を押す)

1. 場所の種類を変更するには、 **[Choose Location Type]\(場所の種類の選択\)** プロパティを開き、必要な場所の種類を選択します。

   たとえば、場所の種類が **[統合サービス環境]** の場合、 **[リージョン]** を選択できます。

   ![[Choose Location Type]\(場所の種類の選択\) - 場所の種類の変更](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 特定の場所を変更するには、 **[場所]** プロパティの一覧を開きます。 場所の種類に基づき、たとえば次のように必要な場所を選択します。

   * 別の Azure リージョンを選択します。

     !["場所" プロパティの一覧を開き、別の Azure リージョンを選択します](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 別の ISE を選択します。

     !["場所" プロパティの一覧を開き、別の ISE を選択します](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 完了したら、忘れずにその Visual Studio ソリューションを保存します。

Visual Studio で場所の種類または場所を変更し、そのロジック アプリを Azure Resource Manager テンプレートとして保存すると、そのテンプレートには、その場所の種類と場所のパラメーター宣言も含まれます。 テンプレート パラメーターとロジック アプリの詳細については、[ロジック アプリの自動デプロイの概要](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)に関する記事を参照してください。

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Azure から更新する

Azure Portal で行ったロジック アプリの編集による変更を残しておきたい場合は、Visual Studio で保持されているそのアプリのバージョンを、これらの変更で更新する必要があります。

* Visual Studio のロジック アプリ デザイナーのツール バーで、 **[最新の情報に更新]** を選びます。

  または

* Visual Studio の Cloud Explorer で、ロジック アプリのショートカット メニューを開き、 **[最新の情報に更新]** を選びます。

![更新でロジック アプリを最新の情報に更新する](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>ロジック アプリの更新を発行する

Visual Studio から Azure にロジック アプリの更新を展開する準備ができたら、ロジック アプリ デザイナーのツール バーで **[発行]** を選びます。

![更新されたロジック アプリを Azure portal に発行する](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>ロジック アプリを手動で実行する

Azure に展開されているロジック アプリを、Visual Studio から手動で開始することができます。 ロジック アプリ デザイナーのツール バーの **[トリガーの実行]** を選びます。

![ロジック アプリのトリガーを手動で実行する](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>実行履歴を確認する

ロジック アプリの実行の状態を確認して問題を診断するには、実行の入力や出力などの詳細を Visual Studio で確認できます。

1. Cloud Explorer でロジック アプリのショートカット メニューを開き、 **[実行履歴を開く]** を選びます。

   ![ロジック アプリの実行履歴を開く](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 特定の実行の詳細を表示するには、実行をダブルクリックします。 次に例を示します。

   ![特定の実行に関する情報を表示する](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > プロパティでテーブルを並べ替えるには、そのプロパティの列ヘッダーを選びます。

1. 入力と出力を確認するステップを展開します。例えば次のようにします。

   ![各ステップの入力と出力を表示する](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>ロジック アプリを無効または有効にする

ロジック アプリを削除することなく、次にトリガー条件が満たされたときにトリガーが発動しないようにすることができます。 ロジック アプリを無効にすると、それ以降、Logic Apps エンジンはロジック アプリのワークフロー インスタンスを作成および実行しなくなります。 Cloud Explorer でロジック アプリのショートカット メニューを開き、 **[無効化]** を選びます。

![Cloud Explorer でロジック アプリを無効にする](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> ロジック アプリを無効にすると、新しい実行は開始されなくなります。 進行中および保留中のすべての実行は完了するまで引き続き実行され、完了するには時間がかかる場合があります。

ロジック アプリを再度有効にするには、Cloud Explorer でロジック アプリのショートカット メニューを開き、 **[有効化]** を選びます。

![Cloud Explorer でロジック アプリを有効にする](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>ロジック アプリを削除する

Azure Portal からロジック アプリを削除するには、Cloud Explorer でロジック アプリのショートカット メニューを開き、 **[削除]** を選びます。

![Azure portal からロジック アプリを削除する](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> ロジック アプリを削除にすると、新しい実行は開始されなくなります。 すべての進行中および保留中の実行は取り消されます。 何千もの実行がある場合、取り消しが完了するまでかなりの時間がかかる場合があります。 

## <a name="troubleshooting"></a>トラブルシューティング

Logic Apps デザイナーでロジック アプリ プロジェクトを開いたときに、Azure サブスクリプションを選択するオプションが表示されないことがあります。 代わりに、ロジック アプリには、使用するもの以外の Azure サブスクリプションが開かれます。 ロジック アプリの .json ファイルを開いた後、Visual Studio は今後使用できるように、最初に選択したサブスクリプションをキャッシュするため、この動作が行われます。 この問題を解決するには、次の手順のいずれかを試してください。

* ロジック アプリの .json ファイルの名前を変更します。 サブスクリプションのキャッシュは、ファイル名に依存します。

* ソリューション内の*すべての*ロジック アプリの以前に選択されたサブスクリプションを削除するには、ソリューションのディレクトリにある非表示の Visual Studio 設定フォルダー (.vs) を削除します。 この場所には、サブスクリプションの情報が格納されます。

## <a name="next-steps"></a>次のステップ

この記事では、Visual Studio で展開済みのロジック アプリを管理する方法について学習しました。 次に、展開に合わせてロジック アプリの定義をカスタマイズする方法を学習してください。

> [!div class="nextstepaction"]
> [JSON でロジック アプリの定義を作成する](../logic-apps/logic-apps-author-definitions.md)
