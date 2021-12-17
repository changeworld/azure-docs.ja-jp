---
title: Standard ロジック アプリ ワークフローの SQL ストレージを設定する
description: シングルテナント Azure Logic Apps でロジック アプリ (Standard) ワークフローの成果物、状態、実行履歴を格納する SQL データベースを設定します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5dd6f2d024055a75e350bfb5b0eee0e26c115193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475808"
---
# <a name="set-up-sql-database-storage-for-standard-logic-apps-in-single-tenant-azure-logic-apps-preview"></a>シングルテナント Azure Logic Apps で Standard ロジック アプリの SQL データベース ストレージを設定する (プレビュー)

> [!IMPORTANT]
> この機能はプレビュー段階にあり、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されます。

シングルテナント Azure Logic Apps、App Service Environment v3、または Azure の外部で実行するワークフローを作成するために **ロジック アプリ (Standard)** リソースの種類を選ぶ場合、ワークフロー関連の成果物、状態、ランタイム データを保存するための Azure Storage アカウントも作成する必要があります。 ただし、ロジック アプリのワークフローのランタイム環境、スループット、スケーリング、パフォーマンス、管理をより柔軟に制御する必要がある場合は、ワークフロー関連のストレージ トランザクションに Azure Storage ではなく、SQL ストレージ プロバイダーを使用できます。

この記事では、Azure Storage の代替手段として SQL ストレージを Azure Logic Apps のプライマリ ストレージ プロバイダーとして使用する理由の概要を示し、Azure portal でのロジック アプリの作成時または Visual Studio Code からのロジック アプリのデプロイ時にストレージ使用のために SQL を設定する方法について説明します。

ロジック アプリを初めて使用する場合は、次のドキュメントを参照してください。

- [Azure Logic Apps とは](logic-apps-overview.md)
- [Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)

<a name="why-sql"></a>

## <a name="why-use-sql"></a>SQL を使用する理由

シングルテナント Azure Logic Apps で使用できる代替のストレージ オプションとして、SQL には次のような利点があります。

| 特長 | 説明 |
|---------|-------------|
| **移植性** | SQL には、仮想マシン、サービスとしてのプラットフォーム (PaaS)、コンテナーなど、さまざまなフォーム ファクターがあります。 ロジック アプリ ワークフローを実行する可能性のあるほとんどすべての場所で SQL データベースを実行できます。 |
| **制御** | SQL では、特定の期間または特定のワークロードについて、データベースのスループット、パフォーマンス、スケーリングをきめ細かく制御できます。 SQL の料金は CPU 使用率とスループットに基づいています。これにより、各操作に基づいてコストがかかる Azure Storage よりも料金が予測しやすくなります。 |
| **既存の資産の利用** | Microsoft のツールを使い慣れている場合は、SQL との最新の統合にその資産を使用できます。 Azure ハイブリッド特典を使用すると、従来のオンプレミス デプロイと最新のクラウド実装全体で資産を再利用できます。 また SQL には、SQL Server Management Studio (SSMS)、コマンド ライン インターフェイス、SDK など、成熟した、十分なサポートを受けられるツールも用意されています。 |
| **コンプライアンス** | SQL には、冗長性を持つバックアップ、復元、フェールオーバー、ビルドのためのオプションが Azure Storage よりも多く用意されています。 ロジック アプリのストレージには、他のエンタープライズ アプリケーションと同じエンタープライズ レベルのメカニズムを適用できます。 |
|||

<a name="when-use-sql"></a>

## <a name="when-to-use-sql"></a>どのような場合に SQL を使用するか

次の表では、SQL の使用を検討する必要があるいくつかの理由について説明します。

| シナリオ | ストレージ プロバイダーの推奨 |
|----------|----------------------------|
| Azure でロジック アプリのワークフローを実行する際に、ストレージのスループットとパフォーマンスをより細かく制御する必要がある。 | Azure Storage にはスループットとパフォーマンスを微調整するためのツールが用意されていないため、ストレージ プロバイダーとして SQL を使用します。 |
| オンプレミスでロジック アプリのワークフローを実行する必要がある。これは、[Azure Arc 対応 Logic Apps](azure-arc-enabled-logic-apps-overview.md)で実行できます。 | SQL をストレージ プロバイダーとして使用して、SQL データベースをホストする場所 (オンプレミス、仮想マシン、コンテナー、マルチクラウドなど) を選択できるようにします。 統合するシステムの近くでロジック アプリ ワークフローを実行するか、クラウドへの依存を減らすことを検討してください。   |
| ストレージ コストを予測できる必要がある。 | スケーリング コストをより細かく制御する場合は、ストレージ プロバイダーとして SQL を使用します。 SQL のコストは、1 秒あたりの計算と入力出力の各操作 (IOPS) に基づいています。 Azure Storage のコストは操作の数に基づいており、ゼロにスケーリングされる小規模なワークロードに適している場合があります。 |
| Azure Storage ではなく SQL を使用したい。 | SQL は、よく知られた信頼性の高いエコシステムであり、ロジック アプリの運用全体に同じガバナンスと管理を適用するために使用できます。 |
| 既存の SQL 環境を再利用する必要がある。 | 再利用する、またはクラウドで最新化する SQL ライセンスを既に所有している場合は、ストレージ プロバイダーとして SQL を使用します。 また、ロジック アプリの統合に Azure ハイブリッド特典を使用することもできます。 |
| その他すべて | 既定のストレージ プロバイダーとして Azure Storage を使用します。 |
|||

## <a name="prerequisites"></a>前提条件

- Azure アカウントとアクティブなサブスクリプション。 お持ちでない場合は、 [無料アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- ロジック アプリで使用する SQL 環境。 ただし、環境をセットアップする前に、次の手順を実行してください。

  1. SQL Server インスタンスを作成する。

     サポートされる種類には、 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)、[Azure SQL データベース](https://azure.microsoft.com/products/azure-sql/database/)、 [Azure SQL Managed Instance](https://azure.microsoft.com/products/azure-sql/managed-instance/) などがあります。

     - サポートされている種類のいずれかを使用して SQL サーバーが Azure でホストされている場合は、次のアクセス許可を設定する必要があります。

       1. [Azure portal](https://portal.azure.com) で、使用する SQL サーバー リソースに移動します。

       1. リソースのナビゲーション メニューの **[セキュリティ]** で、 **[ファイアウォールと仮想ネットワーク]** を選択します。

       1. 開いたウィンドウの **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** で **[はい]** を選択します

       1. 変更を保存します。

     - SQL サーバーが Azure でホストされていない場合は、サーバー上のファイアウォールまたはネットワークの設定で、Azure のサービスとリソースがサーバーとデータベースにアクセスできるようになっていることを確認します。

     - ローカル開発に SQL Express を使用している場合は、既定の名前付きインスタンス  `localhost\SQLExpress` に接続します。

  1. 既存のデータベースを作成または使用します。

     SQL ストレージ プロバイダーを設定するには、使用可能なデータベースが必要です。

  1. ここで、この記事の手順に従って [SQL 環境をセットアップ](#set-up-sql-environment)できます。

- ローカル開発の場合、ローカル コンピューターにインストールされている [Visual Studio Code](https://code.visualstudio.com/Download)。

  > [!NOTE]
  > 必ず Microsoft インストーラー (MSI) バージョン (`func-cli-X.X.XXXX-x*.msi`) を使用して [Azure Functions Core Tools の最新バージョン](https://github.com/Azure/azure-functions-core-tools/releases)をインストールし、SQL がサポートされるようにします。 Visual Studio Code のインストール要件の詳細については、「[Visual Studio Code でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-visual-studio-code.md)」をご覧ください。

<a name="set-up-sql-environment"></a>

## <a name="set-up-your-sql-environment"></a>SQL 環境をセットアップする

1. SQL ストレージ プロバイダーを設定する前に、「[前提条件](#prerequisites)」で必要な手順を完了してください。

1. SQL サーバーのアクセス許可を設定します。

   現時点では、SQL ストレージ プロバイダーは、接続文字列での SQL 認証をサポートしています。 ローカルの開発とテストには Windows 認証を使用することもできます。 現時点では、Azure Active Directory (Azure AD) とマネージド ID のサポートは使用できません。

   対象の SQL データベースでワークフロー関連の成果物を作成および管理するアクセス許可を持つ ID を使用する必要があります。 たとえば、管理者は、これらの成果物を作成して管理するために必要なすべてのアクセス許可を持っています。 次に、指定した SQL 接続文字列を使用して、シングルテナント Azure Logic Apps ランタイムが作成しようとする成果物の一覧を示します。 SQL 接続文字列で使用されている ID に、次の成果物を作成するために必要なアクセス許可があることを確認します。

   - スキーマ  `dt`、 `dc`、 `dq` の作成および削除。
   - これらのスキーマのテーブルの追加、変更、削除。
   - これらのスキーマのユーザー定義テーブル型の追加、変更、削除。

   対象のアクセス許可の詳細については、[データベース エンジンでの SQL サーバーのアクセス許可](/sql/relational-databases/security/permissions-database-engine)に関する記事を確認してください。

1. SQL に接続する

   - SQL データベースで、開発に必要なアクセスが許可されていることを確認します。

   - Azure SQL データベースを使用している場合は、次の要件を満たす必要があります。

     - ローカルの開発とテストの場合は、ローカル コンピューターの IP アドレスからの接続を明示的に許可します。  [Azure SQL Server で IP ファイアウォール規則を設定](../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules)できます。

     - [Azure portal](https://portal.azure.com)で、 [Azure サービスを許可する](../azure-sql/database/network-access-controls-overview.md#allow-azure-services)ことで、指定された接続文字列でロジック アプリ リソースが SQL データベースにアクセスできるようにします。

     - シナリオに応じて、他の  [SQL データベースのネットワーク アクセス制御](../azure-sql/database/network-access-controls-overview.md) を設定します。

   - Azure SQL Managed Instance を使用している場合は、Azure サービス (`logicapp`) が[セキュリティで保護されたパブリック エンドポイントを介して SQL データベースに接続](../azure-sql/managed-instance/public-endpoint-overview.md)できるようにします。

<a name="set-up-sql-logic-app-creation-azure-portal"></a>

## <a name="set-up-sql-during-creation-in-the-azure-portal"></a>Azure portal で作成中に SQL を設定する

Azure で **ロジック アプリ (Standard)** リソースの種類を使用してロジック アプリを作成する場合は、ストレージ プロバイダーとして SQL を設定できます。

1. [Azure portal](https://portal.azure.com) で、Azure アカウントを使ってサインインします。

1. Azure portal の検索ボックスに「`logic apps`」と入力し、 **[ロジック アプリ]** を選択します。

   ![Azure portal の検索ボックスに "ロジック アプリ" という検索用語が入力され、[ロジック アプリ (Standard)] カテゴリが選択されているスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/find-logic-app-resource-template.png)

1. **[ロジック アプリ]** ページで **[追加]** を選択します。

1. **[ロジック アプリの作成]** ページの **[基本]** タブで、ロジック アプリ リソースに関する次の情報を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | ロジック アプリに使用する Azure サブスクリプション。 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | ロジック アプリと関連リソースを作成する Azure リソース グループ。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows-RG` という名前のリソース グループを作成します。 |
   | **Type** | はい | **Standard** | このロジック アプリ リソースの種類は、シングルテナント Azure Logic Apps 環境で実行され、[Standard 使用、課金、価格モデル](logic-apps-pricing.md#standard-pricing)を使用します。 |
   | **ロジック アプリ名** | はい | <*ロジック アプリ名*> | ロジック アプリに使用する名前。 このリソース名は、リージョン間で一意である必要があり、文字、数字、ハイフン ( **-** )、アンダースコア ( **_** )、かっこ ( **()** )、ピリオド ( **.** ) のみを含めることができます。 <p><p>この例では、`Fabrikam-Workflows` という名前のロジック アプリを作成します。 <p><p>**注**: ロジック アプリの名前には自動的にサフィックス `.azurewebsites.net` が付けられます。これは、**ロジック アプリ (Standard)** リソースは、Azure Functions の機能拡張モデルを使用し、Azure Functions ランタイムの拡張機能としてホストされているシングルテナント Azure Logic Apps ランタイムで実行されるためです。 Azure Functions では、同じアプリの名前付け規則が使用されます。 |
   | **発行** | はい | <*デプロイ環境*> | ロジック アプリのデプロイ先。 既定では、シングル テナント Azure Logic Apps にデプロイするための **ワークフロー** が選択されています。 Azure により、最初のワークフローを追加する必要がある空のロジック アプリ リソースが作成されます。 <p><p>**注**: 現時点では、**Docker コンテナー** オプションを使用するには Azure Arc enabled Kubernetes クラスター上に [*カスタムの場所*](../azure-arc/kubernetes/conceptual-custom-locations.md) が必要です。これは、[Azure Arc enabled Logic Apps (プレビュー)](azure-arc-enabled-logic-apps-overview.md) で使用できます。 ロジック アプリのリソースの場所、カスタムの場所、クラスターはすべて同じである必要があります。 |
   | **[リージョン]** | はい | <*Azure-region*> | リソース グループやリソースを作成する際に使用する場所です。 この例では、サンプル ロジック アプリを Azure にデプロイし、**米国西部** を使用します。 <p>- **[Docker コンテナー]** を選択した場合は、カスタムの場所を選択します。 <p>- あらかじめ存在している必要がある [ASEv3](../app-service/environment/overview.md) リソースにデプロイするには、**[リージョン]** の一覧からその環境リソースを選択します。 |
   |||||

   次の例は、 **[ロジック アプリの作成]** ページの **[基本]** タブを示しています。

   ![Azure portal と [ロジック アプリの作成] ページの [基本] タブが示されているスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-logic-app-resource-portal.png)

1. 準備ができたら、 **[次へ: ホスティング]** を選択します。 **[ホスティング]** タブで、ロジック アプリに使用するストレージ ソリューションとホスティング プランに関する情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **ストレージの種類** | はい | **SQL と Azure Storage** | ワークフロー関連の成果物およびデータに使用するストレージの種類。 <p><p>- 以前にリージョンとしてカスタムの場所を選択した場合は、 **[SQL]** を選択します。 <p><p>-以前に Azure リージョンまたは ASEv3 の場所を選択した場合は、 **[SQL と Azure Storage]** を選択します。 <p><p>**注**: Azure リージョンにデプロイする場合は、引き続き Azure Storage アカウントが必要です。これは、Azure Logic Apps プラットフォームでロジック アプリの構成の 1 回限りのホスティングを完了するために使用されます。 ワークフローの定義、状態、実行履歴、およびその他のランタイム成果物は、SQL データベースに格納されます。 <p><p>Azure Arc クラスターでホストされているカスタムの場所へのデプロイでは、ストレージ プロバイダーとして SQL のみが必要です。 |
   | **ストレージ アカウント** | はい | <*Azure-storage-account-name*> | ストレージ トランザクションに使用する [Azure ストレージ アカウント](../storage/common/storage-account-overview.md)。 <p><p>このリソース名は、リージョン間で一意であり、数字と小文字のみを含む 3 から 24 文字である必要があります。 既存のアカウントを選択するか、新しいアカウントを作成します。 <p><p>この例では、`fabrikamstorageacct` という名前のストレージ アカウントを作成します。 |
   | **SQL 接続文字列** | はい | <*sql-connection-string*> | SQL の接続文字列。現時点では、SQL 認証のみがサポートされ、OAuth およびマネージド ID 認証はサポートされていません。 <p><p>**注**: Azure portal ではこの文字列が検証されないため、正しい接続文字列を入力してください。 |
   | **[プランの種類]** | はい | <*hosting-plan*> | ロジック アプリのデプロイに使用するホスティング プラン。 <p><p>詳細については、「[ホスティング プランと価格レベル](logic-apps-pricing.md#standard-pricing)」を参照してください。 |
   | **Windows プラン** | はい | <*プラン名*> | 使用するプラン名。 既存のプラン名を選択するか、新しいプランの名前を指定します。 <p><p>この例では、 `Fabrikam-Service-Plan`という名前を使用しています。 |
   | **SKU とサイズ** | はい | <*価格レベル*> | ロジック アプリに使用する[価格レベル](../app-service/overview-hosting-plans.md)。 選択した内容は、ロジック アプリとワークフローで使用する料金、コンピューティング、メモリ、およびストレージに影響します。 <p><p>既定の価格レベルを変更するには、 **[サイズの変更]** を選択します。 その後、必要なワークロードに基づいて、他の価格レベルを選択できます。 <p><p>詳細については、「[ホスティング プランと価格レベル](logic-apps-pricing.md#standard-pricing)」を参照してください。 |
   |||||

   次の例は、 **[ロジック アプリの作成]** ページの **[ホスティング]** タブを示しています。

   ![Azure portal と [ロジック アプリの作成] ページの [ホスティング] タブが示されているスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/set-up-sql-storage-details.png)

1. 「[Azure portal でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)」の残りの作成手順を完了します。

完了すると、新しいロジック アプリのリソースとワークフローが Azure で有効になり、SQL データベースがストレージ プロバイダーとして使用されます。

<a name="set-up-sql-local-dev"></a>

## <a name="set-up-sql-for-local-development-in-visual-studio-code"></a>Visual Studio Code でローカル開発用に SQL を設定する

次の手順では、Visual Studio Code でローカルの開発とテストを行うためのストレージ プロバイダーとして SQL を設定する方法を示します。

1. シングルテナント Azure Logic Apps で動作するように開発環境を設定します。

   1. Azure Logic Apps (Standard) 拡張機能がインストールされた Visual Studio Code で作業するための[前提条件](create-single-tenant-workflows-visual-studio-code.md#prerequisites)を満たします。

   1. Azure Logic Apps (Standard) 拡張機能と連携するように [Visual Studio Code を設定](create-single-tenant-workflows-visual-studio-code.md#set-up)します。

   1. Visual Studio Code で、[Azure アカウントに接続](create-single-tenant-workflows-visual-studio-code.md#connect-azure-account)し、[空のロジック アプリ プロジェクトを作成](create-single-tenant-workflows-visual-studio-code.md#create-project)します。

1. Visual Studio Code で、[エクスプローラー] ウィンドウを開きます (まだ開いていない場合)。

1. [エクスプローラー] ウィンドウのロジック アプリ プロジェクトのルートで、すべてのファイルとフォルダーの下にある空白の領域の上にマウス ポインターを移動し、ショートカット メニューを開いて、 **[Use SQL storage for your Logic App project]\(ロジック アプリ プロジェクトに SQL ストレージを使用する\)** を選択します。

   ![Visual Studio Code の [エクスプローラー] ウィンドウでマウス ポインターがプロジェクト ルートの空白領域にあり、ショートカット メニューが開き、[Use SQL storage for your Logic App project]\(ロジック アプリ プロジェクトに SQL ストレージを使用する\) が選択されているスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/use-sql-storage-logic-app-project.png)

1. プロンプトが表示されたら、SQL 接続文字列を入力します。 ローカル SQL Express インスタンスまたはその他の SQL データベースを使用することを選択できます。

   ![Visual Studio Code と SQL 接続文字列のプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

   確認後、Visual Studio Code によりプロジェクトの **local.settings.json** ファイルに次の設定が作成されます。 この設定はいつでも更新できます。  

   ![Visual Studio Code、ロジック アプリ プロジェクト、"local.settings.json" ファイルの SQL 接続文字列設定を示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/local-settings-json-file.png)

<a name="set-up-sql-logic-app-deployment-visual-studio-code"></a>

## <a name="set-up-sql-during-deployment-from-visual-studio-code"></a>Visual Studio Code からのデプロイ中に SQL を設定する

Visual Studio Code から Azure にロジック アプリ プロジェクトを直接発行することができます。 この操作により、 **ロジック アプリ (Standard)**   リソースの種類を使用して、ロジック アプリ プロジェクトがデプロイされます。

- Azure で新しい **ロジック アプリ (Standard)** リソースとしてプロジェクトを発行し、ストレージ プロバイダーとして SQL を使用する場合は、アプリを発行するときに SQL 接続文字列を入力します。 完全な手順については、[新しいロジック アプリのデプロイのための SQL の設定](#deploy-new-logic-app-visual-studio-code)に関する記事を参照してください。

- SQL の設定が既に完了している場合は、Azure に既にデプロイされている **ロジック アプリ (Standard)** リソースにロジック アプリ プロジェクトを発行できます。 このアクションにより、既存のロジック アプリが上書きされます。

  > [!NOTE]
  > ローカルの SQL Express は、Azure にデプロイおよびホストされているロジック アプリでは機能しません。

<a name="deploy-new-logic-app-visual-studio-code"></a>

### <a name="set-up-sql-for-new-logic-app-standard-resource-deployment"></a>新しいロジック アプリ (Standard) リソースのデプロイ用に SQL を設定する

1. Visual Studio Code のアクティビティ バーで、Azure アイコンを選択します。

1.  **[Azure: ロジック アプリ (Standard)]**   ウィンドウのツールバーで、 **[Deploy to Logic App]\(ロジック アプリにデプロイする\)** を選択します。

   ![[Azure: ロジック アプリ (Standard)] ウィンドウで [Deploy to Logic App]\(ロジック アプリにデプロイする\) アイコンが選択されていることを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/deploy-to-logic-app.png)

1. プロンプトが表示される場合は、ロジック アプリのデプロイに使用する Azure サブスクリプションを選択します。

1. Visual Studio Code で開いた一覧から、 **[Create new Logic App (Standard) in Azure Advanced]\(Azure Advanced で新しいロジック アプリ (Standard) を作成する\)** の詳細オプションを選択してください。 それ以外の場合、SQL 設定するダイアログは表示されません。

   ![[Create new Logic App (Standard) in Azure Advanced]\(Azure Advanced で新しいロジック アプリ (Standard) を作成する\) が選択されているデプロイ オプションを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-create-logic-app-advanced.png)

1. ダイアログが表示されたら、新しいロジック アプリのグローバルに一意の名前を指定します。これは、 **ロジック アプリ (Standard)**   リソースに使用する名前です。 この例では、 `Fabrikam-Workflows-App` を使用します。

   ![ロジック アプリに使用するグローバルに一意の名前を求めるプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-logic-app-name.png)

1. ロジック アプリの場所を選びます。 入力を開始して一覧をフィルター処理することもできます。

   - Azure にデプロイするには、デプロイ先の Azure リージョンを選びます。 以前に App Service Environment v3 (ASEv3) リソースを作成済みで、そこにデプロイする場合は、ASEv3 を選びます。

   - Azure Arc 対応 Logic Apps にデプロイするには、以前に構成したカスタムの場所を選びます。

   次の例は、**米国西部** にフィルター処理された場所の一覧を示しています。

   ![使用可能な Azure リージョンと Azure Arc デプロイ用のカスタムの場所を含むデプロイの場所を選択するプロンプトを示スクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-location.png)

1. 新しいロジック アプリのホスティング プランの種類を選びます。

   1. アプリの場所として ASEv3 を選んだ場合は、 **[App Service プラン]** を選び、使用する ASEv3 リソースを選びます。 そうでない場合は、 **[ワークフロー Standard]** を選びます。

      ![[ワークフロー Standard] または [App Service プラン] を選ぶプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-hosting-plan.png)

   1. プランの名前を作成するか、既存のプランを選択します。

      この例では、使用できる既存のプランがないため、 **[新しい App Service プランの作成]** を選びます。

      ![[新しい App Service プランの作成] が選択された、ホスティング プランの名前を作成するプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-app-service-plan.png)

1. ホスティング プランの名前を入力して、選択したプランの価格レベルを選択します。

   詳細については、「[ホスティング プランと価格レベル](logic-apps-pricing.md#standard-pricing)」を参照してください。

1. Azure リソース グループのダイアログが表示されたら、最適なパフォーマンスを得るために、プロジェクトと同じ Azure リソース グループをデプロイ用に選びます。

   > [!NOTE]
   > 別のリソース グループを作成したり、使用したりすることはできますが、パフォーマンスに影響を与える可能性があります。 別のリソース グループを作成または選択した場合、確認プロンプトが表示された後にキャンセルすると、デプロイも取り消されます。

1. ロジック アプリのストレージ アカウントを選ぶダイアログが表示されたら、次のいずれかのオプションを選択します。

   - 前にカスタムの場所を選択した場合は、 **[SQL]** オプションを選びます。

   - Azure にデプロイする場合は、 **[SQL と Azure Storage]** オプションを選びます。

     > [!NOTE]
     > このオプションは、Azure デプロイでのみ必要です。 Azure では、Azure Logic Apps プラットフォームでロジック アプリの構成の 1 回限りのホスティングを完了するために、Azure Storage が必要です。 進行中のワークフローの状態、実行履歴、およびその他のランタイム成果物は、SQL データベースに格納されます。
     >
     > Azure Arc クラスターでホストされているカスタムの場所へのデプロイでは、ストレージ プロバイダーとして SQL のみが必要です。  

1. ダイアログが表示されたら、 **[新しいストレージ アカウントを作成する]** または既存のストレージ アカウント (使用可能な場合) を選びます。

   ![[Azure:Logic Apps (Standard)] ウィンドウと、ストレージ アカウントを作成または選択するように求めるプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-storage.png)

1. SQL ストレージの確認プロンプトで **[はい]** を選びます。 接続文字列のプロンプトで、SQL 接続文字列を入力します。

   > [!NOTE]
   > Visual Studio Code ではこの文字列が検証されないため、正しい接続文字列を入力してください。

   ![Visual Studio Code と SQL 接続文字列のプロンプトを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

1. 「[新しいロジック アプリ (Standard) リソースに発行する](create-single-tenant-workflows-visual-studio-code.md#publish-new-logic-app)」の残りのデプロイ手順を完了します。

完了すると、新しいロジック アプリのリソースとワークフローが Azure で有効になり、SQL データベースがストレージ プロバイダーとして使用されます。

## <a name="validate-deployments"></a>デプロイの検証

**ロジック アプリ (Standard)** リソースを Azure にデプロイした後、設定が正しいかどうかを確認できます。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ リソースを開きます。

1. リソースのナビゲーション メニューで、 **[設定]** の下にある **[構成]** を選びます。

1. **[構成]** ウィンドウの **[アプリケーション設定]** で、**Workflows.Sql.ConnectionString** アプリ設定を探し、SQL 接続文字列が表示されており、かつ正しいことを確認します。

1. SQL 環境で、スキーマ名が 'dt' と 'dq' で始まる SQL テーブルが作成されていることを確認します。

たとえば、次のスクリーンショットは、単一のワークフローを持つロジック アプリ リソースに対してシングルテナント Azure Logic Apps ランタイムにより作成されたテーブルを示しています。

![シングルテナント Azure Logic Apps ランタイムによって作成された SQL テーブルを示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-tables-sql.png)

シングルテナント Azure Logic Apps サービスでは、ユーザー定義テーブル型も作成されます。 たとえば、次のスクリーンショットは、単一のワークフローを持つロジック アプリ リソースに対してシングルテナント Azure Logic Apps ランタイムにより作成されたユーザー定義テーブル型を示しています。

![シングルテナント Azure Logic Apps ランタイムによって作成された SQL ユーザー定義テーブル型を示すスクリーンショット。](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-user-defined-tables-sql.png)

## <a name="next-steps"></a>次のステップ

- [Visual Studio Code でシングルテナント Azure Logic Apps (Standard) を使用して統合ワークフローを作成する](create-single-tenant-workflows-visual-studio-code.md)
- [シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)
