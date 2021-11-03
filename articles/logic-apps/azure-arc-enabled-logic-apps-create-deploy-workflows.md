---
title: Azure Arc 対応 Logic Apps を使用してワークフローを作成してデプロイする
description: Kubernetes が実行できる任意の場所で実行されるシングルテナント ベ」ースのロジック アプリ ワークフローを作成してデプロイします。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, reylons, archidda, sopai, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ca9458581468ea359ae1ca2f7e034c7459f87fea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082438"
---
# <a name="create-and-deploy-single-tenant-based-logic-app-workflows-with-azure-arc-enabled-logic-apps-preview"></a>Azure Arc 対応 Logic Apps を使用してシングルテナント ベースのロジック アプリ ワークフローを作成してデプロイする (プレビュー)

> [!NOTE]
> この機能はプレビュー段階にあり、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されます。

Azure Arc 対応 Logic Apps と Azure portal を使用すると、運用および管理する Kubernetes インフラストラクチャに対して、シングルテナント ベースのロジック アプリ ワークフローを作成してデプロイできます。 ロジック アプリは、Azure App Service プラットフォーム拡張機能バンドルをインストールして有効にした Azure Arc 対応 Kubernetes クラスターにマップされた "*カスタムの場所*" で実行されます。

たとえば、このクラスターは、Azure Kubernetes Service、ベアメタル Kubernetes、または別のセットアップにすることができます。 拡張機能バンドルを使用すると、Kubernetes クラスターで Azure Logic Apps、Azure App Service、Azure Functions などのプラットフォーム サービスを実行できます。 

詳細については、次のドキュメントを確認してください。

- [Azure Arc 対応 Logic Apps とは](azure-arc-enabled-logic-apps-overview.md)
- [シングルテナントとマルチテナント、および統合サービス環境](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc の概要](../azure-arc/overview.md)
- [Azure Kubernetes Service の概要](../aks/intro-kubernetes.md)
- [Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)
- [Azure Arc 対応 Kubernetes 上のカスタムの場所](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc の App Service、Functions、および Logic Apps (プレビュー)](../app-service/overview-arc-integration.md)
- [Azure Arc 対応の Kubernetes クラスターを設定して、App Service、Functions、Logic Apps を実行します (プレビュー)](../app-service/manage-create-arc-environment.md)

## <a name="prerequisites"></a>前提条件

このセクションでは、ロジック アプリ ワークフローの作成とデプロイに使用できるすべてのアプローチとツールに共通する前提条件について説明します。 ツール固有の前提条件が、対応する手順と共に表示されます。

- アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- Azure Arc 対応 Kubernetes クラスターと、Azure Logic Apps、Azure App Service、Azure Functions をホストして実行できる "*カスタムの場所*" を備えた Kubernetes 環境。

  > [!IMPORTANT]
  > Kubernetes 環境、カスタムの場所、ロジック アプリには、同じリソースの場所を使用するようにしてください。

  Kubernetes クラスターで App Service バンドル拡張機能を作成するときに、ロジック アプリ ワークフローを実行するために[既定のスケーリング動作を変更](#change-scaling)することもできます。 Azure CLI コマンド [ **`az k8s-extension create`**](/cli/azure/k8s-extension) を使用して拡張機能を作成する場合は、構成設定 `keda.enabled=true` を必ず含めてください。

  `az k8s-extension create {other-command-options} --configuration-settings "keda.enabled=true"`

  詳細については、次のドキュメントを確認してください。

  - [Azure Arc の App Service、Functions、および Logic Apps (プレビュー)](../app-service/overview-arc-integration.md)
  - [Azure Arc 対応 Kubernetes のクラスター拡張機能](../azure-arc/kubernetes/conceptual-extensions.md)
  - [Azure Arc 対応の Kubernetes クラスターを設定して、App Service、Functions、Logic Apps を実行します (プレビュー)](../app-service/manage-create-arc-environment.md)
  - [既定のスケーリング動作の変更](#change-scaling)

- 独自の Azure Active Directory (Azure AD) ID

  ワークフローで Office 365 Outlook や Azure Storage など、Azure でホストされる接続を使用する必要がある場合は、ロジック アプリでの認証に Azure AD ID を使用する必要があります。 Azure Arc 対応 Logic Apps は任意のインフラストラクチャで実行できますが、Azure でホストされる接続を使用するためのアクセス許可を持つ ID が必要です。 この ID を設定するには、ロジック アプリが必要な ID として使用するアプリの登録を Azure AD で作成します。

  > [!NOTE]
  > マネージド ID のサポートは、Azure Arc 対応 Logic Apps では現在使用できません。

  Azure CLI を使用して Azure Active Directory (Azure AD) アプリの登録を作成するには、こちらの手順を実行します。

  1. [`az ad sp create`](/cli/azure/ad/sp#az_ad_sp_create) コマンドを使用して、アプリの登録を作成します。

  1. すべての詳細を確認するには、[`az ad sp show`](/cli/azure/ad/sp#az_ad_sp_show) コマンドを実行します。

  1. 両方のコマンドの出力から、クライアント ID、オブジェクト ID、テナント ID、およびクライアント シークレットの値を見つけて保存します。これらは、後で使用するために保持しておく必要があります。

  Azure portal を使用して Azure Active Directory (Azure AD) アプリの登録を作成するには、こちらの手順を実行します。

  1. [Azure portal](../active-directory/develop/quickstart-register-app.md) を使用して、新しい Azure AD アプリの登録を作成します。

  1. 作成が完了したら、ポータルで新しいアプリの登録を見つけます。

  1. 登録メニューで、 **[概要]** を選択し、クライアント ID、テナント ID、およびクライアント シークレットの値を保存します。

  1. オブジェクト ID を検索するには、 **[ローカル ディレクトリでのマネージド アプリケーション]** フィールドの横にあるアプリ登録の名前を選択します。 プロパティ ビューからオブジェクト ID をコピーします。

## <a name="create-and-deploy-logic-apps"></a>ロジック アプリを作成してデプロイする

Azure CLI、Visual Studio Code、Azure portal のどれを使用するかに基づいて、一致するタブを選択して、特定の前提条件と手順を確認します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

始める前に、次の項目が必要になります。

- ローカル コンピューターにインストールされた最新の Azure CLI 拡張機能。

  - この拡張機能がない場合は、[ご使用のオペレーティング システムまたはプラットフォームのインストール ガイド](/cli/azure/install-azure-cli)を確認してください。

  - 最新バージョンかどうかが不明な場合は、[環境と CLI のバージョンを確認する手順](#check-environment-cli-version)に従ってください。

- "*プレビュー*" 版の Azure Logic Apps (Standard) の Azure CLI 用拡張機能。

  Azure Logic Apps は一般提供されていますが、シングルテナント Azure Logic Apps の拡張機能はまだプレビュー段階です。

- ロジック アプリを作成する [Azure リソース グループ](#create-resource-group)。

  このリソース グループがない場合は、[リソース グループを作成する手順](#create-resource-group)に従ってください。

- データと実行履歴の保持のためにロジック アプリで使用する Azure ストレージ アカウント。

  このストレージ アカウントを持っていない場合は、ロジック アプリの作成時にこのアカウントを作成するか、[手順に従ってストレージ アカウント](/cli/azure/storage/account#az_storage_account_create)を作成することができます。

<a name="check-environment-cli-version"></a>

#### <a name="check-environment-and-cli-version"></a>環境と CLI バージョンを確認する

1. Azure portal にサインインします。 次のコマンドを実行して、サブスクリプションがアクティブであることを確認します。

   ```azurecli-interactive
   az login
   ```

1. 次のコマンドを実行して、ターミナルまたはコマンド ウィンドウで Azure CLI のバージョンを確認します。

   ```azurecli-interactive
   az --version
   ```

   最新バージョンについては、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。

1. 最新バージョンを使用していない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新します。

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Azure Logic Apps (Standard) の Azure CLI 用拡張機能をインストールする

"*プレビュー*" 版のシングルテナント Azure Logic Apps (Standard) の Azure CLI 用拡張機能をインストールするには、次のコマンドを実行します。

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>リソース グループの作成

ロジック アプリのリソース グループがまだない場合は、コマンド `az group create` を実行してグループを作成します。 Azure アカウントの既定のサブスクリプションをまだ設定していない場合は、`--subscription` パラメーターでサブスクリプション名または識別子を使用してください。 そうでない場合は、`--subscription` パラメーターを使用する必要はありません。

> [!TIP]
> 既定のサブスクリプションを設定するには、次のコマンドを実行し、`MySubscription` をご自分のサブスクリプション名または識別子に置き換える必要があります。
>
> `az account set --subscription MySubscription`

たとえば、次のコマンドは `MyResourceGroupName` という名前のリソース グループを、`MySubscription` という名前の Azure サブスクリプションを使用して `eastus` の場所に作成します。

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

リソース グループが正常に作成されると、出力に `provisioningState` が `Succeeded` として示されます。

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

#### <a name="create-logic-app"></a>ロジック アプリを作成する

Azure Arc 対応ロジック アプリを作成するには、次の必須パラメーターを指定してコマンド `az logicapp create` を実行します。 ロジック アプリのリソースの場所、カスタムの場所、Kubernetes 環境はすべて同じである必要があります。

| パラメーター | 説明 |
|------------|-------------|
| `--name -n` | ロジック アプリの一意の名前。 |
| `--resource-group -g` | ロジック アプリの作成先となる[リソース グループ](../azure-resource-manager/management/manage-resource-groups-cli.md)の名前。 使用するリソース グループがない場合は、[リソース グループを作成](#create-resource-group)してください。 |
| `--storage-account -s` | ロジック アプリで使用する[ストレージ アカウント](/cli/azure/storage/account)。 同じリソース グループ内のストレージ アカウントの場合は、文字列値を使用します。 別のリソース グループ内のストレージ アカウントの場合は、リソース ID を使用します。 |
|||

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation
```

プライベート Azure Container Registry (ACR) イメージを使用して Azure Arc 対応ロジック アプリを作成するには、次の必須パラメーターを指定してコマンド `az logicapp create` を実行します。

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   --deployment-container-image-name myacr.azurecr.io/myimage:tag
   --docker-registry-server-password MyPassword 
   --docker-registry-server-user MyUsername
```

#### <a name="show-logic-app-details"></a>ロジック アプリの詳細を表示する

Azure Arc 対応ロジック アプリの詳細を表示するには、次の必須パラメーターを指定してコマンド `az logicapp show` を実行します。

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="deploy-logic-app"></a>ロジック アプリをデプロイする

[Azure App Service の Kudu zip のデプロイ](../app-service/resources-kudu.md)を使用して Azure Arc 対応ロジック アプリをデプロイするには、次の必須パラメーターを指定してコマンド `az logicapp deployment source config-zip` を実行します。

> [!IMPORTANT]
> zip ファイルにプロジェクトの成果物がルート レベルで含まれていることを確認します。 これらの成果物には、すべてのワークフロー フォルダー、host.json、connections.js などの構成ファイル、および他の関連ファイルが含まれます。 余分なフォルダーを追加したり、プロジェクト構造にまだ存在しないフォルダーに成果物を含めたりしないでください。 たとえば次の一覧は MyBuildArtifacts.zip ファイル構造の例を示しています。
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

#### <a name="start-logic-app"></a>ロジック アプリを起動する

Azure Arc 対応ロジック アプリを起動するには、次の必須パラメーターを指定してコマンド `az logicapp start` を実行します。

```azurecli
az logicapp start --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="stop-logic-app"></a>ロジック アプリを停止する

Azure Arc 対応ロジック アプリを停止するには、次の必須パラメーターを指定してコマンド `az logicapp stop` を実行します。

```azurecli
az logicapp stop --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="restart-logic-app"></a>ロジック アプリを再起動する

Azure Arc 対応ロジック アプリを再起動するには、次の必須パラメーターを指定してコマンド `az logicapp restart` を実行します。

```azurecli
az logicapp restart --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

#### <a name="delete-logic-app"></a>ロジック アプリを削除する

Azure Arc 対応ロジック アプリを削除するには、次の必須パラメーターを指定してコマンド `az logicapp delete` を実行します。

```azurecli
az logicapp delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code で、ロジック アプリ ワークフローをエンドツーエンドで作成、デプロイ、および監視できます。 シングルテナントの Azure Logic Apps と Azure Arc 対応 Logic Apps との間で、実行されるロジック アプリ ワークフロー開発のデザイナー エクスペリエンスに変更や違いはありません。

1. ロジック アプリ プロジェクトを作成するには、[Visual Studio Code を使用したシングルテナントのAzure Logic Apps での統合ワークフローの作成](create-single-tenant-workflows-visual-studio-code.md)に関するドキュメントに記載されている前提条件と手順に従います。

1. Azure にデプロイする準備ができたら、 **[ロジック アプリにデプロイする]** エクスペリエンスを使用して、以前に作成したカスタムの場所にロジック アプリのリソースを作成し、同じ場所にワークフローをデプロイします。

   1. ロジック アプリ プロジェクトのウィンドウの空白の領域からショートカット メニューを開き、 **[ロジック アプリにデプロイする]** を選択します。

   1. カスタムの場所に関連付けられている Azure サブスクリプションを選択します。

   1. 新しい Azure Arc 対応 Logic Apps リソースを作成するには、 **[Create new Logic App in Azure (Advanced)]\(Azure で新しいロジック アプリを作成する (詳細)\)** を選択します。 または、一覧から既存のロジック アプリ リソースを選択し、次の手順をスキップすることもできます。

   1. グローバルに一意の名前をロジック アプリに指定します。

   1. デプロイ先の Azure Arc 対応 Kubernetes 環境のカスタムの場所を選択します。 代わりに汎用 Azure リージョンを選択すると、シングルテナントの Azure Logic Apps で実行される Azure Arc 非対応のロジック アプリ リソースが作成されます。

   1. ロジック アプリをデプロイする新しいリソース グループを選択または作成します。

   1. ロジック アプリの状態とメタデータを保存するための新しいストレージ アカウントを選択または作成します。

   1. ロジック アプリのアプリケーション ログを格納するための新しい Application Insights リソースを選択または作成します。

   1. まだ行っていない場合は、ロジック アプリでマネージド API 接続を認証できるように、Azure Active Directory (Azure AD) ID を設定します。 詳細については、最重要の[前提条件](#prerequisites)を参照してください。

   1. Azure AD ID のクライアント ID、テナント ID、オブジェクト ID、およびクライアント シークレットを入力します。

      > [!NOTE]
      > この手順を実行する必要があるのは 1 回だけです。 Visual Studio Code によって、プロジェクトの connections.json ファイルとマネージド API 接続のアクセス ポリシーが更新されます。

1. 完了すると、ロジック アプリがアクティブになり、Azure Arc 対応 Kubernetes クラスターで実行され、テストの準備が整います。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

ポータルベースのデザイナーの編集機能は、現在、Azure Arc 対応 Logic Apps 向けに開発中です。 ポータル ベースのデザイナーを使用してロジック アプリを作成、デプロイ、表示することはできますが、デプロイ後にポータルで編集することはできません。 現時点では、Visual Studio Code でローカルにロジック アプリ プロジェクトを作成および編集してから、Visual Studio Code、Azure CLI、または自動デプロイを使用してデプロイできます。

1. Azure portal で [**ロジック アプリ (Standard)** リソースを作成します](create-single-tenant-workflows-azure-portal.md)。 ただし、 **[発行]** の宛先には **[Docker コンテナー]** を選択します。 **[リージョン]** には、アプリの場所として先ほど作成したカスタムの場所を選択します。

   既定では、**Logic App (Standard)** リソースはシングルテナントの Azure Logic Apps で実行されます。 しかし、Azure Arc 対応 Logic Apps の場合、ロジック アプリ リソースは Kubernetes 環境用に作成したカスタムの場所で実行されます。 また、App Service プランは自動的に作成されるため、作成する必要はありません。

   > [!IMPORTANT]
   > ロジック アプリのリソースの場所、カスタムの場所、Kubernetes 環境はすべて同じである必要があります。

1. [Visual Studio Code を使用し、ロジック アプリを編集してデプロイします](create-single-tenant-workflows-visual-studio-code.md)。

1. ロジック アプリをビルドしてデプロイすると、ポータルまたは Application Insights を使用して、通常どおりワークフローを監視および表示できます。

   デプロイされたロジック アプリのポータル エクスペリエンスは現在、読み取り専用モードで使用できます。つまり、ワークフローやアプリの設定は変更できません。 ただし、実行履歴、トリガー履歴、アプリに関するその他の情報は引き続き表示できます。 現時点では、Azure CLI、Visual Studio Code、自動デプロイのいずれかを使用してロジック アプリを更新できます。

---

## <a name="set-up-connection-authentication"></a>接続の認証を設定する

現在、Azure Arc 対応 Kubernetes クラスターでは、ロジック アプリのマネージド ID を使用したマネージド API 接続の認証はサポートされていません。 ワークフローでマネージド コネクタを使用する場合は、これらの Azure がホストするマネージド接続を作成します。

代わりに、Azure Active Directory (Azure AD) で独自のアプリの登録を作成する必要があります。 その後、このアプリの登録を、Azure Arc 対応 Logic Apps でデプロイおよび実行されているロジック アプリの ID として使用できます。 詳細については、[最重要の前提条件](#prerequisites)を確認してください。

アプリの登録から、クライアント ID、オブジェクト ID、テナント ID、およびクライアント シークレットが必要です。 Visual Studio Code を使用してデプロイする場合は、Azure AD ID を使用してロジック アプリを設定するための組み込みのエクスペリエンスがあります。 詳細については、[ロジック アプリ ワークフローを作成してデプロイする - Visual Studio Code](#create-and-deploy-logic-apps) に関するセクションを参照してください。

ただし、開発には Visual Studio Code を使用するが、Azure CLI または自動パイプラインを使用してデプロイする場合は、こちらの手順に従います。

### <a name="configure-connection-and-app-settings-in-your-project"></a>プロジェクトで接続とアプリの設定を構成する

1. ロジック アプリ プロジェクトの **connections.json** ファイルで、マネージド接続の `authentication` オブジェクトを探します。 このオブジェクトの内容を、[最重要の前提条件](#prerequisites)で以前に生成した、ご自分のアプリの登録情報に置き換えます。

   ```json
   "authentication": {
      "type": "ActiveDirectoryOAuth",
      "audience": "https://management.core.windows.net/",
      "credentialType": "Secret",
      "clientId": "@appsetting('WORKFLOWAPP_AAD_CLIENTID')",
      "tenant": "@appsetting('WORKFLOWAPP_AAD_TENANTID')",
      "secret": "@appsetting('WORKFLOWAPP_AAD_CLIENTSECRET')"
   } 
   ```

1. ロジック アプリ プロジェクトの **local.settings.json** ファイルに、クライアント ID、オブジェクト ID、テナント ID、およびクライアント シークレットを追加します。 デプロイ後、これらの設定はロジック アプリの設定になります。

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         <...>
         "WORKFLOWAPP_AAD_CLIENTID":"<my-client-ID>",
         "WORKFLOWAPP_AAD_OBJECTID":"<my-object-ID",
         "WORKFLOWAPP_AAD_TENANTID":"<my-tenant-ID>",
         "WORKFLOWAPP_AAD_CLIENTSECRET":"<my-client-secret>"
      }
   }
   ```

> [!IMPORTANT]
> 運用シナリオまたは環境では、[キー コンテナー](../app-service/app-service-key-vault-references.md)を使用するなどして、このようなシークレットや機密情報をセキュリティで保護するようにしてください。

### <a name="add-access-policies"></a>アクセス ポリシーを追加する

シングルテナントの Azure Logic Apps では、ロジック アプリごとに ID があります。これには、Azure でホストされているマネージド接続を使用するためのアクセス許可がアクセス ポリシーによって付与されています。 これらのアクセス ポリシーは、Azure portal またはインフラストラクチャ デプロイを使用して設定できます。

#### <a name="arm-template"></a>ARM テンプレート

Azure Resource Manager テンプレート (ARM テンプレート) には、マネージド API 接続 "*ごと*" に次のリソース定義を含め、次の情報を指定します。

| パラメーター | 説明 |
|-----------|-------------|
| <*connection-name*> | マネージド API 接続の名前 (例: `office365`) |
| <*object-ID*> | アプリの登録から以前に保存した Azure AD ID のオブジェクト ID |
| <*tenant-ID*> | アプリの登録から以前に保存した Azure AD ID のテナント ID |
|||

```json
{
   "type": "Microsoft.Web/connections/accessPolicies",
   "apiVersion": "2016-06-01",
   "name": "[concat('<connection-name>'),'/','<object-ID>')]",
   "location": "<location>",
   "dependsOn": [
      "[resourceId('Microsoft.Web/connections', parameters('connection_name'))]"
   ],
   "properties": {
      "principal": {
         "type": "ActiveDirectory",
         "identity": {
            "objectId": "<object-ID>",
            "tenantId": "<tenant-ID>"
         }
      }
   }
}
```

詳細については、[Microsoft.Web/connections/accesspolicies (ARM テンプレート)](/azure/templates/microsoft.web/connections?tabs=json) のドキュメントを参照してください。

#### <a name="azure-portal"></a>Azure portal

このタスクでは、以前に保存したクライアント ID を "*アプリケーション ID*" として使用します。

1. Azure Portal で、ご利用のロジック アプリを探して選択します。 ロジック アプリのメニューの **[ワークフロー]** で、 **[接続]** を選択します。これにより、ロジック アプリのワークフロー内のすべての接続が一覧表示されます。

1. **[API 接続]** で接続を選択します。この例では `office365` です。

1. 接続のメニューの **[設定]** で、 **[アクセス ポリシー]**  >  **[追加]** を選択します。

1. **[アクセス ポリシーの追加]** ペインの検索ボックスで、以前に保存したクライアント ID を検索して選択します。

1. 終了したら、 **[追加]** を選択します。

1. ロジック アプリで Azure でホストされる接続ごとに、これらの手順を繰り返します。

## <a name="automate-devops-deployment"></a>DevOps のデプロイを自動化する

Azure Arc 対応ロジック アプリをビルドしてデプロイするために、[シングルテナント ベースのロジック アプリ](single-tenant-overview-compare.md)と同じパイプラインとプロセスを使用できます。 DevOps 用のパイプラインを使用してインフラストラクチャのデプロイを自動化するには、コンテナー以外とコンテナーの両方のデプロイに対してインフラストラクチャ レベルで次の変更を行います。

### <a name="standard-deployment-non-container"></a>標準のデプロイ (コンテナー以外)

ロジック アプリのデプロイに zip デプロイを使用する場合は、コンテナー イメージをホストするために Docker レジストリを設定する必要はありません。 Kubernetes のロジック アプリは、厳密にはコンテナーで実行されますが、これらのコンテナーは Azure Arc 対応 Logic Apps によって自動的に管理されます。 このシナリオの場合は、インフラストラクチャを設定するときに次のタスクを実行します。

- Kubernetes でロジック アプリを作成しているリソース プロバイダーに通知します。
- デプロイに App Service プランを含めます。 詳細については、「[デプロイに App Service プランを含める](#include-app-service-plan)」を参照してください。

[Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) に、次の値を含めます。

| Item | JSON プロパティ | 説明 |
|------|---------------|-------------|
| 場所 | `location` | カスタムの場所および Kubernetes 環境と同じリソースの場所 (Azure リージョン) を必ず使用してください。 ロジック アプリのリソースの場所、カスタムの場所、Kubernetes 環境はすべて同じである必要があります。 <p><p>**注**: この値は、カスタムの場所の "*名前*" と同じではありません。 |
| アプリの種類 | `kind` | Azure プラットフォームでアプリを識別できるようにデプロイするアプリの種類。 Azure Logic Apps の場合、この情報は次の例のようになります: `kubernetes,functionapp,workflowapp,linux` |
| 拡張された場所 | `extendedLocation` | このオブジェクトでは、Kubernetes 環境に "*カスタムの場所*" の `"name"` が必要であるため、`"type"` が `"CustomLocation"` に設定されている必要があります。 |
| ホスティング プランのリソース ID | `serverFarmId` | 関連付けられている App Service プランのリソース ID。これは次のような形式になります。 <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| ストレージ接続文字列 | `AzureWebJobsStorage` | ストレージ アカウントの接続文字列 <p><p>**重要**: ARM テンプレートでは、自分のストレージ アカウントの接続文字列を指定する必要があります。 運用シナリオまたは環境では、キー コンテナーを使用するなどして、このようなシークレットや機密情報をセキュリティで保護するようにしてください。 |
||||

#### <a name="arm-template"></a>ARM テンプレート

次の例は、ARM テンプレートで使用できる Azure Arc 対応 Logic Apps リソース定義のサンプルを示しています。 詳細については、[Microsoft.Web/sites template format (JSON)](/azure/templates/microsoft.web/sites?tabs=json) のドキュメントを参照してください。

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": "kubernetes,functionapp,workflowapp,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "clientAffinityEnabled": false,
      "name": "[parameters('name')]",
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "Node|12"
      }
   }
}
```

### <a name="container-deployment"></a>コンテナーのデプロイ

コンテナー ツールとデプロイ プロセスを使用する場合は、ロジック アプリをコンテナー化し、Azure Arc 対応 Logic Apps にデプロイできます。 このシナリオの場合は、インフラストラクチャを設定するときに次の高度なタスクを実行します。

- コンテナー イメージをホストするための Docker レジストリを設定します。

- ロジック アプリをコンテナー化するには、たとえば、ロジック アプリ プロジェクトのルート フォルダーに次の Dockerfile を追加し、Docker レジストリにイメージをビルドして発行する手順に従います。「[チュートリアル: Azure Container Registry タスクを使用して、クラウドでコンテナー イメージをビルドしてデプロイする」](../container-registry/container-registry-tutorial-quick-task.md)を参照してください。

  > [!NOTE]
  > [ストレージ プロバイダーとして SQL を使用する](set-up-sql-db-storage-single-tenant-standard-workflows.md)場合は、Azure Functions イメージ バージョン 3.3.1 以降を使用してください。

  ```text
  FROM mcr.microsoft.com/azure-functions/node:3.3.1
  ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
  AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
  FUNCTIONS_V2_COMPATIBILITY_MODE=true
  COPY . /home/site/wwwroot
  RUN cd /home/site/wwwroot
  ```

- Kubernetes でロジック アプリを作成しているリソース プロバイダーに通知します。

- デプロイ テンプレートで、デプロイを計画している Docker レジストリとコンテナー イメージをポイントします。 シングルテナントの Azure Logic Apps は、この情報を使用して、Docker レジストリからコンテナー イメージを取得します。

- デプロイに App Service プランを含めます。 詳細については、「[デプロイに App Service プランを含める](#include-app-service-plan)」を参照してください。

[Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) に、次の値を含めます。

| Item | JSON プロパティ | 説明 |
|------|---------------|-------------|
| 場所 | `location` | カスタムの場所および Kubernetes 環境と同じリソースの場所 (Azure リージョン) を必ず使用してください。 ロジック アプリのリソースの場所、カスタムの場所、Kubernetes 環境はすべて同じである必要があります。 <p><p>**注**: この値は、カスタムの場所の "*名前*" と "*同じではありません*"。 |
| アプリの種類 | `kind` | Azure プラットフォームでアプリを識別できるようにデプロイするアプリの種類。 Azure Logic Apps の場合、この情報は次の例のようになります: `kubernetes,functionapp,workflowapp,container` |
| 拡張された場所 | `extendedLocation` | このオブジェクトでは、Kubernetes 環境に "*カスタムの場所*" の `"name"` が必要であるため、`"type"` が `"CustomLocation"` に設定されている必要があります。 |
| コンテナー名 | `linuxFxVersion` | コンテナーの名前。次のような形式になります。`DOCKER\|<container-name>` |
| ホスティング プランのリソース ID | `serverFarmId` | 関連付けられている App Service プランのリソース ID。これは次のような形式になります。 <p><p>`"/subscriptions/{subscriptionID}/resourceGroups/{groupName}/providers/Microsoft.Web/serverfarms/{appServicePlanName}"` |
| ストレージ接続文字列 | `AzureWebJobsStorage` | ストレージ アカウントの接続文字列 <p><p>**重要**: Docker コンテナーにデプロイする際に、ARM テンプレートで自分のストレージ アカウントの接続文字列を指定する必要があります。 運用シナリオまたは環境では、キー コンテナーを使用するなどして、このようなシークレットや機密情報をセキュリティで保護するようにしてください。 |
||||

Docker レジストリとコンテナー イメージを参照するには、こちらの値をテンプレートに含めます。

| Item | JSON プロパティ | 説明 |
|------|---------------|-------------|
| Docker レジストリ サーバー URL | `DOCKER_REGISTRY_SERVER_URL` | Docker レジストリ サーバーの URL |
| Docker レジストリ サーバー | `DOCKER_REGISTRY_SERVER_USERNAME` | Docker レジストリ サーバーにアクセスするユーザー名 |
| Docker レジストリ サーバーのパスワード | `DOCKER_REGISTRY_SERVER_PASSWORD` | Docker レジストリ サーバーにアクセスするためのパスワード |
||||

#### <a name="arm-template"></a>ARM テンプレート

次の例は、ARM テンプレートで使用できる Azure Arc 対応 Logic Apps リソース定義のサンプルを示しています。 詳細については、[Microsoft.Web/sites template format (ARM テンプレート)](/azure/templates/microsoft.web/sites?tabs=json) のドキュメントを参照してください。

```json
{
   "type": "Microsoft.Web/sites",
   "apiVersion": "2020-12-01",
   "name": "[parameters('name')]",
   "location": "[parameters('location')]",
   "kind": " kubernetes,functionapp,workflowapp,container",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
    },
   "properties": {
      "name": "[parameters('name')]",
      "clientAffinityEnabled": false,
      "serverFarmId": "<hosting-plan-ID>",
      "siteConfig": {
         "appSettings": [
            {
               "name": "FUNCTIONS_EXTENSION_VERSION",
               "value": "~3"
            },
            {
               "name": "FUNCTIONS_WORKER_RUNTIME",
               "value": "node"
            },
            {
               "name": "AzureWebJobsStorage",
               "value": "<storage-connection-string>"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__id",
               "value": "Microsoft.Azure.Functions.ExtensionBundle.Workflows"
            },
            {
               "name": "AzureFunctionsJobHost__extensionBundle__version",
               "value": "[1.*, 2.0.0)"
            },
            {
               "name": "APP_KIND",
               "value": "workflowapp"
            }, 
            {
               "name": "DOCKER_REGISTRY_SERVER_URL",
               "value": "<docker-registry-server-URL>"
            },
            { 
               "name": "DOCKER_REGISTRY_SERVER_USERNAME",
               "value": "<docker-registry-server-username>"
            },
            {
               "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
               "value": "<docker-registry-server-password>"
            }
         ],
         "use32BitWorkerProcess": "[parameters('use32BitWorkerProcess')]",
         "linuxFxVersion": "DOCKER|<container-name>"
      }
   }
}
```

<a name="include-app-service-plan"></a>

### <a name="include-app-service-plan-with-deployment"></a>デプロイに App Service プランを含める

標準またはコンテナーのいずれのデプロイの場合でも、デプロイにApp Service プランを含める必要があります。 このプランは Kubernetes 環境との関連性は低くなりますが、標準とコンテナーのどちらのデプロイにも、App Service プランが引き続き必要です。

他の作成オプションでは通常、このプラン用に Azure リソースのプロビジョニングが処理されますが、デプロイで "コードとしてのインフラストラクチャ" テンプレートを使用する場合は、プラン用に Azure リソースを明示的に作成する必要があります。 ホスティング プラン リソースは変更されず、`sku` の情報だけが変更されます。

[Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) に、次の値を含めます。

| Item | JSON プロパティ | 説明 |
|------|---------------|-------------|
| 場所 | `location` | カスタムの場所および Kubernetes 環境と同じリソースの場所 (Azure リージョン) を必ず使用してください。 ロジック アプリのリソースの場所、カスタムの場所、Kubernetes 環境はすべて同じである必要があります。 <p><p>**注**: この値は、カスタムの場所の "*名前*" と同じではありません。 |
| 種類 | `kind` | `kubernetes,linux` である必要がある、デプロイされる App Service プランの種類 |
| 拡張された場所 | `extendedLocation` | このオブジェクトでは、Kubernetes 環境に "*カスタムの場所*" の `"name"` が必要であるため、`"type"` が `"CustomLocation"` に設定されている必要があります。 |
| ホスティング プラン名 | `name` | App Service プランの名前 |
| プラン レベル | `sku: tier` | App Serviceプラン レベル (`K1`) |
| プラン名 | `sku: name` | App Serviceプラン名 (`Kubernetes`) |
||||

#### <a name="arm-template"></a>ARM テンプレート

次の例は、アプリのデプロイで使用できる App Service プランのリソース定義のサンプルを示しています。 詳細については、[Microsoft.Web/serverfarms template format (ARM テンプレート)](/azure/templates/microsoft.web/serverfarms?tabs=json) のドキュメントを参照してください。

```json
{
   "type": "Microsoft.Web/serverfarms",
   "apiVersion": "2020-12-01",
   "location": "<location>",
   "name": "<hosting-plan-name>",
   "kind": "kubernetes,linux",
   "extendedLocation": {
      "name": "[parameters('customLocationId')]",
      "type": "CustomLocation"
   },
   "sku": {
      "tier": "K1",
      "name": "Kubernetes", 
      "capacity": 1
   },
   "properties": {
      "kubeEnvironmentProfile": {
         "id": "[parameters('kubeEnvironmentId')]"
      }
   }
}
```

<a name="change-scaling"></a>

## <a name="change-default-scaling-behavior"></a>既定のスケーリング動作を変更する

Azure Arc 対応 Logic Apps では、バックエンド ストレージ キュー内の "*ジョブ*" の数に基づいて、ロジック アプリのスケーリングが自動的に管理されます。 ただし、この既定のスケーリング動作は変更できます。

ロジック アプリでは、ワークフロー定義によって実行するアクションのシーケンスが指定されます。 ワークフローの実行がトリガーされるたびに、Azure Logic Apps ランタイムによって、ワークフロー定義内のアクションの種類ごとに "*ジョブ*" が作成されます。 次に、ランタイムによってこれらのジョブが "*ジョブ シーケンサー*" に整理されます。 このシーケンサーによって、ワークフロー定義のジョブの実行がオーケストレーションされますが、基になる Azure Logic Apps ジョブ オーケストレーション エンジンによって各ジョブが実行されます。

ステートフル ワークフローの場合、ジョブ オーケストレーション エンジンによりストレージ キュー メッセージが使用され、ジョブ シーケンサーでジョブがスケジュールされます。 バックグラウンドでは、"*ジョブ ディスパッチャー*" (または "*ディスパッチャー worker インスタンス*") によってこれらのジョブ キューが監視されます。 オーケストレーション エンジンにより worker インスタンスの既定の最小および最大数が使用され、ジョブ キューが監視されます。 ステートレス ワークフローの場合、オーケストレーション エンジンによって、アクションの状態がメモリ内に完全に保持されます。

既定のスケーリング動作を変更するには、ジョブ キューを監視する worker インスタンスの最小および最大数に異なる値を指定します。

### <a name="prerequisites-to-change-scaling"></a>スケーリングを変更するための前提条件

Azure Arc 対応 Kubernetes クラスターで、以前に作成した App Service バンドル拡張機能の `keda.enabled` プロパティを `true` に設定する必要があります。 詳細については、[最重要の前提条件](#prerequisites)を確認してください。

### <a name="change-scaling-threshold"></a>スケーリングしきい値を変更する

Azure Arc 対応 Logic Apps では、ジョブ キューの長さによってスケール イベントがトリガーされ、ロジック アプリのスケーリング発生頻度のしきい値が設定されます。 キューの長さ (既定値は `20` ジョブに設定されています) は変更することができます。 スケーリングの頻度を低くするには、キューの長さを増やします。 スケーリングの頻度を高くするには、キューの長さを減らします。 このプロセスでは試行錯誤が必要になる場合があります。

キューの長さを変更するには、ロジック アプリ プロジェクトのルート レベルの **host.json** ファイルで、`Runtime.ScaleMonitor.KEDA.TargetQueueLength` プロパティを設定します。次に例を示します。

```json
"extensions": {
   "workflow": {
      "settings": {
         "Runtime.ScaleMonitor.KEDA.TargetQueueLength": "10"
      }
   }
}
```

### <a name="change-throughput-maximum"></a>スループットの最大値を変更する

既存のロジック アプリ リソースで、worker インスタンスの最大数 (既定値は `2` に設定されています) を変更できます。 この値によって、ジョブ キューを監視できる worker インスタンスの数の上限を制御します。

この最大値を変更するには、Azure CLI (ロジック アプリの作成のみ) と Azure portal を使用します。

#### <a name="azure-cli"></a>Azure CLI

新しいロジック アプリを作成するには、次のパラメーターを指定してコマンド `az logicapp create` を実行します。

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 1] [--max-worker-count 4]
```

インスタンスの最大数を構成するには、`--settings` パラメーターを使用します。

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --settings "K8SE_APP_MAX_INSTANCE_COUNT=10"
```

#### <a name="azure-portal"></a>Azure portal

シングルテナント ベースのロジック アプリの設定で、こちらの手順に従って、`K8SE_APP_MAX_INSTANCE_COUNT` の設定値を追加または編集します。

1. Azure portal で、シングルテナント ベースのロジック アプリを検索して開きます。

1. ロジック アプリ メニューの **[設定]** で、 **[構成]** を選択します。

1. **[構成]** ペインの **[アプリケーション設定]** で、新しいアプリケーション設定を追加するか、既に追加されている場合は既存の値を編集します。

   1. **[新しいアプリケーション設定]** を選択し、必要な最大値を指定した設定 `K8SE_APP_MAX_INSTANCE_COUNT` を追加します。

   1. 設定 `K8SE_APP_MAX_INSTANCE_COUNT` の既存の値を編集します。

1. 完了したら、変更を保存します。

### <a name="change-throughput-minimum"></a>スループットの最小値を変更する

既存のロジック アプリ リソースで、worker インスタンスの最小数 (既定値は `1` に設定されています) を変更できます。 この値によって、ジョブ キューを監視できる worker インスタンスの数の下限を制御します。 高可用性またはパフォーマンスを実現するには、この値を大きくします。

この最小値を変更するには、Azure CLI または Azure portal を使用します。

#### <a name="azure-cli"></a>Azure CLI

既存のロジック アプリのリソースの場合は、次のパラメーターを指定してコマンド `az logicapp scale` を実行します。

```azurecli
az logicapp scale --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --instance-count 5 
```

新しいロジック アプリを作成するには、次のパラメーターを指定してコマンド `az logicapp create` を実行します。

```azurecli
az logicapp create --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --storage-account MyStorageAccount --custom-location MyCustomLocation 
   [--plan MyHostingPlan] [--min-worker-count 2] [--max-worker-count 4]
```

#### <a name="azure-portal"></a>Azure portal

シングルテナント ベースのロジック アプリの設定で、こちらの手順に従って **[Scale Out]** プロパティの値を変更します。

1. Azure portal で、シングルテナント ベースのロジック アプリを検索して開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[Scale Out]** を選択します。

1. **[Scale Out]** ペインで、最小インスタンス スライダーを目的の値にドラッグします。

1. 完了したら、変更を保存します。

## <a name="troubleshoot-problems"></a>問題のトラブルシューティング

デプロイしたロジック アプリの詳細情報を取得するには、次のオプションを試してください。

### <a name="access-app-settings-and-configuration"></a>アプリの設定と構成にアクセスする

アプリの設定にアクセスするには、次のパラメーターを指定してコマンド `az logicapp config appsettings` を実行します。

```azurecli
az logicapp config appsettings list --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

アプリの設定を構成するには、次のパラメーターを指定してコマンド `az logicapp config appsettings set` を実行します。 ご自分の設定の名前と値を持つ `--settings` パラメーターを必ず使用してください。

```azurecli
az logicapp config appsettings set --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --settings "MySetting=1"
```

アプリの設定を削除するには、次のパラメーターを指定してコマンド `az logicapp config appsettings delete` を実行します。 削除する設定の名前を持つ `--setting-names` パラメーターを必ず使用してください。

```azurecli
az logicapp config appsettings delete --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
   --setting-names MySetting
```

### <a name="view-logic-app-properties"></a>ロジック アプリのプロパティを表示する

アプリの情報とプロパティを表示するには、次のパラメーターを指定してコマンド `az logicapp show` を実行します。

```azurecli
az logicapp show --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription
```

### <a name="monitor-workflow-activity"></a>ワークフロー アクティビティを監視する

ロジック アプリでワークフローのアクティビティを表示するには、こちらの手順を実行します。 

1. Azure portal で、デプロイされたロジック アプリを見つけて開きます。

1. ロジック アプリのメニューで、 **[ワークフロー]** を選択し、ワークフローを選択します。 

1. [ワークフロー] メニューで、 **[概要]** を選択します。

### <a name="collect-logs"></a>ログの収集

ロジック アプリに関するログ データを取得するには、ロジック アプリで Application Insights を有効にします (まだ有効になっていない場合)。

## <a name="next-steps"></a>次の手順

- [Azure Arc 対応 Logic Apps について](azure-arc-enabled-logic-apps-overview.md)
