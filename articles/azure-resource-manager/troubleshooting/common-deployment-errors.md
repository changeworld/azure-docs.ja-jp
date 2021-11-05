---
title: 一般的なデプロイ エラーのトラブルシューティング
description: Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用して Azure リソースをデプロイする際の一般的なエラーを解決する方法について説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe10ccd523618c1afbc9bf5214bcd51bc18419ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092264"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>よくある Azure デプロイ エラーのトラブルシューティングに関する記事

この記事では、Azure へのデプロイに関する一般的なエラーについて説明し、そのエラーを解決するための情報を提供します。 Azure リソースは Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してデプロイできます。 デプロイ エラーのエラー コードを見つけることができない場合は、「[エラー コードを見つける](#find-error-code)」を参照してください。

エラー コードに関する情報を探していて、その情報がこの記事に記載されていない場合は、お知らせください。 このページの最後で、フィードバックを残していただくことができます。 このフィードバックは、GitHub のイシューで追跡されます。

ポータルから PowerShell コマンドおよび Azure CLI コマンドを実行するには、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。 コンピューターからコマンドを実行するには、[Azure PowerShell](/powershell/azure/install-az-ps) または [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

## <a name="error-codes"></a>エラー コード

| エラー コード | 対応策 | 詳細情報 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | ストレージ アカウントの名前付けの制限に従ってください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 使用可能なストレージ アカウント プロパティを確認してください。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできません。 後で要求を再試行するか、別の VM サイズを要求します。 | [Linux のプロビジョニングと割り当ての問題](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Windows のプロビジョニングと割り当ての問題](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [割り当てエラーのトラブルシューティングを行う](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | 同時実行操作の完了を待ちます。 | |
| AuthorizationFailed | お客様のアカウントまたはサービス プリンシパルには、デプロイを完了するために十分なアクセス権がありません。 自分のアカウントが属するロールと、デプロイの範囲に対するアクセス権を確認してください。<br><br>必要なリソース プロバイダーが登録されていないと、このエラーを受け取ることがあります。 | [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[登録を解決する](error-register-resource-provider.md) |
| BadRequest | Resource Manager で予期される値と一致しないデプロイ値を送信しました。 トラブルシューティングの方法については、内部ステータス メッセージを確認してください。 | [テンプレート リファレンス](/azure/templates/)と[サポートされている場所](../templates/resource-location.md) |
| Conflict | リソースの現在の状態では許可されていない操作を要求しています。 たとえば、ディスクのサイズ変更が許可されているのは、VM の作成時と VM の割り当て解除時のみです。 | |
| DeploymentActiveAndUneditable | このリソース グループへの同時実行デプロイが完了するまで待ちます。 | |
| DeploymentFailedCleanUp | 完全モードでデプロイすると、テンプレートにないすべてのリソースが削除されます。 このエラーは、テンプレートにないすべてのリソースを削除するための適切なアクセス許可がない場合に表示されます。 このエラーを回避するには、デプロイ モードを増分モードに変更します。 | [Azure Resource Manager のデプロイ モード](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | デプロイ名には、英字、数字、ハイフン `(-)`、ドット `(.)`、アンダースコア `(_)` のみを含めることができます。 | |
| DeploymentNameLengthLimitExceeded | デプロイ名は、64 文字までに制限されています。  | |
| DeploymentFailed | DeploymentFailed エラーは、そのエラーを解決するために必要な詳細が示されない一般的なエラーです。 エラー コードのエラー詳細で情報を確認してください。 | [エラー コードを見つける](#find-error-code) |
| DeploymentQuotaExceeded | リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 | [デプロイ数が 800 を超えたときのエラーを解決する](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS レコード名は、一意の名前にする必要があります。 別の番号を入力します。 | |
| ImageNotFound | VM イメージの設定を確認してください。 |  |
| InternalServerError | 一時的な問題が原因で発生します。 デプロイを再試行してください。 | |
| InUseSubnetCannotBeDeleted | リソースを更新しようとするときにこのエラーが発生することがありますが、リソースを削除して作成すると、要求が処理されます。 変更されていないすべての値を指定してください。 | [リソースを更新する](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 該当するテナントのアクセス トークンを取得します。 トークンは、自分のアカウントが属するテナントからのみ取得できます。 | |
| InvalidContentLink | これは、入れ子になった無効なテンプレートにリンクしようとしたことが原因と考えられます。 入れ子になったテンプレートに指定した URI を十分に確認してください。 ストレージ アカウントにテンプレートがある場合は、URI がアクセス可能であることを確認してください。 場合によっては、SAS トークンを渡す必要があります。 現時点では、[Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)の背後にあるストレージ アカウントのテンプレートにリンクすることはできません。 GitHub などの別のリポジトリにテンプレートを移動することを検討してください。 | [リンク済みテンプレート](../templates/linked-templates.md) |
| InvalidDeploymentLocation | サブスクリプション レベルでデプロイする場合は、以前に使用したデプロイ名に別の場所を指定しています。 | [サブスクリプション レベルのデプロイ](../templates/deploy-to-subscription.md) |
| InvalidParameter | リソースに対して指定したいずれかの値が、予期される値に一致しません。 このエラーはさまざまな状況が原因となって発生する可能性があります。 たとえば、パスワードが十分でない場合や、BLOB 名が正しくない場合があります。 エラー メッセージから、どの値を修正する必要があるかがわかるはずです。 | |
| InvalidRequestContent | デプロイの値に認識されない値が含まれているか、必要な値が不足しています。 リソースの種類の値を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidRequestFormat | デプロイを実行するときにデバッグ ログを有効にし、要求の内容を確認してください。 | [デバッグ ログ](#enable-debug-logging) |
| InvalidResourceLocation | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| InvalidResourceNamespace | **type** プロパティに指定したリソース名前空間を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidResourceReference | リソースがまだ存在しないか、正しく参照されていません。 依存関係を追加する必要があるかどうかを確認してください。 シナリオに必要なパラメーターを含めて **reference** 関数を使用しているかどうかを確認してください。 | [依存関係を解決する](error-not-found.md) |
| InvalidResourceType | **type** プロパティに指定したリソースの種類を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidSubscriptionRegistrationState | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | テンプレートの構文にエラーがないか確認してください。 | [無効なテンプレートを解決する](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | デプロイ先のリソース グループと同じテナントに自分のアカウントが属しているかどうかを確認してください。 | |
| LinkedInvalidPropertyId | リソースのリソース ID が正しく解決されていません。 リソース ID に必要なすべての値 (サブスクリプション ID、リソース グループ名、リソースの種類、親リソース名 (必要な場合)、リソース名など) を指定しているかどうかを確認してください。 | |
| LocationRequired | リソースの場所を指定します。 | [場所を設定する](../templates/resource-location.md) |
| MismatchingResourceSegments | 入れ子になったリソースに、名前と種類のセグメントの数が正しく設定されていることを確認します。 | [リソース セグメントを解決する](error-invalid-template.md#incorrect-segment-lengths) |
| MissingRegistrationForLocation | リソース プロバイダーの登録状態、およびサポートされている場所を確認してください。 | [登録を解決する](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](error-register-resource-provider.md) |
| NoRegisteredProviderFound | リソース プロバイダーの登録状態を確認してください。 | [登録を解決する](error-register-resource-provider.md) |
| NotFound | 依存するリソースを、親リソースと並列にデプロイしようとしている可能性があります。 依存関係を追加する必要があるかどうかを確認してください。 | [依存関係を解決する](error-not-found.md) |
| OperationNotAllowed | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過する操作が試みられています。 可能であれば、クォータ内に収まるようにデプロイを修正してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](error-resource-quota.md) |
| ParentResourceNotFound | 子リソースを作成する前に親リソースが存在することを確認してください。 | [親リソースを解決する](error-parent-resource.md) |
| PasswordTooLong | 文字数の多すぎるパスワードを選択したか、パスワードの値をパラメーターとして渡す前にセキュリティで保護された文字列に変換した可能性があります。 テンプレートに **secure string** パラメーターが含まれている場合、値をセキュリティで保護された文字列に変換する必要はありません。 パスワードの値はテキストとして指定してください。 |  |
| PrivateIPAddressInReservedRange | 指定した IP アドレスは、Azure で必要なアドレス範囲に含まれます。 予約済みの範囲を避けるように IP アドレスを変更してください。 | [IP アドレス](../../virtual-network/ip-services/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | 指定した IP アドレスがサブネットの範囲外です。 サブネットの範囲に収まるように IP アドレスを変更してください。 | [IP アドレス](../../virtual-network/ip-services/public-ip-addresses.md) |
| PropertyChangeNotAllowed | デプロイ済みのリソースで、一部のプロパティを変更できません。 リソースを更新する際は、許可されているプロパティに変更を制限してください。 | [リソースを更新する](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | サブスクリプションには、デプロイ中に実行しようとしているアクションを防ぐリソース ポリシーが含まれています。 アクションをブロックしているポリシーを見つけてください。 可能であれば、ポリシーの制限を満たすようにデプロイを変更してください。 | [ポリシーを解決する](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | 予約された名前が含まれていないリソース名を指定します。 | [予約されたリソース名](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 削除が完了するまで待ちます。 | |
| ResourceGroupNotFound | デプロイのターゲット リソース グループの名前を確認してください。 サブスクリプションにターゲット リソース グループが既に存在している必要があります。 サブスクリプションのコンテキストを確認してください。 | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 解決できないリソースをデプロイで参照しています。 **reference** 関数に、シナリオに必要なパラメーターを含まれていることを確認してください。 | [参照を解決する](error-not-found.md) |
| ResourceQuotaExceeded | デプロイで、サブスクリプション、リソース グループ、またはリージョンのクォータを超過するリソースの作成が試みられています。 可能であれば、クォータ内に収まるようにインフラストラクチャを変更してください。 修正できない場合は、クォータの変更を要求することを検討してください。 | [クォータを解決する](error-resource-quota.md) |
| SkuNotAvailable | 選択した場所で利用可能な SKU (VM サイズなど) を選択します。 | [SKU を解決する](error-sku-not-available.md) |
| StorageAccountAlreadyExists <br> StorageAccountAlreadyTaken | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](error-storage-account-name.md)  |
| StorageAccountNotFound | サブスクリプション、リソース グループ、および使用するストレージ アカウントの名前を確認してください。 | |
| SubnetsNotInSameVnet | 仮想マシンでは、仮想ネットワークを 1 つのみを持つことができます。 複数の NIC をデプロイするときは、それらが同じ仮想ネットワークに属していることを確認してください。 | [複数の NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | デプロイ用に指定されたサブスクリプションにアクセスできません。 サブスクリプション ID が間違っている、テンプレートをデプロイしているユーザーにサブスクリプションにデプロイするための適切なアクセス許可がない、またはサブスクリプション ID の形式が間違っている可能性があります。 入れ子になったデプロイを使用して[スコープにまたがってデプロイ](../templates/deploy-to-resource-group.md)する場合は、サブスクリプションの GUID を指定してください。 | |
| SubscriptionNotRegistered | リソースをデプロイするときは、リソース プロバイダーをサブスクリプションに登録する必要があります。 デプロイに Azure Resource Manager テンプレートを使用すると、リソース プロバイダーがサブスクリプションに自動的に登録されます。 自動登録が時間内に完了しないことがあります。 この断続的なエラーを回避するには、デプロイの前にリソース プロバイダーを登録します。 | [登録を解決する](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | 1 回のデプロイのリソース グループ数を減らします。 | [スコープをまたいだデプロイ](../templates/deploy-to-resource-group.md) |

## <a name="find-error-code"></a>エラー コードを見つける

発生する可能性のあるエラーには、次の 2 種類があります。

- 検証エラー
- 展開エラー

検証エラーは、デプロイ前に判断できるシナリオで発生します。 たとえば、テンプレート構文エラーや、サブスクリプションクォータを超えるリソースのデプロイを試みる場合などです。 デプロイ エラーは、デプロイ プロセスで発生する状況に起因します。 並列でデプロイされているリソースへのアクセスの試みも含まれます。

ARM テンプレートの場合、どちらの種類のエラーでも、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーも[アクティビティ ログ](../../azure-monitor/essentials/activity-log.md)に表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。 Bicep ファイルの検証エラーは、アクティビティ ログまたはデプロイ履歴に表示されません。 アクティビティ ログに最新のデプロイ情報が表示されるのには数分かかります。

### <a name="validation-errors"></a>検証エラー

ARM テンプレートをポータルにアップロードし、リソースをデプロイできます。 テンプレートに構文エラーがある場合は、デプロイを実行しようとすると検証エラーが表示されます。 詳細については、「[カスタム テンプレートからリソースをデプロイする](../templates/deploy-portal.md#deploy-resources-from-custom-template)」を参照してください。

次の例では、ストレージ アカウントのデプロイを試み、検証エラーが発生します。

:::image type="content" source="media/common-deployment-errors/validation-error.png" alt-text="Azure portal の検証エラーのスクリーンショット。":::

詳細については、メッセージを選択します。 テンプレートには、エラー コード `InvalidTemplate` を含む構文エラーがあります。 **[概要]** は、式に閉じかっこが不足していることを示しています。

:::image type="content" source="media/common-deployment-errors/validation-details.png" alt-text="構文エラーを示す検証エラー メッセージのスクリーンショット。":::

### <a name="deployment-errors"></a>デプロイ エラー

操作が検証に合格しても、デプロイ中に失敗した場合、デプロイ エラーを受け取ります。

ポータルでデプロイ エラーを確認するには、リソース グループに移動し、 **[設定]**  >  **[デプロイ]** を選択します。 **[エラーの詳細]** を選択します。

:::image type="content" source="media/common-deployment-errors/deployment-error-details.png" alt-text="失敗したデプロイのエラーの詳細へのリソース グループのリンクのスクリーンショット。":::

エラー メッセージとエラー コード `NoRegisteredProviderFound` が表示されます。

:::image type="content" source="media/common-deployment-errors/deployment-error-summary.png" alt-text="デプロイ エラーの詳細を示すメッセージのスクリーンショット。":::

### <a name="powershell"></a>PowerShell

PowerShell でデプロイ エラー コードとメッセージを表示するには、[Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment) を使用します。

```azurepowershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).StatusMessage
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI でデプロイ エラー コードとメッセージを表示するには、[az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) を使用します。

```azurecli
az deployment operation group list --name exampledeployment --resource-group examplegroup --query "[*].properties.statusMessage"
```

## <a name="enable-debug-logging"></a>デバッグ ログの有効化

問題が発生した原因を調べるには、要求と応答の詳細が必要な場合があります。 デプロイ中に、追加情報をログに記録することを要求できます。

### <a name="powershell"></a>PowerShell

PowerShell で、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して `DeploymentDebugLogLevel` パラメーターを `All`、`ResponseContent`、`RequestContent` のいずれかに設定します。

> [!NOTE]
> デバッグ ログを有効にすると、[Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) などのコマンドによって、シークレット (パスワード、listKeys など) がログされる可能性があるという警告が表示されます。
>
> Az モジュール バージョン 4.8 以降の `Get-AzResourceGroupDeploymentOperation` は、出力に `properties` が含まれないため、Azure CLI を使用してデバッグの `request` 情報と `response` 情報を取得してください。  詳細については、[GitHub 問題 14706](https://github.com/Azure/azure-powershell/issues/14706) を参照してください。


```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

次のコマンドレットを使用して、要求の内容を確認します。

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

次のコマンドレットを使用して、応答の内容を確認します。

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

この情報は、テンプレートの値が正しく設定されているかどうかを確認するのに役立ちます。

### <a name="azure-cli"></a>Azure CLI

現在、Azure CLI はデバッグ ログの有効化をサポートしていませんが、デバッグ ログを取得できます。

次のコマンドでデプロイ操作を確認します。

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

次のコマンドを使用して、要求の内容を確認します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

次のコマンドを使用して、応答の内容を確認します。

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>入れ子になったテンプレート

[入れ子になった](../templates/linked-templates.md#nested-template) ARM テンプレートのデバッグ情報をログに記録するには、`debugSetting` 要素を使用します。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep では、[Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) ではなく [modules](../bicep/modules.md) が使用されます。 modules を使用すると、コードを再利用して別の Bicep ファイルから Bicep ファイルをデプロイできます。

## <a name="create-a-troubleshooting-template"></a>トラブルシューティング用テンプレートの作成

場合によっては、テンプレートのトラブルシューティングの最も簡単な方法は、テンプレートの一部をテストすることです。 エラーの原因と考えられる部分に注目できる簡略化されたテンプレートを作成できます。 たとえば、リソースの参照時にエラーが発生しているとします。 この場合、テンプレート全体を処理するのではなく、問題の原因と考えられる部分を返すテンプレートを作成します。 これにより、適切なパラメーターを渡しているかどうか、テンプレート関数を正しく使用しているかどうか、求めているリソースを取得しているかどうかを確認できます。

次の ARM テンプレートと Bicep ファイルの例では、既存のストレージ アカウントを使用しています。 パラメーターで、ストレージ アカウントの名前とリソース グループを指定します。 Azure Cloud Shell を使用して [ARM テンプレート](../templates/deploy-cloud-shell.md#deploy-local-template)または [Bicep ファイル](../bicep/deploy-cloud-shell.md)をデプロイできます。 出力は、ストレージ アカウントのプロパティ名と値を持つオブジェクトです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Bicep サンプルでは、ストレージ アカウントが存在するリソース グループからデプロイ コマンドを実行します。

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

または、設定が誤っている依存関係に関連すると思われるデプロイ エラーが発生しているとします。 テンプレートをテストするには、これを簡略化されたテンプレートに分解します。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 そのリソースを正しく定義したことを確認したら、それに依存するリソース (SQL Database など) を追加します。 これら 2 つのリソースを正しく定義したことを確認したら、その他の依存リソース (監査ポリシーなど) を追加します。 各テストデプロイの間に、リソース グループを削除して、依存関係を適切にテストします。

## <a name="next-steps"></a>次の手順

- ARM テンプレートのトラブルシューティングの詳細については、「[クイックスタート: ARM テンプレート デプロイのトラブルシューティング](quickstart-troubleshoot-arm-deployment.md)」を参照してください。
- Bicep ファイルのトラブルシューティングの詳細については、「[クイックスタート: Bicep ファイルのデプロイのトラブルシューティング](quickstart-troubleshoot-bicep-deployment.md)」を参照してください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../templates/deployment-history.md)に関するページを参照してください。
