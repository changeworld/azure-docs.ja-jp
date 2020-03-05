---
title: テンプレートでデプロイ スクリプトを使用する | Microsoft Docs
description: Azure Resource Manager テンプレートでデプロイ スクリプトを使用する。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: jgao
ms.openlocfilehash: e881cde36bc56c175004e8d6adb9b7b85e9b5454
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616309"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>テンプレートでデプロイ スクリプトを使用する (プレビュー)

Azure Resource テンプレートでデプロイ スクリプトを使用する方法を説明します。 `Microsoft.Resources/deploymentScripts` という名前の新しいリソースの種類を使用すると、ユーザーはテンプレートのデプロイでデプロイ スクリプトを実行し、実行結果を確認できます。 これらのスクリプトは、次のようなカスタム手順を実行するために使用できます。

- ユーザーをディレクトリに追加する
- アプリ登録を作成する
- データ プレーン操作 (BLOB のコピー、シード データベースなど) の実行
- ライセンス キーを検索して検証する
- 自己署名証明書を作成する
- Azure AD にオブジェクトを作成する
- カスタム システムから IP アドレス ブロックを検索する

デプロイ スクリプトの利点は次のとおりです。

- 簡単にコーディング、使用、デバッグができます。 デプロイ スクリプトは、好みの開発環境で開発できます。 スクリプトは、テンプレートまたは外部スクリプト ファイルに埋め込むことができます。
- スクリプト言語とプラットフォームを指定できます。 現時点では、Linux 環境の Azure PowerShell および Azure CLI のデプロイ スクリプトがサポートされています。
- スクリプトの実行に使用する ID を指定できるようにします。 現時点では、[Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) のみがサポートされています。
- スクリプトにコマンド ライン引数を渡すことを許可します。
- スクリプトの出力を指定して、デプロイに渡すことができます。

> [!NOTE]
> デプロイ スクリプトは現在プレビュー段階です。 使用するには、[プレビューにサインアップする](https://aka.ms/armtemplatepreviews)必要があります。

> [!IMPORTANT]
> スクリプトの実行とトラブルシューティングのため、同じリソース グループ内に 2 つのデプロイ スクリプト リソース (ストレージ アカウントとコンテナー インスタンス) が作成されます。 これらのリソースは、通常、デプロイ スクリプトの実行が終了状態になったときにスクリプト サービスによって削除されます。 リソースが削除されるまで、リソースに対する課金が発生します。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。

## <a name="prerequisites"></a>前提条件

- **ターゲットの resource-group への共同作成者のロールが付与されたユーザー割り当てマネージド ID**。 この ID は、デプロイ スクリプトを実行するために使用されます。 リソース グループの外部で操作を実行するには、追加のアクセス許可を付与する必要があります。 たとえば、新しいリソース グループを作成する場合は、サブスクリプション レベルに ID を割り当てます。

  > [!NOTE]
  > デプロイ スクリプト エンジンでは、バックグラウンドでストレージ アカウントとコンテナー インスタンスを作成する必要があります。  サブスクリプションで Azure ストレージ アカウント (Microsoft.Storage) と Azure コンテナー インスタンス (Microsoft.ContainerInstance) リソース プロバイダーを登録していない場合は、サブスクリプション レベルで共同作成者のロールを持つユーザー割り当てマネージド ID が必要です。

  ID を作成するには、[Azure portal を使用してユーザー割り当てマネージド ID を作成する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)、[Azure CLI を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)、または [Azure PowerShell を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)に関する各記事を参照してください。 この識別 ID は、テンプレートをデプロイするときに必要です。 ID の形式は次のとおりです。

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  次の CLI または PowerShell スクリプトを使用して、リソース グループ名と ID 名を指定することで ID を取得します。

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```
  ---

- **Azure PowerShell バージョン 3.0.0、2.8.0、または 2.7.0** あるいは **Azure CLI バージョン 2.0.80、2.0.79、2.0.78、または 2.0.77**。 これらのバージョンは、テンプレートのデプロイには必要ありません。 ただし、これらのバージョンは、デプロイ スクリプトをローカルでテストするために必要です。 [Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 事前構成済みの Docker イメージを使用できます。  「[開発環境の設定](#configure-development-environment)」を参照してください。

## <a name="sample-templates"></a>サンプル テンプレート

次の JSON はサンプルです。  最新のテンプレートのスキーマについては、[こちら](/azure/templates/microsoft.resources/deploymentscripts)をご覧ください。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> この例は、デモンストレーション用です。  **scriptContent** と **primaryScriptUris** をテンプレート内で共存させることはできません。

プロパティ値の詳細:

- **identity**: デプロイ スクリプト サービスは、ユーザー割り当てのマネージド ID を使用してスクリプトを実行します。 現時点では、ユーザー割り当てマネージド ID のみがサポートされています。
- **kind**: スクリプトの種類を指定します。 現在、Azure PowerShell および Azure CLI のスクリプトがサポートされています。 値は、**AzurePowerShell** と **AzureCLI** です。
- **forceUpdateTag**:テンプレートのデプロイ間でこの値を変更すると、デプロイ スクリプトが強制的に再実行されます。 パラメーターの defaultValue として設定する必要がある newGuid() 関数または utcNow() 関数を使用します。 詳細については、「[スクリプトを複数回実行する](#run-script-more-than-once)」を参照してください。
- **azPowerShellVersion**/**azCliVersion**:使用するモジュールのバージョンを指定します。 現在、デプロイ スクリプトでは、Azure PowerShell バージョン 2.7.0、2.8.0、3.0.0 と、Azure CLI バージョン 2.0.80、2.0.79、2.0.78、2.0.77 がサポートされています。
- **arguments**: パラメーター値を指定します。 値はスペースで区切ります。
- **scriptContent**:スクリプトの内容を指定します。 外部スクリプトを実行するには、代わりに `primaryScriptUri` を使用します。 例については、「[インライン スクリプトを使用する](#use-inline-scripts)」および「[外部スクリプトを使用する](#use-external-scripts)」を参照してください。
- **primaryScriptUri**:サポートされているファイル拡張子を含むプライマリ デプロイ スクリプトへのパブリックにアクセス可能な URL を指定します。
- **supportingScriptUris**:`ScriptContent` または `PrimaryScriptUri` で呼び出されるサポート ファイルへのパブリックにアクセス可能な URL の配列を指定します。
- **timeout**: [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定される、スクリプトの許容最長実行時間を指定します。 既定値は **P1D** です。
- **cleanupPreference**。 スクリプトの実行がターミナル状態になった際の、デプロイ リソースのクリーンアップ設定を指定します。 既定の設定は、**Always** です。これは、ターミナル状態 (Succeeded、Failed、Canceled) に関係なくリソースを削除することを意味します。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。
- **retentionInterval**:デプロイ スクリプトの実行が終了状態に達した後、サービスがデプロイ スクリプト リソースを保持する間隔を指定します。 この期間が経過すると、デプロイ スクリプト リソースは削除されます。 期間は [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) のパターンに基づきます。 既定値は 7 日を意味する **P1D** です。 このプロパティは、cleanupPreference が *OnExpiration* に設定されている場合に使用されます。 *OnExpiration* プロパティは、現在有効になっていません。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。

### <a name="additional-samples"></a>その他のサンプル

- [証明書を作成してキー コンテナーに割り当てる](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [ユーザー割り当てマネージド ID を作成してリソース グループに割り当て、デプロイ スクリプトを実行する](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)。

> [!NOTE]
> 事前にユーザー割り当て ID を作成し、アクセス許可を付与しておくことをお勧めします。 デプロイ スクリプトを実行するものと同じテンプレートで、ID を作成してアクセス許可を付与すると、サインインおよびアクセス許可に関連するエラーが発生する場合があります。 アクセス許可が有効になるまでには少し時間がかかります。

## <a name="use-inline-scripts"></a>インライン スクリプトを使用する

次のテンプレートには、`Microsoft.Resources/deploymentScripts` の種類で定義されたリソースが 1 つあります。

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> インライン デプロイ スクリプトは二重引用符で囲まれているため、デプロイ スクリプト内の文字列は、代わりに単一引用符で囲む必要があります。 PowerShell のエスケープ文字は **&#92;** です。 前の JSON サンプルに示されているように、文字列の置換を使用することを検討することもできます。 名前パラメーターの既定値を表示します。

このスクリプトは、1 つのパラメーターを受け取り、パラメーター値を出力します。 **DeploymentScriptOutputs** は、出力を格納するために使用されます。  出力セクションの**値**の線は、格納されている値にアクセスする方法を示しています。 `Write-Output` はデバッグ目的で使用されます。 出力ファイルにアクセスする方法については、「[デプロイ スクリプトのデバッグ](#debug-deployment-scripts)」を参照してください。  プロパティの説明については、「[サンプル テンプレート](#sample-templates)」を参照してください。

スクリプトを実行するには、 **[使ってみる]** を選択して Azure Cloud Shell を開き、次のコードをシェル ペインに貼り付けます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

出力は次のようになります。

![Resource Manager テンプレート デプロイ スクリプトの hello world 出力](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>外部スクリプトを使用する

インライン スクリプトに加えて、外部スクリプト ファイルを使用することもできます。 **ps1** ファイル拡張子を持つプライマリ PowerShell スクリプトのみがサポートされています。 CLI スクリプトの場合、スクリプトが有効な bash スクリプトである限り、プライマリ スクリプトには任意の拡張子を設定できます (または拡張子なしにできます)。 外部スクリプト ファイルを使用するには、`scriptContent` を `primaryScriptUri` に置き換えます。 次に例を示します。

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

例を確認するには、[[こちら]](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json) を選択します。

外部スクリプト ファイルにアクセスできる必要があります。  Azure ストレージ アカウントに格納されているスクリプト ファイルをセキュリティで保護する方法については、「[チュートリアル:Azure Resource Manager テンプレートのデプロイ時に成果物をセキュリティで保護する](./template-tutorial-secure-artifacts.md)」 を参照してください。

## <a name="use-supporting-scripts"></a>サポート スクリプトを使用する

複雑なロジックを、1 以上のサポート スクリプト ファイルに分けることができます。 `supportingScriptURI` プロパティを使用すると、必要に応じて、サポート スクリプト ファイルに URI の配列を指定できます。

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

サポート スクリプト ファイルは、インライン スクリプトとプライマリ スクリプト ファイルの両方から呼び出すことができます。 サポート スクリプト ファイルには、ファイル拡張子に関する制限はありません。

サポート ファイルは、ランタイムで azscripts/azscriptinput にコピーされます。 インライン スクリプトおよびプライマリ スクリプト ファイルからサポート ファイルを参照するには、相対パスを使用します。

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell スクリプトからの出力を操作する

次のテンプレートは、2 つの deploymentScripts リソース間で値を渡す方法を示しています。

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

最初のリソースでは、 **$DeploymentScriptOutputs** という名前の変数を定義し、それを使用して出力値を格納します。 テンプレート内の別のリソースから出力値にアクセスするには、次のように指定します。

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>CLI スクリプトからの出力を操作する

PowerShell デプロイ スクリプトとは異なり、CLI/bash のサポートでは、スクリプトの出力を格納するための共通変数は公開されません。代わりに、スクリプトの出力ファイルが存在する場所を格納する **AZ_SCRIPTS_OUTPUT_PATH** という環境変数があります。 デプロイ スクリプトが Resource Manager テンプレートから実行される場合、この環境変数は Bash シェルによって自動的に設定されます。

デプロイ スクリプトの出力は AZ_SCRIPTS_OUTPUT_PATH の場所に保存される必要があり、その出力は有効な JSON 文字列オブジェクトでなければなりません。 ファイルの内容は、キーと値のペアとして保存される必要があります。 たとえば、文字列の配列は、{ “MyResult”: [ “foo”, “bar”] } として格納されます。  配列の結果のみ ([ “foo”, “bar” ] など) の格納は、無効です。

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json?range=1-44)]

前のサンプルでは、[jq](https://stedolan.github.io/jq/) が使用されています。 これには、コンテナー イメージが付属しています。 「[開発環境の設定](#configure-development-environment)」を参照してください。

## <a name="handle-non-terminating-errors"></a>終了しないエラーを処理する

デプロイ スクリプトで [ **$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) 変数を使用することで、終了しないエラーに PowerShell が応答する方法を制御できます。 デプロイ スクリプト エンジンでは、値の設定も変更も行いません。  $ErrorActionPreference に設定した値に関係なく、デプロイ スクリプトでエラーが発生すると、そのスクリプトによってリソースのプロビジョニングの状態が *[失敗]* に設定されます。

## <a name="debug-deployment-scripts"></a>デプロイ スクリプトのデバッグ

スクリプ トサービスによって、[ストレージ アカウント](../../storage/common/storage-account-overview.md)と、スクリプト実行用の[コンテナー インスタンス](../../container-instances/container-instances-overview.md)が作成されます。 両方のリソースのリソース名のサフィックスは、**azscripts** です。

![Resource Manager テンプレート デプロイ スクリプトのリソース名](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

ユーザー スクリプト、実行結果、stdout ファイルは、ストレージ アカウントのファイル共有に格納されます。 **Azscripts** という名前のフォルダーがあります。 このフォルダーには、**azscriptinput** と **azscriptinput** という、入力用と出力ファイル用の 2 つのフォルダーがあります。

出力フォルダーには、**executionresult.json** とスクリプトの出力ファイルが含まれています。 **executionresult.json** で、スクリプト実行のエラー メッセージを確認できます。 出力ファイルは、スクリプトが正常に実行された場合にのみ作成されます。 入力フォルダーには、システム用 PowerShell スクリプト ファイルとユーザー用デプロイ スクリプト ファイルが含まれています。 ユーザー用デプロイ スクリプトを変更したものに置き換え、Azure コンテナー インスタンスからデプロイ スクリプトを再実行することができます。

デプロイ スクリプト リソースのデプロイ情報は、REST API を使用して、リソース グループ レベルとサブスクリプション レベルで取得できます。

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient) を使用する例を次に示します。

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

次のように出力されます。

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

出力には、デプロイの状態とデプロイ スクリプトのリソース ID が表示されます。

次の REST API では、ログが返されます。

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

デプロイ スクリプト リソースが削除される前にのみ機能します。

ポータルで deploymentScripts リソースを表示するには、 **[非表示の型の表示]** を選択します。

![Resource Manager テンプレートのデプロイ スクリプト、非表示の型の表示、ポータル](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>デプロイ スクリプト リソースのクリーンアップ

デプロイ スクリプトは、デプロイ スクリプトの実行とデバッグ情報の格納に使用されるストレージ アカウントとコンテナー インスタンスを作成します。 これら 2 つのリソースは、プロビジョニングされたリソースと同じリソース グループ内に作成され、スクリプトの有効期限が切れるとスクリプト サービスによって削除されます。 これらのリソースのライフ サイクルを制御できます。  削除されるまで、両方のリソースに対して課金されます。 価格情報については、「[Container Instances の価格](https://azure.microsoft.com/pricing/details/container-instances/)」と「[Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

これらのリソースのライフ サイクルは、テンプレートの次のプロパティによって制御されます。

- **cleanupPreference**:スクリプトの実行がターミナル状態になった再のクリーンアップ設定  サポートされる値は

  - **Always**:スクリプトの実行がターミナル状態になった場合に、リソースを削除します。 リソースがクリーンアップされた後も deploymentScripts リソースが存在する可能性があるため、システム スクリプトでは、リソースが削除される前に、スクリプトの実行結果 (stdout、出力、戻り値など) が DB にコピーされます。
  - **OnSuccess**:スクリプトの実行が成功した場合にのみ、リソースを削除します。 引き続きリソースにアクセスして、デバッグ情報を見つけることができます。
  - **OnExpiration**:**retentionInterval** 設定の有効期限が切れている場合にのみ、リソースを削除します。 このプロパティは現在無効になっています。

- **retentionInterval**:スクリプト リソースを保持する期間を指定します。この時間が経過すると、期限切れになり削除されます。

> [!NOTE]
> デプロイ スクリプト リソースは、他の目的では使用しないことをお勧めします。

## <a name="run-script-more-than-once"></a>スクリプトを複数回実行する

デプロイ スクリプトの実行はべき等操作です。 deploymentScripts リソースのプロパティ (インライン スクリプトを含む) が変更されていない場合、テンプレートを再デプロイしてもスクリプトは実行されません。 デプロイ スクリプト サービスは、テンプレート内のリソース名と同じリソース グループ内の既存のリソースを比較します。 同じデプロイ スクリプトを複数回実行する場合は、次の 2 つのオプションがあります。

- deploymentScripts リソースの名前を変更します。 たとえば、[utcNow](./template-functions-string.md#utcnow) テンプレート関数をリソース名として使用するか、リソース名の一部として使用します。 リソース名を変更すると、新しい deploymentScripts リソースが作成されます。 これは、スクリプトの実行履歴を保持するのに適しています。

    > [!NOTE]
    > この utcNow 関数は、パラメーターの既定値でのみ使用できます。

- `forceUpdateTag` テンプレート プロパティに別の値を指定してください。  たとえば、値として utcNow を使用します。

> [!NOTE]
> べき等であるデプロイ スクリプトを記述します。 それにより、誤って再び実行されることがあっても、システム変更が引き起こされません。 たとえば、デプロイ スクリプトを使用して Azure リソースを作成する場合は、スクリプトが成功するか、リソースが再度作成されないよう、作成する前にリソースが存在しないことを確認してください。

## <a name="configure-development-environment"></a>開発環境の設定

デプロイ スクリプト開発環境として、事前構成済みの docker コンテナー イメージを使用できます。 次の手順では、Windows で Docker イメージを構成する方法について説明します。 Linux および Mac の場合、インターネット上で情報をご確認ください。

1. 開発用コンピューターに [Docker Desktop](https://www.docker.com/products/docker-desktop) をインストールします。
1. Docker Desktop を開きます。
1. タスクバーから Docker のデスクトップ アイコンを選択し、 **[設定]** を選択します。
1. **[共有ドライブ]** を選択し、コンテナーで使用できるようにするローカル ドライブを選択して、 **[適用]** を選択します

    ![Resource Manager テンプレートのデプロイ スクリプト Docker ドライブ](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. プロンプトで Windows 資格情報を入力します。
1. ターミナル ウィンドウ (コマンド プロンプトまたは Windows PowerShell) を開きます (PowerShell ISE は使用しないでください)。
1. デプロイ スクリプトのコンテナー イメージをローカル コンピューターにプルします。

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    この例では、PowerShell バージョン 2.7.0 を使用します。

    Microsoft Container Registry (MCR) から CLI イメージをプルするには、次のようにします。

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    この例では、CLI バージョン 2.0.80 を使用します。 デプロイ スクリプトでは、[こちら](https://hub.docker.com/_/microsoft-azure-cli)にある既定の CLI コンテナー イメージが使用されます。

1. Docker イメージをローカルで実行します。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **&lt;host driver letter>** および **&lt;host directory name>** を、共有ドライブ上の既存のフォルダーに置き換えます。  このフォルダーは、コンテナー内の **/data** フォルダーにマップされます。 D:\docker をマップする例を次に示します。

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-it** は、コンテナー イメージを維持したままにすることを意味します。

    CLI の例:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. プロンプトが表示されたら、 **[Share it]\(共有する\)** を選択します。
1. 次のスクリーンショットは、d:\docker フォルダーに helloworld.ps1 ファイルがある場合に、PowerShell スクリプトを実行する方法を示しています。

    ![Resource Manager テンプレートのデプロイ スクリプト Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

テストが正常に完了したスクリプトは、デプロイ スクリプトとして使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、デプロイ スクリプトの使用方法について学習しました。 デプロイ スクリプトのチュートリアルを詳しく見るには、次を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Resource Manager テンプレートでデプロイ スクリプトを使用する](./template-tutorial-deployment-script.md)