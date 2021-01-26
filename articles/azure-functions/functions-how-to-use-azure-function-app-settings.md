---
title: Azure Functions で関数アプリの設定を構成する
description: Azure Functions で関数アプリの設定を構成する方法について説明します。
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 2526fd60d6e07ecf43864945f2b05858b41ca567
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035208"
---
# <a name="manage-your-function-app"></a>お使いの Function App の管理 

Azure Functions では、Function App に個々の関数の実行コンテキストが用意されています。 Function App の動作は、特定の Function App によってホストされるすべての関数に適用されます。 Function App リ内のすべての関数は、同じ[言語](supported-languages.md)である必要があります。 

Function App 内の個々の関数は、共に配置され、共にスケーリングされます。 同じ Function App 内のすべての関数は、Function App がスケーリングされるときに、インスタンスごとに同じリソースを使用します。 

接続文字列、環境変数、およびその他のアプリケーション設定は、Function App ごとに別に定義されます。 Function App 間で共有する必要があるすべてのデータは、外部の永続化されたストアに格納する必要があります。

この記事では、お使いの Function App を構成および管理する方法を説明します。 

> [!TIP]  
> 多くの構成オプションは、[Azure CLI] を使用して管理することもできます。 

## <a name="get-started-in-the-azure-portal"></a>Azure portal での作業開始

1. まず、[Azure Portal] に移動し、Azure アカウントにサインインします。 ポータルの上部にある検索バーで Function App の名前を入力し、一覧からその Function App を選択します。 

2. 左側のウィンドウの **[設定]** で、 **[構成]** を選択します。

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Azure portal の Function App の概要":::

Function App の管理に必要なすべての機能には、概要ページからアクセスできます (特に **[[アプリケーションの設定]](#settings)** と **[[プラットフォーム機能]](#platform-features)** )。

## <a name="work-with-application-settings"></a><a name="settings"></a>アプリケーション設定を操作する

**[アプリケーションの設定]** タブでは、Function App に使用される設定を管理します。 これらの設定は暗号化されて格納されているため、ポータルで値を表示するには **[値を表示する]** を選択する必要があります。 また、Azure CLI を使用してアプリケーション設定にアクセスすることもできます。

### <a name="portal"></a>ポータル

ポータルに設定を追加するには、 **[新しいアプリケーション設定]** を選択して新しいキーと値のペアを追加します。

![Azure portal の関数アプリの設定。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) コマンドでは、次の例のように、既存のアプリケーションの設定を返します。

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドでは、アプリケーション設定を追加または更新します。 次の例では、`CUSTOM_FUNCTION_APP_SETTING` という名前のキーと `12345` の値で設定を作成しています。


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>アプリケーションの設定

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

ローカルで Function App を開発する場合、これらの値を、local.settings.json プロジェクト ファイルにローカルのコピーとして保持する必要があります。 詳細については、[ローカルの設定ファイル](functions-run-local.md#local-settings-file)に関するページを参照してください。

## <a name="hosting-plan-type"></a>ホスティング プランの種類

関数アプリを作成するときに、アプリが実行される App Service ホスティング プランも作成します。 プランには、1 つ以上の関数アプリを含めることができます。 関数の機能、スケーリング、価格は、プランの種類によって異なります。 詳細については、「[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)」ページを参照してください。

自分の関数アプリで使用されているプランの種類は、Azure portal、Azure CLI、または Azure PowerShell API を使用して確認できます。 

次の値は、プランの種類を示します。

| プランの種類 | ポータル | Azure CLI または PowerShell |
| --- | --- | --- |
| [従量課金プラン](consumption-plan.md) | **従量課金プラン** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [専用 (App Service) プラン](dedicated-plan.md) | 各種 | 各種 |

# <a name="portal"></a>[ポータル](#tab/portal)

自分の関数アプリで使用されるプランの種類を確認するには、[Azure portal](https://portal.azure.com) でその関数アプリの **[概要]** タブの **[App Service プラン]** を参照します。 価格レベルを表示するには、 **[App Service プラン]** の名前を選択し、左側のウィンドウから **[プロパティ]** を選択します。

![ポータルでのスケーリング プランの表示](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

次の Azure CLI コマンドを実行して、ホスティング プランの種類を取得します。

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

前の例で、`<RESOURCE_GROUP>` と `<FUNCTION_APP_NAME>` はリソース グループと関数アプリの名前にそれぞれ置き換えてください。 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

次の Azure PowerShell コマンドを実行して、ホスティング プランの種類を取得します。

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
前の例で、`<RESOURCE_GROUP>` と `<FUNCTION_APP_NAME>` はリソース グループと関数アプリの名前にそれぞれ置き換えてください。 

---


## <a name="platform-features"></a>プラットフォーム機能

Function App は、Azure App Service プラットフォームで実行され、管理されます。 このため、Azure の主要 Web ホスティング プラットフォームのほとんどの機能にアクセスできます。 左側のウィンドウでは、Function App で使用できる App Service プラットフォームの多くの機能にアクセスできます。 

> [!NOTE]
>  Function App が従量課金ホスティング プランで実行されている場合は、一部の App Service 機能が使用できません。

この記事の残りの部分では、Functions で便利な Azure portal で使用できる次の App Service 機能について説明します。

+ [App Service Editor](#editor)
+ [コンソール](#console)
+ [高度なツール (Kudu)](#kudu)
+ [デプロイ オプション](#deployment)
+ [CORS](#cors)
+ [認証](#auth)

App Service の設定を使用する方法の詳細については、[Azure App Service の設定の構成](../app-service/configure-common.md)に関するページをご覧ください。

### <a name="app-service-editor"></a><a name="editor"></a>App Service Editor

![App Service Editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service Editor は、JSON 構成ファイルとコード ファイルを変更するために使用できる高度なポータル内エディターです。 このオプションを選択すると、別のブラウザー タブが基本的なエディターで起動します。 これにより、Git リポジトリとの統合、コードの実行とデバッグ、および Function App の設定変更を行うことができます。 このエディターは、組み込みの Function App エディターと比較して、お使いの関数の開発環境が強化されています。  

お使いの関数は、お使いのローカル コンピューターで開発することを検討することをお勧めします。 ローカルで開発し、Azure に発行する場合、お使いのプロジェクト ファイルはポータルで読み取り専用になります。 詳細については、「[Azure Functions をローカルでコーディングしてテストする](functions-develop-local.md)」を参照してください。

### <a name="console"></a><a name="console"></a>コンソール

![関数アプリ コンソール](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

ポータル内コンソールは、コマンド ラインから Function App を操作するのに適した開発者ツールです。 一般的なコマンドには、ディレクトリやファイルの作成、ナビゲーション、バッチ ファイルやスクリプトの実行などがあります。 

ローカルで開発する場合、[Azure Functions Core Tools](functions-run-local.md) および [Azure CLI] を使用することをお勧めします。

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>高度なツール (Kudu)

![Kudu の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service 用の高度なツール (Kudu とも呼ばれます) を使用すると、Function App の高度な管理機能にアクセスできます。 Kudu から、システム情報、アプリ設定、環境変数、サイト拡張機能、HTTP ヘッダー、およびサーバー変数を管理します。 また、`https://<myfunctionapp>.scm.azurewebsites.net/` など、Function App の SCM エンドポイントにアクセスして、**Kudu** を起動することもできます 


### <a name="deployment-center"></a><a name="deployment"></a>デプロイ センター

ソース管理ソリューションを使用してお使いの関数コードを開発および管理する場合、デプロイ センターを使用すると、ソース管理からビルドおよびデプロイをすることができます。 更新を行う場合、お使いのプロジェクトは Azure にビルドされ、デプロイされます。 詳細については、「[Azure Functions のデプロイ テクノロジ](functions-deployment-technologies.md)」を参照してください。

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>クロス オリジン リソース共有

最新のブラウザーでは、クライアントで悪意のあるコードが実行されるのを防ぐために、Web アプリケーションから別のドメインで実行されるリソースへの要求をブロックします。 [クロス オリジン リソース共有 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) を使用すると、お使いの Function App でどこのオリジンがエンドポイントを呼び出せるかを `Access-Control-Allow-Origin` ヘッダーで宣言できます。

#### <a name="portal"></a>ポータル

お使いの Function App の **[許可されたオリジン]** 一覧を構成すると、お使いの Function App の HTTP エンドポイントからのすべての応答に `Access-Control-Allow-Origin` ヘッダーが自動追加されます。 

![関数アプリの CORS 一覧の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

ワイルドカード (`*`) を使用すると、他のすべてのドメインは無視されます。 

許可されたオリジンの一覧にドメインを追加するには、[`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) コマンドを使用します。 次の例では、contoso.com ドメインが追加されます。

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

現在許可されているオリジンを列挙するには、[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) コマンドを使用します。

### <a name="authentication"></a><a name="auth"></a>認証

![関数アプリの認証を構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

関数が HTTP トリガーを使用するとき、呼び出しに、最初に認証を必須とすることができます。 App Service では、Azure Active Directory 認証と、Facebook、Microsoft、Twitter などのソーシャル プロバイダーを使用したサインインがサポートされます。 特定の認証プロバイダーの構成の詳細については、[Azure App Service での認証の概要](../app-service/overview-authentication-authorization.md)に関するページを参照してください。 


## <a name="next-steps"></a>次のステップ

+ [Azure App Service の設定の構成](../app-service/configure-common.md)
+ [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
