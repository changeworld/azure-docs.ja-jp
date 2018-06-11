# [Azure Resource Manager のドキュメント](index.md)

# 概要
## [Azure Resource Manager とは](resource-group-overview.md)
## [リソース プロバイダーと種類](resource-manager-supported-services.md)
## [Resource Manager デプロイとクラシック デプロイ](resource-manager-deployment-model.md)
## [サブスクリプションのガバナンス](/azure/architecture/cloud-adoption-guide/subscription-governance)
## [管理グループを使用してリソースを整理する](management-groups-overview.md)

# 作業開始
## [テンプレートを作成してデプロイする](resource-manager-create-first-template.md)
## [テンプレート用の VS Code 拡張機能](resource-manager-vscode-extension.md)
## [Visual Studio と Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 方法
## テンプレートの作成
### [テンプレート セクション](resource-group-authoring-templates.md)
#### [パラメーター](resource-manager-templates-parameters.md)
#### [変数](resource-manager-templates-variables.md)
#### [リソース](resource-manager-templates-resources.md)
#### [Outputs](resource-manager-templates-outputs.md)
### [リンクされたテンプレートと入れ子になったテンプレート](resource-group-linked-templates.md)
### [リソース間の依存関係を定義する](resource-group-define-dependencies.md)
### [複数のインスタンスの作成](resource-group-create-multiple.md)
### [リソースを更新する](/azure/architecture/building-blocks/extending-templates/update-resource)

## デプロイ
### Azure PowerShell
#### [テンプレートのデプロイ](resource-group-template-deploy.md)
#### [SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-powershell-sas-token.md)
#### [テンプレートをエクスポートして再デプロイする](resource-manager-export-template-powershell.md)
### Azure CLI
#### [テンプレートのデプロイ](resource-group-template-deploy-cli.md)
#### [SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-cli-sas-token.md)
#### [テンプレートをエクスポートして再デプロイする](resource-manager-export-template-cli.md)
### Azure ポータル
#### [リソースのデプロイ](resource-group-template-deploy-portal.md)
#### [テンプレートをエクスポートする](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [複数のリソース グループまたはサブスクリプション](resource-manager-cross-resource-group-deployment.md)
### [Visual Studio Team Services を使用した継続的インテグレーション](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)

## [管理]
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [タグを使用してリソースを整理する](resource-group-using-tags.md)
### [リソースを新しいグループまたはサブスクリプションに移動する](resource-group-move-resources.md)
### [EA サブスクリプションを作成する](programmatically-create-subscription.md)
### [EA サブスクリプションを作成するためのアクセスを許可する](grant-access-to-create-subscription.md)
### [管理グループを作成する](management-groups-create.md)
### [管理グループを管理する](management-groups-manage.md)

## アクセスを制御する
### サービス プリンシパルの作成
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [サブスクリプションにアクセスするための認証 API](resource-manager-api-authentication.md)
### [リソースのロック](resource-group-lock-resources.md)

## Audit
### [アクティビティ ログを表示する](resource-group-audit.md)
### [デプロイ操作の表示](resource-manager-deployment-operations.md)

## トラブルシューティング
### [デプロイ エラーのトラブルシューティング](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Linux デプロイの問題](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Linux のプロビジョニングと割り当ての問題](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Windows のプロビジョニングと割り当ての問題](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Windows デプロイの問題](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# リファレンス
## [テンプレートの形式](/azure/templates/)
## [テンプレート関数](resource-group-template-functions.md)
### [配列とオブジェクトの関数](resource-group-template-functions-array.md)
### [比較関数](resource-group-template-functions-comparison.md)
### [デプロイ関数](resource-group-template-functions-deployment.md)
### [論理関数](resource-group-template-functions-logical.md)
### [数値関数](resource-group-template-functions-numeric.md)
### [リソース関数](resource-group-template-functions-resource.md)
### [文字列関数](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [個人データを管理する](resource-manager-personal-data.md)
## [スロットル リクエスト](resource-manager-request-limits.md)
## [非同期操作の追跡](resource-manager-async-operations.md)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
