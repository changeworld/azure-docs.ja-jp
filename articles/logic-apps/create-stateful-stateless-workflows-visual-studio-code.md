---
title: Visual Studio Code でオートメーション ワークフロー (プレビュー) を作成する
description: アプリ、データ、クラウド サービス、オンプレミス システムを統合するために Visual Studio Code で Azure Logic Apps (プレビュー) 拡張機能を使用してステートレスまたはステートフルのオートメーション ワークフローを作成します
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 94d970390f62107a82dc586605d34dd61cae0c26
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993078"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps (プレビュー) 拡張機能を使用して Visual Studio Code でステートフルまたはステートレスのワークフローを作成する

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

アプリ、データ、クラウド サービス、およびシステムを統合するロジック アプリ ワークフローを作成するには、Visual Studio Code と Azure Logic Apps (プレビュー) 拡張機能を使用して、["*ステートフル*" および "*ステートレス*" のロジック アプリ ワークフロー](#stateful-stateless)を作成して実行します。

![Visual Studio Code とロジック アプリ ワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

パブリック プレビュー拡張機能を使用して作成したロジック アプリでは、新しい **[ロジック アプリ (プレビュー)]** リソースの種類が使用され、[Azure Functions](../azure-functions/functions-overview.md) ランタイムが利用されます。 この新しいリソースの種類には複数のワークフローを含めることができます。これは、複数の関数を含めることができる **[関数アプリ]** リソースの種類に似ています。

一方、Visual Studio Code と Azure portal で作成して使用するために、元の **[ロジック アプリ]** リソースの種類もまだ存在しています。 ただし、元のリソースの種類のエクスペリエンスは、新しいリソースの種類とは別のものです。 現時点では、 **[ロジック アプリ]** と **[ロジック アプリ (プレビュー)]** の両方のリソースの種類が Visual Studio Code と Azure portal に同時に存在できます。 Azure サブスクリプションでデプロイされているすべてのロジック アプリを表示してアクセスできますが、それぞれのカテゴリとセクションで個別に表示されています。

この記事では、[このパブリック プレビューの概要](#whats-new)を示し、 **[ロジック アプリ (プレビュー)]** リソースの種類に関するさまざまな側面と、Visual Studio Code を使用してこのリソースを作成する方法について説明します。

* [ステートフルおよびステートレス](#stateful-stateless)のロジック アプリの違い。

* [セットアップ要件](#prerequisites)を満たし、パブリック プレビュー拡張機能用に [Visual Studio Code を設定する](#set-up)方法。

* [プロジェクトを作成し、ワークフロー テンプレートを選択して](#create-project)、新しい **[ロジック アプリ (プレビュー)]** ワークフローを作成する方法。

* これらの新しいロジック アプリを Visual Studio Code から [Azure に](#publish-azure)直接発行する方法、または任意の場所で実行できる [Docker コンテナーに](#deploy-docker)発行する方法。 Docker の詳細については、「[Docker とは](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)」を参照してください。

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>このパブリック プレビューの内容

Azure Logic Apps (プレビュー) 拡張機能を使用すると、次のように Visual Studio Code のローカル開発環境に Logic Apps の現在の機能および追加機能が多数提供されます。

* サービスとしてのソフトウェア (SaaS) およびサービスとしてのプラットフォーム (PaaS) のアプリとサービス用の [300 以上のコネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)に加えて、オンプレミス システム用のコネクタから、統合とオートメーション ワークフローのためのロジック アプリを構築します。

  * Azure Service Bus、Azure Event Hubs、SQL Server などの一部のマネージド コネクタは、組み込みのネイティブ トリガーや HTTP アクションなどのアクションと同様に動作します。

  * Azure Logic Apps サービスがクラウド接続のランタイム エンドポイントに要求を送信するために使用できる Shared Access Signature (SAS) 接続文字列を生成するため、どこからでも実行できるロジック アプリを作成してデプロイします。 Logic Apps サービスでは、Azure にデプロイするときにこれらの値を Azure Key Vault に簡単に格納できるように、これらの接続文字列が他のアプリケーション設定と共に保存されます。

    > [!NOTE]
    > 既定では、 **[ロジック アプリ (プレビュー)]** リソースには、実行時に接続を認証するために自動的に有効にされる[システム割り当て ID](../logic-apps/create-managed-service-identity.md) があります。 この ID は、接続の作成時に使用する認証資格情報または接続文字列とは異なります。 この ID を無効にした場合、接続は実行時に機能しません。

* メモリ内でのみ実行されるステートレスなロジック アプリを作成します。これにより、実行履歴やアクション間のデータが外部ストレージに保持されないため、処理が速くなり、応答が速く、スループットが向上し、実行にかかるコストも少なくなります。 必要に応じて、デバッグを容易にするために実行履歴を有効にすることができます。 詳細については、「[ステートフルおよびステートレスのロジック アプリ](#stateful-stateless)」を参照してください。

* Visual Studio Code 開発環境でローカルにロジック アプリをテストします。

* ロジック アプリを Visual Studio Code から [Azure App Service](../app-service/environment/intro.md) や [Docker コンテナー](/dotnet/core/docker/introduction)などのさまざまなホスティング環境に直接発行し、デプロイします。

> [!NOTE]
> 現在の既知の問題の詳細については、プレビュー拡張機能の[既知の問題に関する GitHub ページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>ステートフルおよびステートレスのロジック アプリ

* *ステートレス*

  前のイベントのデータを保持、確認、または参照する必要がある場合は、ステートフルなロジック アプリを作成します。 これらのロジック アプリでは、各アクションの入出力とワークフローの状態が外部ストレージに保持されます。これにより、各実行が完了した後に実行の詳細と履歴を確認できます。 ステートフルなロジック アプリは、サービス停止が発生したときに高い回復性を実現します。 サービスとシステムを復元した後に、中断されたロジック アプリの実行を保存済みの状態から再構築し、ロジック アプリを再実行して完了することができます。 ステートフルなワークフローは、最大 1 年間実行を継続できます。

* *ステートレス*

  前のイベントのデータを保存、確認、または参照する必要がない場合は、ステートレスなロジック アプリを作成します。 これらのロジック アプリは、この情報を外部ストレージに転送するのではなく、各アクションとそのワークフローの状態の入出力をメモリ内でのみ保持します。 その結果、ステートレスなロジック アプリでは実行の詳細と履歴が外部ストレージに保持されないため、通常は 5 分未満に実行時間が短縮され、応答時間が短縮され、スループットが向上し、実行コストが削減されます。 ただし、サービス停止が発生した場合、中断された実行は自動的には復元されないため、呼び出し元は中断された実行を手動で再送信する必要があります。 デバッグを容易にするために、ステートレスなロジック アプリの[実行履歴を有効にする](#run-history)ことができます。

  ステートレスなワークフローでは、現在、トリガーではなく[マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)に対するアクションのみがサポートされています。 ワークフローを開始するには、[組み込みの Request トリガー、Event Hubs トリガー、または Service Bus トリガー](../connectors/apis-list.md#built-ins)を選択します。 サポートされていないトリガー、アクション、コネクタの詳細については、[サポートされていない機能](#unsupported)を参照してください。

ステートフルなロジック アプリとステートレスなロジック アプリの間で入れ子になったロジック アプリの動作の違いについては、「[ステートフルなロジック アプリとステートレスなロジック アプリの入れ子になった動作の違い](#nested-behavior)」を参照してください。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>価格モデル

新しい **[ロジック アプリ (プレビュー)]** リソースの種類をデプロイすると、ホスティング プランを選択するように求められます。具体的には、価格モデルとして使用するために、[App Service プランまたは Premium プラン](../azure-functions/functions-scale.md)を選択する必要があります。 App Service プランを選択した場合は、[[価格レベル]](../app-service/overview-hosting-plans.md) も選択するように求められます。 パブリック プレビュー期間中は、App Service でロジック アプリを実行しても、選択したプランの他に "*追加*" 料金は発生しません。

ステートフルなロジック アプリは[外部ストレージ](../azure-functions/functions-scale.md#storage-account-requirements)を使用するため、Azure Storage の価格モデルは、Azure Logic Apps ランタイムが実行するストレージ トランザクションに適用されます。 たとえば、キューはスケジュール設定に使用され、テーブルと BLOB はワークフローの状態の格納に使用されます。

この新しいリソースの種類に適用される価格モデルの詳細については、次のトピックを参照してください。

* [Azure Functions のスケールとホスティング](../azure-functions/functions-scale.md)
* [Azure App Service でスケールアップする](../app-service/manage-scale-up.md)
* [Azure Functions の価格の詳細](https://azure.microsoft.com/pricing/details/functions/)
* [App Service の価格の詳細](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Azure Storage の価格の詳細](https://azure.microsoft.com/pricing/details/storage/)

## <a name="prerequisites"></a>前提条件

### <a name="access-and-connectivity"></a>アクセスと接続

* 要件をダウンロードしたり、Visual Studio Code から Azure アカウントに接続したり、Visual Studio Code から Azure、Docker コンテナー、またはその他の環境に発行したりするためのインターネットへのアクセス。

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* この記事の同じロジック アプリの例をビルドするには、Microsoft の職場または学校アカウントを使用してサインインする Office 365 Outlook 電子メール アカウントが必要です。

  Outlook.com や [Gmail](../connectors/connectors-google-data-security-privacy-policy.md) など、[Azure Logic Apps でサポートされている別の電子メール コネクタ](/connectors/)を使用する場合でもこの例に従うことができ、全体的な手順は同じです。ただし、ユーザー インターフェイスとオプションは一部異なる場合があります。 たとえば、Outlook.com コネクタを使用する場合は、代わりに個人用 Microsoft アカウントを使用してサインインします。

### <a name="tools"></a>ツール

* [Visual Studio Code 1.30.1 (2019 年 1 月) 以降](https://code.visualstudio.com/) (無料)。 また、以下の Visual Studio Code 用の追加ツールをダウンロードしてインストールします (まだインストールしていない場合)。

  * [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。これにより、Visual Studio Code 内の他のすべての Azure 拡張機能に対して、共通する単一の Azure サインインとサブスクリプションのフィルター処理が可能になります。

  * [Visual Studio Code 用の C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。これにより、F5 機能でロジック アプリを実行できるようになります。

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md) (バージョン [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) または [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936))。Microsoft インストーラー (MSI) で提供されます。 これらのツールには、Visual Studio Code で実行される Azure Functions ランタイムを動かす同じランタイムのバージョンが含まれています。

    > [!IMPORTANT]
    > これらのバージョンより古いバージョンをインストールしている場合は、最初にそのバージョンをアンインストールするか、ダウンロードしてインストールしたバージョンが PATH 環境変数で参照されていることを確認します。
    >
    > JavaScript コードを実行するために[**インライン コード** アクション](../logic-apps/logic-apps-add-run-inline-code.md)を使用する場合、このアクションではバージョン 2x がサポートされていないため、Azure Functions ランタイム バージョン 3x を使用する必要があります。 また、現在、このアクションは Linux オペレーティング システムではサポートされていません。

  * [Visual Studio Code 用 Azure Logic Apps (プレビュー) 拡張機能](https://go.microsoft.com/fwlink/p/?linkid=2143167)。 このパブリック プレビュー拡張機能を使用すると、ステートフルおよびステートレスなロジック アプリを作成し、Visual Studio Code でローカルにテストすることができます。

    現時点では、元の **Azure Logic Apps** 拡張機能と新しい **Azure Logic Apps (プレビュー)** 拡張機能の両方を Visual Studio Code に同時にインストールすることができます。 Visual Studio Code ツールバーの Azure アイコンを選択すると、Azure にデプロイされているすべてのロジック アプリを表示できます。ただし、各リソースの種類は、**Logic Apps** および **Azure Logic Apps (プレビュー)** の拡張機能セクションに個別に表示されます。

    > [!IMPORTANT]
    > **Azure Logic Apps (プライベート プレビュー)** 拡張機能を使用してロジック アプリを作成した場合、これらのロジック アプリはパブリック プレビュー拡張機能では動作しません。 ただし、プライベート プレビュー拡張機能をアンインストールし、必要なクリーンアップを実行し、パブリック プレビュー拡張機能をインストールすることによって、これらのロジック アプリを移行できます。 その後、Visual Studio Code で新しいプロジェクトを作成し、以前に作成したロジック アプリの **workflow.definition** ファイルを新しいプロジェクトにコピーできます。
    >
    > そのため、パブリック プレビュー拡張機能をインストールする前に、以前のバージョンをすべてアンインストールしてから、次のビルド成果物を削除してください。
    >
    > * **Microsoft.Azure.Functions.ExtensionBundle.Workflows** フォルダー。このフォルダーには以前の拡張機能バンドルが含まれ、次のいずれかのパスに配置されています。
    >
    >   * `C:\Users\<username>\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\<username>.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * **microsoft.azure.workflows.webjobs.extension** フォルダー。このフォルダーはプライベート プレビュー拡張機能の [NuGet](/nuget/what-is-nuget) キャッシュであり、次のパスに配置されています。
    >
    >   `C:\Users\<username>\.nuget\packages`

    **Azure Logic Apps (プレビュー)** 拡張機能をインストールするには、次の手順に従います。

    1. Visual Studio Code の左側のツールバーで、 **[拡張機能]** を選択します。

    1. 拡張機能の検索ボックスに、「`azure logic apps preview`」と入力します。 結果リストから、 **[Azure Logic Apps (プレビュー)]** **>** **[インストール]** の順に選択します。

       インストールが完了すると、パブリック プレビュー拡張機能が **[拡張機能:インストール済み]** リストに表示されます。

       ![Visual Studio Code にインストールされている拡張機能の一覧で下線が付いた "Azure Logic Apps (プレビュー)" 拡張機能を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* この記事で作成したロジック アプリの例をテストするには、Request トリガーに呼び出しを送信できるツールが必要です。これは、ロジック アプリの例の最初の手順です。 このようなツールがない場合は、[Postman](https://www.postman.com/downloads/) をダウンロードしてインストールし、使用することができます。

* 診断ログとトレース機能を簡単にするために、[Application Insights](../azure-monitor/app/app-insights-overview.md) リソースを追加して使用することができます。 このリソースは、ロジック アプリのデプロイ中に作成するか、ロジック アプリをデプロイした後に Azure portal で作成できます。

### <a name="storage-requirements"></a>ストレージの要件

現時点では、新しい **[ロジック アプリ (プレビュー)]** リソースの作成は Mac OS では使用できません。 ただし、Windows またはその他の OS (Linux など) では、このストレージ要件を設定します。

1. [Azure ストレージ エミュレーター 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179) をダウンロードしてインストールします。

1. エミュレーターを実行するには、無料の [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658) など、ローカルの SQL DB をインストールする必要があります。 詳細については、「[開発とテストに Azure ストレージ エミュレーターを使用する](../storage/common/storage-use-emulator.md)」を参照してください。

   > [!IMPORTANT]
   > ロジック アプリ デザイナーを開いてロジック アプリのワークフローを作成する前に、必ずエミュレーターを起動してください。 それ以外の場合は、`Workflow design time could not be started` というメッセージが表示されます。
   >
   > ![実行中の Azure ストレージ エミュレーターを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code を設定する

1. すべての拡張機能が正しくインストールされていることを確認するには、Visual Studio Code を再読み込みまたは再起動します。

1. パブリック プレビュー拡張機能が最新の更新プログラムを取得できるように、Visual Studio Code によって拡張機能の更新プログラムが自動的に検出され、インストールされることを確認します。

   この設定を確認するには、次の手順を実行します。

   1. **[ファイル]** メニューで、 **[基本設定]** **>** **[設定]** の順に選択します。

   1. **[ユーザー]** タブで、 **[機能]** **>** **[拡張機能]** の順に選択します。

   1. **[更新プログラムの自動チェック]** と **[自動更新]** が選択されていることを確認します。

1. 次の **Azure Logic Apps (プレビュー)** 拡張機能の設定が Visual Studio Code で有効にされていることを確認します。

   * **Azure Logic Apps V2:パネル モード**
   * **Azure Logic Apps V2:プロジェクト ランタイム**

   1. **[ファイル]** メニューで、 **[基本設定]** **>** **[設定]** の順に選択します。

   1. **[ユーザー]** タブで、 **>** **[拡張機能]** **>** **[Azure Logic Apps (プレビュー)]** の順に選択します。

   1. **[Azure Logic Apps V2:パネル モード]** で、 **[パネル モードを有効にする]** が選択されていることを確認します。 **[Azure Logic Apps V2:プロジェクト ランタイム]** で、前にインストールした [Azure Functions Core Tools のバージョン](#prerequisites)に基づいて、バージョンを **~3** または **~2** に設定します。

      > [!IMPORTANT]
      > JavaScript コードを実行するために[**インライン コード** アクション](../logic-apps/logic-apps-add-run-inline-code.md)を使用する場合、このアクションではバージョン 2 がサポートされていないため、プロジェクト ランタイム バージョン 3 を使用する必要があります。 また、現在、このアクションは Linux オペレーティング システムではサポートされていません。

      !["Azure Logic Apps (プレビュー)" 拡張機能の Visual Studio Code 設定を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

1. Visual Studio Code ツール バーで、Azure アイコンを選択します。

   ![Visual Studio Code ツールバーと選択された Azure アイコンを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure ウィンドウの **[Azure:Logic Apps (プレビュー)]** で、 **[Azure へのサインイン]** を選択します。 Visual Studio Code 認証ページが表示されたら、Azure アカウントでサインインします。

   ![Azure ウィンドウと Azure にサインインするために選択されたリンクを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   サインインすると、Azure ウィンドウに Azure アカウントのサブスクリプションが表示されます。 期待されるサブスクリプションが表示されない場合、または特定のサブスクリプションのみが表示されるようにする場合は、次の手順を実行します。

   1. サブスクリプションの一覧で、 **[サブスクリプションの選択]** ボタン (フィルター アイコン) が表示されるまで、最初のサブスクリプションの横にポインターを移動します。 フィルター アイコンを選択します。

      ![Azure ウィンドウと選択されたフィルター アイコンを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      または、Visual Studio Code のステータス バーで Azure アカウントを選択します。 

   1. 別のサブスクリプションの一覧が表示されたら、目的のサブスクリプションを選択し、 **[OK]** を必ず選択してください。

<a name="create-project"></a>

## <a name="create-a-local-project"></a>ローカル プロジェクトを作成する

ロジック アプリを作成する前に、Visual Studio Code からロジック アプリを管理およびデプロイできるように、ローカル プロジェクトを作成します。 基になるプロジェクトは、Azure Functions プロジェクト (関数アプリ プロジェクトとも呼ばれます) によく似ています。

1. コンピューター上に、Visual Studio Code で後で作成するプロジェクトに使用する "*空の*" ローカル フォルダーを作成します。

1. Visual Studio Code で、開いているすべてのフォルダーを閉じます。

1. Azure ウィンドウで、 **[Azure:Logic Apps (プレビュー)]** の横にある **[新しいプロジェクトの作成]** (フォルダーと稲妻が表示されたアイコン) を選択します。

   ![[新しいプロジェクトの作成] が選択されている Azure ウィンドウのツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender ファイアウォールによって `Code.exe` (Visual Studio Code) および `func.exe` (Azure Functions Core Tools) にネットワーク アクセスを許可するように求められた場合は、 **[プライベート ネットワーク (ホーム ネットワークや社内ネットワークなど)]** **>** **[アクセスを許可]** の順に選択します。

1. プロジェクト フォルダーを作成した場所を参照し、そのフォルダーを選択して続行します。

   ![新しく作成されたプロジェクト フォルダーと [選択] ボタンが選択された [フォルダーの選択] ダイアログ ボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 表示されたテンプレートの一覧で、 **[ステートフル ワークフロー]** または **[ステートレス ワークフロー]** を選択します。 この例では **[ステートフル ワークフロー]** を選択します。

   ![[ステートフル ワークフロー] が選択されているワークフロー テンプレートの一覧を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. ロジック アプリ ワークフローの名前を指定し、Enter キーを押します。 この例では、`example-workflow` という名前を使用しています。

   ![[新しいステートフル ワークフローの作成 (3/4)] ボックスとワークフロー名「example-workflow」を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. 次に表示される一覧で、 **[現在のウィンドウで開く]** を選択します。

   ![[現在のウィンドウで開く] が選択されている一覧を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code がリロードされ、エクスプローラー ウィンドウが開き、プロジェクトが表示されます。プロジェクトには、自動的に生成されたプロジェクト ファイルが含まれています。 たとえば、プロジェクトには、ロジック アプリ ワークフローの名前を示すフォルダーがあります。 このフォルダーにある `workflow.json` ファイルには、ロジック アプリ ワークフローの基になる JSON 定義が含まれています。

   ![エクスプローラー ウィンドウにプロジェクト フォルダー、ワークフロー フォルダー、および "workflow. json" ファイルが表示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

次に、ロジック アプリ デザイナーで **workflow.json** ファイルを開きます。

### <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>ロジック アプリ デザイナーでワークフロー定義ファイルを開く

デザイナーでワークフロー定義ファイルを開く前に、Visual Studio Code が Windows または Linux で実行されている場合は、Azure ストレージ エミュレーターが実行されていることを確認してください。 詳細については、「[前提条件](#prerequisites)」を参照してください。

1. ワークフローのプロジェクト フォルダーを展開します。 **workflow.json** ファイルのショートカット メニューを開き、 **[デザイナーで開く]** を選択します。

   ![エクスプローラー ウィンドウが表示され、workflow.json ファイルのショートカット ウィンドウで [デザイナーで開く] が選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   `Workflow design time could not be started` というエラー メッセージが表示された場合は、Azure ストレージ エミュレーターが実行されていることを確認します。 それ以外の場合は、次のトラブルシューティングの推奨事項を試してください。

   Visual Studio Code で、プレビュー拡張機能からの出力を確認します。

   1. **[表示]** メニューの **[出力]** を選択します。

   1. **[出力]** タイトル バーの一覧から **[Azure Logic Apps]** を選択して、プレビュー拡張機能の出力を表示できるようにします。たとえば、次のようになります。

      ![[Azure Logic Apps] が選択されている Visual Studio Code の [出力] ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. 出力を確認し、次のエラー メッセージが表示されているかどうかを調べます。

      ```text
      A host error has occurred during startup operation '<operation-ID>'.
      System.Private.CoreLib: The file 'C:\Users\<your-username>\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      このエラーは、以前にデザイナーを開こうとしたときに、プロジェクトを廃止または削除した場合に発生する可能性があります。 このエラーを解決するには、 **...\Users\\{your-username}\AppData\Local\Temp\Functions\ExtensionBundles** の位置にある **ExtensionBundles** フォルダーを削除し、デザイナーで **workflow.json** ファイルをもう一度開いてみてください。

1. **[Azure でコネクタを有効にする]** の一覧で、 **[Azure のコネクタを使用する]** を選択します。これは、Azure サービスのコネクタだけでなく、Azure portal で利用できるすべてのマネージド コネクタに適用されます。

   ![[Azure でコネクタを有効にする] の一覧が開き、[Azure のコネクタを使用する] が選択されているエクスプローラー ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. リソース グループの一覧で、 **[新しいリソース グループの作成]** を選択します。

   ![リソース グループの一覧が表示され、[新しいリソース グループの作成] が選択されたエクスプローラー ウィンドウを示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. リソース グループの名前を指定し、Enter キーを押します。 この例では、`example-logic-app-preview-rg` を使用します。

   ![エクスプローラー ウィンドウとリソース グループ名のボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 場所の一覧で、リソース グループとリソースの作成に使用する Azure リージョンを見つけて選択します。 この例では、 **[米国中西部]** を使用します。

   > [!NOTE]
   > 現在、一部のリージョンはサポートされていませんが、更新は進行中です。 詳細については、プレビュー拡張機能の[既知の問題に関する GitHub ページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

   ![エクスプローラー ウィンドウと場所の一覧が表示され、[米国中西部] が選択されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   この手順を実行すると、Visual Studio Code のロジック アプリ デザイナーが開きます。

   > [!NOTE]
   > Visual Studio Code でワークフロー デザイン時 API が開始されると、起動に数秒かかる場合があるというメッセージが表示されます。 このメッセージは無視することも **[OK]** を選択することもできます。

   ロジック アプリ デザイナーが表示されると、 **[操作の選択]** プロンプトがデザイナーに表示され、既定で選択されます。これにより、 **[アクションの追加]** ウィンドウが表示されます。

   ![ロジック アプリ デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 次に、ワークフローに[トリガーとアクションを追加します](#add-trigger-actions)。

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>トリガーとアクションを追加する

**workflow.json** ファイルのショートカット メニューからロジック アプリ デザイナーを開くと、 **[操作の選択]** プロンプトがデザイナーに表示され、既定で選択されます。 トリガーとアクションを追加して、ワークフローの作成を開始できるようになりました。

この例のロジック アプリ ワークフローでは、次のトリガーとアクションを使用します。

* 組み込みの [Request トリガー](../connectors/connectors-native-reqres.md) **[HTTP 要求の受信時]** 。受信呼び出しまたは要求を受信し、他のサービスまたはロジック アプリが呼び出すことができるエンドポイントを作成します。

* [Office 365 Outlook アクション](../connectors/connectors-create-api-office365-outlook.md) **[メールの送信]** 。

* 組み込みの[応答アクション](../connectors/connectors-native-reqres.md)。応答を送信してデータを呼び出し元に返すために使用します。

### <a name="add-the-request-trigger"></a>要求トリガーを追加する

1. ネイティブに実行されるトリガーを選択できるように、デザイナーの横にある **[トリガーの追加]** ウィンドウの **[操作の選択]** 検索ボックスで **[組み込み]** が選択されていることを確認します。

1. **[操作の選択]** 検索ボックスに「`when a http request`」と入力し、 **[HTTP 要求の受信時]** という名前の組み込みの Request トリガーを選択します。

   ![ロジック アプリ デザイナーと、[HTTP 要求の受信時] トリガーが選択された [トリガーの追加] ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   トリガーがデザイナーに表示されると、トリガーの詳細ウィンドウが開き、トリガーのプロパティ、設定、およびその他のアクションが表示されます。

   ![[HTTP 要求の受信時] トリガーが選択されたロジック アプリ デザイナーが表示され、トリガー詳細ウィンドウが開いたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 詳細ウィンドウが表示されない場合は、デザイナーでトリガーが選択されていることを確認します。

1. デザイナー上の項目を削除する必要がある場合は、次の手順を実行します。

   1. デザイナーで項目を選択します。

   1. 右側に表示される項目の詳細ウィンドウで、省略記号 ( **...** ) ボタン **>** **[削除]** の順に選択します。 削除を確定するには、 **[OK]** を選択します。

      ![詳細ウィンドウが開いてデザイナーに選択した項目が表示され、省略記号ボタンと [削除] オプションが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook アクションを追加する

1. デザイナーで、追加したトリガーの下の **[新しいステップ]** を選択します。

   デザイナーに **[操作の選択]** プロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ウィンドウが再び表示されます。

1. Azure にデプロイされているマネージド コネクタのアクションを見つけて選択できるように、 **[アクションの追加]** ウィンドウの **[操作の選択]** 検索ボックスで **[Azure]** を選択します。

   この例では、Office 365 Outlook アクション **[メールの送信 (V2)]** を選択して使用します。

   ![ロジック アプリ デザイナーが表示され、[アクションの追加] ウィンドウで Office 365 Outlook の [メールの送信] アクションが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. アクションの詳細ウィンドウで **[サインイン]** を選択して、電子メール アカウントへの接続を作成できるようにします。

   ![ロジック アプリ デザイナーと、[サインイン] が選択された [メールの送信 (V2)] ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Visual Studio Code で電子メール アカウントへのアクセスに同意するかどうかを確認するメッセージが表示されたら、 **[開く]** を選択します。

   ![アクセス許可を確認する Visual Studio Code プロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 今後プロンプトが表示されないようにするには、認証ページを信頼されたドメインとして追加できるように、 **[信頼されているドメインの構成]** を選択します。

1. 後続のプロンプトに従ってサインインし、アクセスを許可して、Visual Studio Code に戻ることを許可します。

   > [!NOTE]
   > プロンプトを完了するまでの時間が長すぎると、認証プロセスがタイムアウトして失敗します。 この場合は、デザイナーに戻り、サインインし直して接続を作成します。

1. Azure Logic Apps プレビュー拡張機能によって、電子メール アカウントへのアクセスに同意するように求められたら、 **[開く]** を選択します。 後続のプロンプトに従って、アクセスを許可します。

   ![アクセス許可を確認するプレビュー拡張機能のプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 今後プロンプトが表示されないようにするには、 **[この拡張機能を再度表示しません]** を選択します。

1. デザイナーで **[メールの送信]** アクションが選択されていない場合は、そのアクションを選択します。

1. アクションの詳細ウィンドウの **[パラメーター]** タブで、アクションに必要な情報を指定します。次に例を示します。

   ![Office 365 Outlook の [メールの送信] アクションの詳細が表示されたロジック アプリ デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 電子メールの受信者。テストのためにご自分のメール アドレスを指定できます。 この例では、架空のメール アドレス `sophiaowen@fabrikam.com` を使用しています。 |
   | **件名** | はい | `An email from your example workflow` | メールの件名 |
   | **本文** | はい | `Hello from your example workflow!` | メールの本文の内容 |
   ||||

   > [!NOTE]
   > **[設定]** タブ、 **[実行までの期間]** タブ、または **[静的な結果]** タブの詳細ウィンドウで変更を加える場合は、タブを切り替えたり、デザイナーにフォーカスを変更したりする前に、必ず **[完了]** を選択して変更をコミットしてください。 それ以外の場合、Visual Studio Code では変更が保持されません。 詳細については、プレビュー拡張機能の[既知の問題に関する GitHub ページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

1. デザイナーで、 **[保存]** を選択します。

次に、Visual Studio Code でローカルにワークフローをデバッグしてテストします。

<a name="debug-test-locally"></a>

## <a name="debug-and-test-your-logic-app"></a>ロジック アプリをデバッグしてテストする

1. ステートレスなロジック アプリ ワークフローを簡単にデバッグできるように、[そのワークフローの実行履歴を有効にできます](#run-history)。

1. Visual Studio Code ツールバーで **[実行]** メニューを開き、 **[デバッグを開始する]** (F5) を選択します。

   **[ターミナル]** ウィンドウが開き、デバッグ セッションを確認できます。

1. 次に、Request トリガーでエンドポイントのコールバック URL を検索します。

   1. エクスプローラー ウィンドウを再度開き、プロジェクトを表示できるようにします。

   1. **workflow.json** ファイルのショートカット メニューから、 **[概要]** を選択します。

      ![エクスプローラー ウィンドウが表示され、workflow.json ファイルのショートカット ウィンドウで [概要] が選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. **[コールバック URL]** の値を探します。この値は、Request トリガーの例として次の URL のようになります。

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![ワークフローの [概要] ページに [コールバック URL] が表示されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. ロジック アプリ ワークフローをトリガーすることによってコールバック URL をテストするには、[Postman](https://www.postman.com/downloads/) を開くか、要求を作成および送信するための任意のツールを開きます。

   この例では、Postman を使用して続行します。 詳細については、[Postman の概要](https://learning.postman.com/docs/getting-started/introduction/)に関するページを参照してください。

   1. Postman のツールバーで、 **[新規]** を選択します。

      ![[新規] ボタンが選択された Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **[新規作成]** ウィンドウの **[構成要素]** で、 **[要求]** を選択します。

   1. **[要求の保存]** ウィンドウの **[要求名]** で、要求の名前を指定します。たとえば、`Test workflow trigger` のように指定します。

   1. **[保存先のコレクションまたはフォルダーの選択]** で、 **[コレクションの作成]** を選択します。

   1. **[すべてのコレクション]** で、要求を整理するために作成するコレクションの名前を指定して Enter キーを押し、 **[<*コレクション名*> に保存]** を選択します。 この例では、`Logic Apps requests` というコレクション名を使用しています。

      Postman の要求ウィンドウが開き、Request トリガーのコールバック URL に要求を送信できるようになります。

      ![要求ウィンドウを開いた Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code に戻ります。 ワークフローの [概要] ページから、 **[コールバック URL]** プロパティ値をコピーします。

   1. Postman に戻ります。 [要求] ウィンドウで、既定の要求メソッドとして **[GET]** が表示されているメソッドの一覧の横にあるアドレス ボックスに、先ほどコピーしたコールバック URL を貼り付け、 **[送信]** を選択します。

      ![アドレス ボックスにコールバック URL が入力され、[送信] ボタンが選択されている Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      ロジック アプリ ワークフローの例では、次の例のような電子メールが送信されます。

      ![例に記載されている Outlook の電子メールを示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code で、ワークフローの [概要] ページに戻ります。

   ステートフルなワークフローを作成した場合、送信した要求がワークフローをトリガーすると、[概要] ページにワークフローの実行の状態と履歴が表示されます。 実行の状態の詳細については、「[実行履歴を確認する](../logic-apps/monitor-logic-apps.md#review-runs-history)」を参照してください。

   ![ワークフローの [概要] ページに実行の状態と履歴が表示されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > 実行の状態が表示されない場合は、 **[更新]** を選択して [概要] ページを更新してみてください。

1. 特定の実行の各ステップの状態とそのステップの入出力を確認するには、その実行の省略記号 ( **...** ) ボタンを選択し、 **[実行の表示]** を選択します。

   ![ワークフローの実行履歴行に省略記号ボタンが表示され、[実行の表示] が選択されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code には、各アクションの実行の状態が表示されます。

1. 各ステップの入出力を確認するには、検査するステップを展開します。 そのステップの未加工の入出力をさらに確認するには、 **[未加工入力の表示]** または **[未加工出力の表示]** を選択します。

   ![ワークフロー内の各ステップの状態に加えて、展開された [メールの送信] アクションの入出力を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. デバッグ セッションを停止するには、 **[実行]** メニューで、 **[デバッグの停止]** (Shift + F5) を選択します。

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>呼び出し元に応答を返す

ロジック アプリに要求を送信した呼び出し元に応答を返すには、Request トリガーを開始したワークフローに対して、組み込みの[応答アクション](../connectors/connectors-native-reqres.md)を使用できます。

1. ロジック アプリ デザイナーで、 **[メールの送信]** アクションの下に表示される **[新しいステップ]** を選択します。

   デザイナーに **[操作の選択]** プロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ウィンドウが再び表示されます。

1. **[アクションの追加]** ウィンドウで、 **[アクションの選択]** 検索ボックスの下にある **[組み込み]** が選択されていることを確認します。 検索ボックスに「`response`」と入力し、 **[応答]** アクションを選択します。

   ![[応答] アクションが選択されたロジック アプリ デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   **[応答]** アクションがデザイナーに表示されると、アクションの詳細ウィンドウが自動的に開きます。

   ![[応答] アクションの詳細ウィンドウが開き、[本文] プロパティが [メールの送信] アクションの [本文] プロパティ値に設定されているロジック アプリ デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **[パラメーター]** タブで、呼び出す関数に必要な情報を指定します。

   この例では、 **[メールの送信]** アクションから出力された **[本文]** プロパティ値が返されます。

   1. **[本文]** プロパティ ボックス内をクリックすると、動的コンテンツ リストが表示され、先行するトリガーとワークフロー内のアクションから使用可能な出力値が表示されます。

      ![動的コンテンツ リストが表示されるように、[本文] プロパティ内にマウス ポインターを置いた [応答] アクションの詳細ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 動的コンテンツ リストの **[メールの送信]** から **[本文]** を選択します。

      ![開いた動的コンテンツ リストを示すスクリーンショット。 リストの [メールの送信] ヘッダーの下で、[本文] 出力値が選択されています。](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      完了すると、[応答] アクションの **[本文]** プロパティが、 **[メールの送信]** アクションの **[本文]** 出力値に設定されます。

      ![ワークフロー内の各ステップの状態に加えて、展開された [応答] アクションの入出力を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. デザイナーで、 **[保存]** を選択します。

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>ロジック アプリを再テストする

ロジック アプリを更新した後、Visual Studio でデバッガーを再実行し、更新したロジック アプリをトリガーする別の要求を送信することで、別のテストを実行できます。この手順は[ロジック アプリのデバッグとテスト](#debug-test-locally)と同様です。

1. Visual Studio Code ツールバーで **[実行]** メニューを開き、 **[デバッグを開始する]** (F5) を選択します。

1. Postman またはツールで要求を作成して送信するには、別の要求を送信してワークフローをトリガーします。

1. ステートフルなワークフローを作成した場合は、ワークフローの [概要] ページで最新の実行の状態を確認します。 その実行の各ステップの状態、入力、および出力を表示するには、その実行の省略記号 ( **...** ) ボタンを選択し、 **[実行の表示]** を選択します。

   例として、サンプル ワークフローが [応答] アクションを使用して更新された後の実行のステップ バイ ステップの状態を次に示します。

   ![更新されたワークフロー内の各ステップの状態に加えて、展開された [応答] アクションの入出力を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. デバッグ セッションを停止するには、 **[実行]** メニューで、 **[デバッグの停止]** (Shift + F5) を選択します。

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Azure に発行する

Visual Studio Code では、プロジェクトを Azure に直接デプロイすることができます。これにより、新しい **[ロジック アプリ (プレビュー)]** リソースの種類を使用してロジック アプリが発行されます。 Azure Functions の関数アプリ リソースと同様に、この新しいリソースの種類のデプロイでは、デプロイ時に設定できる[ホスティング プランと価格レベル](../app-service/overview-hosting-plans.md)を選択する必要があります。 ホスティング プランと価格の詳細については、次のトピックを参照してください。

* [Azure App Service でスケールアップする](../app-service/manage-scale-up.md)
* [Azure Functions のスケールとホスティング](../azure-functions/functions-scale.md)

ロジック アプリは新しいリソースとして発行できます。これにより、[関数アプリの要件と同様に、Azure ストレージ アカウントなど](../azure-functions/storage-considerations.md)、必要な追加のリソースが自動的に作成されます。 または、以前にデプロイされた **[ロジック アプリ (プレビュー)]** リソースにロジック アプリを発行することもできます。これは、デプロイ プロセスによって Azure で上書きされます。

### <a name="publish-as-a-new-logic-app-preview-resource"></a>新しい [ロジック アプリ (プレビュー)] リソースとして発行する

1. Visual Studio Code ツール バーで、Azure アイコンを選択します。

1. **[Azure:Logic Apps (プレビュー)]** ウィンドウのツールバーで、 **[ロジック アプリにデプロイする]** を選択します。

   ![[Azure:Logic Apps (プレビュー)] ウィンドウと [ロジック アプリにデプロイする] が選択されたウィンドウのツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Visual Studio Code で開いたリストで、次のオプションから選択します。

   * **[Azure で新しいロジック アプリ (プレビュー) を作成する]** (クイック)
   * **[Azure Advanced で新しいロジック アプリ (プレビュー) を作成する]**
   * 以前にデプロイされた **[ロジック アプリ (プレビュー)]** リソース (存在する場合)

   この例では、 **[Azure Advanced で新しいロジック アプリ (プレビュー) を作成する]** を選択して続行します。

   ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[Azure で新しいロジック アプリ (プレビュー) を作成する] が選択されたリストを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 新しい **[ロジック アプリ (プレビュー)]** リソースを作成するには、次の手順を実行します。

   1. 新しいロジック アプリのグローバルに一意の名前を指定します。これは、 **[ロジック アプリ (プレビュー)]** リソースに使用する名前です。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、作成する新しいロジック アプリの名前を入力するプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 新しいロジック アプリのホスティング プランとして、[ **[App Service プラン]** ](../azure-functions/functions-scale.md#app-service-plan) または [ **[Premium]** ](../azure-functions/functions-scale.md#premium-plan) を選択します。 この例では、 **[App Service プラン]** を選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[App Service プラン] または [Premium] を選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 新しい App Service プランを作成するか、既存のプランを選択します。 この例では、 **[新しい App Service プランを作成する]** を選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[新しい App Service プランを作成する] または既存の App Service プランを選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service プランの名前を入力して、プランの[価格レベル](../app-service/overview-hosting-plans.md)を選択します。 この例では、 **[F1 Free]** プランを選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、価格レベルを選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 最適なパフォーマンスを得るには、デプロイ用のプロジェクトと同じリソース グループを見つけて選択します。

      > [!NOTE]
      > 別のリソース グループを作成したり、使用したりすることはできますが、パフォーマンスに影響を与える可能性があります。 別のリソース グループを作成または選択した場合、確認プロンプトが表示された後にキャンセルすると、デプロイも取り消されます。

   1. ステートフルなワークフローの場合は、 **[新しいストレージ アカウントを作成する]** または既存のストレージ アカウントを選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、ストレージ アカウントを作成または選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. 診断ログとトレース機能を簡単にするために、既存の Application Insights リソースを選択することができます。 それ以外の場合は、 **[Application Insights リソースの新規作成]** を選択するか、アプリをデプロイした後に Azure portal で Application Insights を設定することができます。

      デプロイする前に、プロジェクトのルート レベルに存在する **host.json** ファイルで `logging` オブジェクトに `logLevel` オブジェクトを追加し、`Host.Triggers.Workflow` を `Information` に設定します。たとえば、次のようにします。

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      **host.json** ファイルは次のようになります。

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   完了すると、ロジック アプリを発行するために必要なリソースの作成とデプロイが Visual Studio Code で開始されます。

1. デプロイ プロセスを確認および監視するには、 **[表示]** メニューで **[出力]** を選択します。 [出力] ウィンドウのツールバーの一覧で、 **[Azure Logic Apps]** を選択します。

   ![ツールバーの一覧で [Azure Logic Apps] が選択された [出力] ウィンドウと、デプロイの進行状況と状態を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code で Azure へのロジック アプリ ワークフローのデプロイが完了すると、次のメッセージが表示されます。

   ![Azure へのデプロイが正常に完了したというメッセージを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   これで、ロジック アプリが Azure で稼働し、既定で有効になりました。

次に、デプロイされたロジック アプリを [Azure portal](#find-manage-deployed-workflows-portal) または [Visual Studio Code](#find-manage-deployed-workflows-vs-code) で検索する方法をご覧ください。

### <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>デプロイされた [ロジック アプリ (プレビュー)] リソースの監視を有効にする

デプロイされた **[ロジック アプリ (プレビュー)]** リソースで実行履歴と監視を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、デプロイされた **[ロジック アプリ (プレビュー)]** リソースを見つけて選択します。

1. そのリソースのメニューで、 **[API]** の下にある **[CORS]** を選択します。

1. **[CORS]** ウィンドウの **[許可されたオリジン]** で、ワイルドカード文字 (*) を追加します。

1. 操作が完了したら、 **[CORS]** のツールバーで、 **[保存]** を選択します。

   ![Azure portal とデプロイされた [ロジック アプリ (プレビュー)] リソースを示すスクリーンショット。 リソース メニューで [CORS] が選択され、[許可されたオリジン] の新しいエントリがワイルドカード文字 (*) に設定されています。](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Visual Studio Code でデプロイされたロジック アプリを検索して管理する

Visual Studio Code では、リソースの種類が元の **[ロジック アプリ]** であるか **[ロジック アプリ (プレビュー)]** であるかに関係なく、Azure サブスクリプションでデプロイされたすべてのロジック アプリを表示できます。また、これらのロジック アプリの管理に役立つタスクを選択できます。 ただし、両方のリソースの種類にアクセスするには、Visual Studio Code 用の **Azure Logic Apps** と **Azure Logic Apps (プレビュー)** の両方の拡張機能が必要です。

1. 左側のツールバーで、Azure アイコンを選択します。 **[Azure: Logic Apps (プレビュー)]** ウィンドウで、サブスクリプションを展開します。これによって、そのサブスクリプションにデプロイされているすべてのロジック アプリが表示されます。

1. 管理するロジック アプリを検索して選びます。 ロジック アプリのショートカット メニューを開き、実行するタスクを選択します。

   たとえば、デプロイしたロジック アプリの停止、開始、再起動、削除などのタスクを選択できます。

   ![Visual Studio Code と、開いている "Azure Logic Apps (プレビュー)" 拡張機能ウィンドウ、デプロイされたワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. デプロイされたロジック アプリを Azure portal で開くには、Visual Studio Code でロジック アプリのショートカット メニューを開き、 **[ポータルで開く]** を選択します。

   ブラウザーで Azure portal が開き、Visual Studio Code にサインインしている場合はポータルに自動的にサインインし、ロジック アプリが表示されます。

   ![Visual Studio Code でのロジック アプリのための Azure portal ページを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Azure portal に個別にサインインし、ポータルの検索ボックスを使用してロジック アプリを検索し、結果の一覧からロジック アプリを選択することもできます。

   ![Azure portal と、デプロイされているロジック アプリの検索結果が表示された検索バーを示すスクリーンショット。選択された状態で表示されます。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>ポータルでデプロイされたロジック アプリを検索して管理する

Azure portal では、リソースの種類が元の **[ロジック アプリ]** であるか **[ロジック アプリ (プレビュー)]** であるかに関係なく、Azure サブスクリプションでデプロイされたすべてのロジック アプリを表示できます。 現時点では、各リソースの種類は Azure で個別のカテゴリとして編成および管理されています。

> [!NOTE]
> パブリック プレビューでは、デプロイされた **[ロジック アプリ (プレビュー)]** リソースを Azure portal で表示することのみ可能です。新しい **[ロジック アプリ (プレビュー)]** リソースを作成することはできません。 これらのロジック アプリは Visual Studio Code でのみ作成できます。 ただし、このリソースの種類を使用して、デプロイされたロジック アプリに[ワークフローを追加する](#add-workflows)ことができます。

**[ロジック アプリ (プレビュー)]** リソースの種類を持つロジック アプリを検索するには、次の手順を実行します。

1. Azure portal の検索ボックスに、「`logic app preview`」と入力します。 結果の一覧が表示されたら、 **[サービス]** で、 **[ロジック アプリ (プレビュー)]** を選択します。

   ![検索テキスト "logic app preview" が入力された Azure portal の検索ボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **[ロジック アプリ (プレビュー)]** ウィンドウで、Visual Studio Code からデプロイしたロジック アプリを探して選択します。

   ![Azure portal と、Azure でデプロイされた [ロジック アプリ (プレビュー)] リソースを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure portal で、選択したロジック アプリの個別のリソース ページが開きます。

   ![Azure portal でのロジック アプリ ワークフローのリソース ページを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. このロジック アプリのワークフローを表示するには、ロジック アプリのメニューで **[ワークフロー]** を選択します。

   **[ワークフロー]** ウィンドウには、現在のロジック アプリのすべてのワークフローが表示されます。 この例では、Visual Studio Code で作成したワークフローを示しています。

   ![[ワークフロー] ウィンドウが開き、デプロイされたワークフローを含む [ロジック アプリ (プレビュー)] リソース ページを示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. ワークフローを表示するには、 **[ワークフロー]** ウィンドウで、そのワークフローを選択します。

   [ワークフロー] ウィンドウが開き、詳細情報とそのワークフローに対して実行できるタスクが表示されます。

   たとえば、ワークフローのステップを表示するには、 **[デザイナー]** を選択します。

   ![選択されたワークフローの [概要] ウィンドウを示すスクリーンショット。[ワークフロー] メニューには、選択した [デザイナー] コマンドが表示されています。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   ロジック アプリ デザイナーが開き、Visual Studio Code で作成したワークフローが表示されます。 これで、Azure portal でこのワークフローに変更を加えることができます。

   ![Visual Studio Code からデプロイされたロジック アプリ デザイナーとワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>デプロイされたロジック アプリにワークフローを追加する

Azure portal を使用すると、Visual Studio Code からデプロイした **[ロジック アプリ (プレビュー)]** リソースに空のワークフローを追加し、それらのワークフローを Azure portal でビルドできます。

1. [Azure portal](https://portal.azure.com) で、デプロイされた **[ロジック アプリ (プレビュー)]** リソースを見つけて選択します。

1. ロジック アプリのメニューで、 **[ワークフロー]** を選択します。 **[ワークフロー]** ウィンドウで、 **[追加]** を選択します。

   ![選択されたロジック アプリの [ワークフロー] ウィンドウと、[追加] コマンドが選択されたツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **[新しいワークフロー]** ウィンドウで、ワークフローの名前を指定します。 **[ステートフル]** または **[ステートレス]** **>** **[作成]** の順に選択します。

   Azure で新しいワークフローがデプロイされた後、 **[ワークフロー]** ウィンドウに表示されます。このワークフローを選択して、ロジック アプリ デザイナーやコード ビューを開くなど、管理やその他のタスクを実行できます。

   ![選択されたワークフローを管理およびレビューのオプションと共に示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   たとえば、新しいワークフローのデザイナーを開くと、空のキャンバスが表示されます。 このワークフローを Azure portal でビルドできるようになりました。

   ![ロジック アプリ デザイナーと空のワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Docker コンテナーへの展開

[.NET Core コマンド ライン インターフェイス (CLI) ツール](/dotnet/core/tools/)を使用すると、プロジェクトをビルドしてから、ビルドを発行できます。 次に、ロジック アプリ ワークフローのデプロイ先として [Docker コンテナー](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container)を作成して使用できます。 詳細については、次のトピックをご覧ください。

* [コンテナーと Docker の概要](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET および Docker の概要](/dotnet/core/docker/introduction)
* [Docker に関する用語](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [チュートリアル: Docker の概要](/visualstudio/docker/tutorials/docker-tutorial)

1. プロジェクトをビルドするには、コマンド ライン プロンプトを開き、次のコマンドを実行します。

   `dotnet build -c release`

   詳細については、[dotnet build](/dotnet/core/tools/dotnet-build/) のリファレンス ページを参照してください。

1. 次のコマンドを実行してビルドを発行します。

   `dotnet publish -c release`

   詳細については、[dotnet publish](/dotnet/core/tools/dotnet-publish/) のリファレンス ページを参照してください。

1. .NET ワークフロー用の Docker ファイルを使用し、次のコマンドを実行して、Docker コンテナーをビルドします。

   `docker build --tag local/workflowcontainer .`

   例として、.NET ワークフロー用のサンプル Docker ファイルを次に示します。ただし、<*storage-account-connection-string*> の値は、前に保存した Azure ストレージ アカウントの接続文字列に置き換えてください。次の例のようになります。

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   詳細については、[docker build](https://docs.docker.com/engine/reference/commandline/build/) に関するページを参照してください。

1. 次のコマンドを使用して、コンテナーをローカルで実行します。

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   詳細については、[docker run](https://docs.docker.com/engine/reference/commandline/run/) に関するページを参照してください。

1. Request トリガーのコールバック URL を取得するには、次の要求を送信します。

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   <*master-key*> の値は、**azure-webjobs-secrets/{deployment-name}/host.json** ファイルで `AzureWebJobsStorage` に対して設定した Azure ストレージ アカウントで定義されています。この値は次のセクションで確認できます。

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>ステートレスなロジック アプリ ワークフローの実行履歴

ステートレスなロジック アプリ ワークフローを簡単にデバッグできるようにするには、そのワークフローの実行履歴を有効にし、完了後に実行履歴を無効にします。

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Visual Studio Code でのステートレスなロジック アプリ ワークフローの場合

Visual Studio Code でステートレスなロジック アプリ ワークフローをローカルで実行している場合は、次の手順に従います。

1. プロジェクトで、**workflow-designtime** フォルダーを見つけて展開します。 **local.settings.json** ファイルを見つけて開きます。

1. `Workflow.<yourWorkflowName>.operationOptions` プロパティを追加し、値を `WithStatelessRunHistory` に設定します。次に例を示します。

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflow.<yourWorkflowName>.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. 完了時に実行履歴を無効にするには、`Workflow.<yourWorkflowName>.OperationOptions` プロパティとその値を削除するか、プロパティを `None` に設定します。

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Azure portal でのステートレスなロジック アプリ ワークフローの場合

既にプロジェクトを Azure portal にデプロイしている場合は、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、ご利用の **[ロジック アプリ (プレビュー)]** リソースを検索して開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[構成]** を選択します。

1. **[アプリケーションの設定]** タブで、 **[新しいアプリケーション設定]** を選択します。

1. **[アプリケーション設定の追加/編集]** ウィンドウで、 **[名前]** ボックスに次の操作オプションの名前を入力します。 

   `Workflow.<yourWorkflowName>.OperationOptions`

1. **[値]** ボックスに、「`WithStatelessRunHistory`」と入力します。

   次に例を示します。

   ![Azure portal と [ロジック アプリ (プレビュー)] リソースを示すスクリーンショット。[構成] > [新しいアプリケーション設定] < [アプリケーション設定の追加/編集] ウィンドウが開き、"Workflow.<yourWorkflowName>OperationOptions" というオプションが "WithStatelessRunHistory" に設定されています。](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. 終了したら、 **[OK]** を選択します。 **[構成]** ウィンドウで、 **[保存]** を選択します。

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>ステートフルなロジック アプリとステートレスなロジック アプリの入れ子になった動作の違い

[Request](../connectors/connectors-native-reqres.md) トリガー、[HTTP Webhook](../connectors/connectors-native-webhook.md) トリガー、または [ApiConnectionWehook 型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)であり HTTPS 要求を受信できるマネージド コネクタ トリガーを使用することによって、[ロジック アプリ ワークフローを他のロジック アプリ ワークフローから呼び出すことができるように](../logic-apps/logic-apps-http-endpoint.md)できます。

親ワークフローが子ワークフローを呼び出した後、入れ子になったロジック アプリ ワークフローが実行できる動作パターンを次に示します。

* 非同期ポーリング パターン

  親は最初の呼び出しに対する応答を待機せず、子が実行を終了するまで、子の実行履歴を継続的にチェックします。 既定では、ステートフルなワークフローはこのパターンに従います。これは、[要求タイムアウト制限](../logic-apps/logic-apps-limits-and-config.md)を超える可能性がある、長時間実行される子ワークフローに適しています。

* 同期パターン ("ファイア アンド フォーゲット")

  子は `202 ACCEPTED` 応答を直ちに返すことによって呼び出しを確認し、親は子の結果を待たずに次のアクションに進みます。 代わりに、子の実行が終了すると、親は結果を受信します。 応答アクションを含まない子のステートフルなワークフローは、常に同期パターンに従います。 子ワークフローがステートフルである場合は、実行履歴を確認することができます。

  この動作を有効にするには、ワークフローの JSON 定義で、`OperationOptions` プロパティを `DisableAsyncPattern` に設定します。 詳細については、[トリガーとアクションの種類 (操作オプション)](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) に関するページを参照してください。

* トリガーと待機

  子ワークフローがステートレスである場合、親は子からの結果を返す応答を待機します。 このパターンは、組み込みの [HTTP トリガーまたはアクション](../connectors/connectors-native-http.md)を使用して子ワークフローを呼び出す場合と同様に機能します。 応答アクションを含まないステートレスな子ワークフローは直ちに `202 ACCEPTED` 応答を返しますが、親は次のアクションに進む前に子が終了するまで待機します。 これらの動作は、ステートレスな子ワークフローにのみ適用されます。

次の表では、親と子がステートフル、ステートレス、または混合のどのワークフロー型であるかに基づいて、子ワークフローの動作を指定します。

| 親ワークフロー | 子ワークフロー | 子の動作 |
|-----------------|----------------|----------------|
| ステートレス | ステートレス | `operationOptions=DisableSynPattern` 設定を使用した非同期または同期 |
| ステートレス | ステートレス | トリガーと待機 |
| ステートレス | ステートレス | Synchronous |
| ステートレス | ステートレス | トリガーと待機 |
||||

## <a name="limits"></a>制限

このリソースの種類では、[Azure Logic Apps に対する既存の制限](../logic-apps/logic-apps-limits-and-config.md)の多くが同じですが、このパブリック プレビュー拡張機能の違いは次のとおりです。

* マネージド コネクタ:接続ごとに、1 分あたり 50 の要求

* [JavaScript アクションのインライン コード アクション](../logic-apps/logic-apps-add-run-inline-code.md)では、次の制限が変更されました。

  * コード文字の制限は、1,024 文字から 10 万文字に増加しています。

  * コードの実行時間の制限は、5 秒から 15 秒に増加しています。

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>利用不可またはサポートされていない機能

このパブリック プレビューでは、次の機能は使用できないか、サポートされていません。

* 現時点では、新しい **[ロジック アプリ (プレビュー)]** リソースの作成は Mac OS では使用できません。

* このプレビューでは、カスタム コネクタ、Webhook ベースのトリガー、およびスライディング ウィンドウ トリガーはサポートされていません。

* ステートレスなロジック アプリ ワークフローの場合は、トリガーではなく[マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)に対してのみアクションを追加できます。 ワークフローを開始するには、[組み込みの Request トリガー、Event Hubs トリガー、または Service Bus トリガー](../connectors/apis-list.md#built-ins)を使用します。

* Azure portal では、新しい **[ロジック アプリ (プレビュー)]** リソースの種類を使用して新しいロジック アプリを作成することはできません。 これらのロジック アプリは Visual Studio Code でのみ作成できます。 ただし、このリソースの種類のロジック アプリを Visual Studio Code から Azure にデプロイした後、[これらのロジック アプリに新しいワークフローを追加することができます](#add-workflows)。

* ロジック アプリのデプロイでは、**従量課金**ホスティング プランはサポートされていません。

## <a name="next-steps"></a>次のステップ

このパブリック プレビュー拡張機能を使用したお客様からのご意見をお待ちしております。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。