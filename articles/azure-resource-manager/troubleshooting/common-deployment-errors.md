---
title: よくある Azure デプロイ エラーのトラブルシューティングに関する記事
description: Azure リソースを Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してデプロイする際によくあるエラーについて説明します。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 988d2e85475ed1c94580cc550630df82fceddb7a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470245"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>よくある Azure デプロイ エラーのトラブルシューティングに関する記事

この記事では、Azure へのデプロイに関する一般的なエラーについて説明し、その解決方法についての情報を提供します。 Azure リソースは Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してデプロイできます。 デプロイ エラーのエラー コードを見つけることができない場合は、「[エラー コードを見つける](find-error-code.md)」を参照してください。

エラー コードが一覧にない場合は、GitHub の問題を提出してください。 ページの右側にある **[フィードバック]** を選択します。 ページの下部にある **[フィードバック]** で **[This page]\(このページ\)** を選択します。

## <a name="error-codes"></a>エラー コード

| エラー コード | 対応策 | 詳細情報 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | ストレージ アカウントの名前付けの制限に従ってください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 使用可能なストレージ アカウント プロパティを確認してください。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできません。 後で要求を再試行するか、別の VM サイズを要求します。 | [Linux のプロビジョニングと割り当ての問題](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Windows のプロビジョニングと割り当ての問題](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [割り当てエラーのトラブルシューティングを行う](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | 同時実行操作の完了を待ちます。 | |
| AuthorizationFailed | お客様のアカウントまたはサービス プリンシパルには、デプロイを完了するために十分なアクセス権がありません。 自分のアカウントが属するロールと、デプロイの範囲に対するアクセス権を確認してください。<br><br>必要なリソース プロバイダーが登録されていないと、このエラーを受け取ることがあります。 | [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[登録を解決する](error-register-resource-provider.md) |
| BadRequest | Resource Manager で予期される値と一致しないデプロイ値を送信しました。 トラブルシューティングの方法については、内部ステータス メッセージを確認してください。 | [テンプレート リファレンス](/azure/templates/) <br><br> [サポートされる場所](../templates/resource-location.md) |
| Conflict | リソースの現在の状態では許可されていない操作を要求しています。 たとえば、ディスクのサイズ変更が許可されているのは、VM の作成時と VM の割り当て解除時のみです。 | |
| DeploymentActiveAndUneditable | このリソース グループへの同時実行デプロイが完了するまで待ちます。 | |
| DeploymentFailedCleanUp | 完全モードでデプロイすると、テンプレートにないすべてのリソースが削除されます。 このエラーは、テンプレートにないすべてのリソースを削除するための適切なアクセス許可がない場合に表示されます。 このエラーを回避するには、デプロイ モードを増分モードに変更します。 | [Azure Resource Manager のデプロイ モード](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | デプロイ名には、英字、数字、ハイフン `(-)`、ドット `(.)`、アンダースコア `(_)` のみを含めることができます。 | |
| DeploymentNameLengthLimitExceeded | デプロイ名は、64 文字までに制限されています。  | |
| DeploymentFailed | DeploymentFailed エラーは、そのエラーを解決するために必要な詳細が示されない一般的なエラーです。 エラー コードのエラー詳細で情報を確認してください。 | [エラー コードを見つける](find-error-code.md) |
| DeploymentQuotaExceeded | リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 | [デプロイ数が 800 を超えたときのエラーを解決する](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS レコード名は、一意の名前にする必要があります。 別の番号を入力します。 | |
| ImageNotFound | VM イメージの設定を確認してください。 |  |
| InternalServerError | 一時的な問題が原因で発生します。 デプロイを再試行してください。 | |
| InUseSubnetCannotBeDeleted | このエラーは、リソースを更新しようとしたときに、要求プロセスによってリソースの削除と作成が行われている場合に発生する可能性があります。 変更されていないすべての値を指定してください。 | [リソースを更新する](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 該当するテナントのアクセス トークンを取得します。 トークンは、自分のアカウントが属するテナントからのみ取得できます。 | |
| InvalidContentLink | これは、入れ子になった無効なテンプレートにリンクしようとしたことが原因と考えられます。 入れ子になったテンプレートに指定した URI を十分に確認してください。 ストレージ アカウントにテンプレートがある場合は、URI がアクセス可能であることを確認してください。 場合によっては、SAS トークンを渡す必要があります。 現時点では、[Azure Storage ファイアウォール](../../storage/common/storage-network-security.md)の背後にあるストレージ アカウントのテンプレートにリンクすることはできません。 GitHub などの別のリポジトリにテンプレートを移動することを検討してください。 | [リンク済みテンプレート](../templates/linked-templates.md) |
| InvalidDeploymentLocation | サブスクリプション レベルでデプロイする場合は、以前に使用したデプロイ名に別の場所を指定しています。 | [サブスクリプション レベルのデプロイ](../templates/deploy-to-subscription.md) |
| InvalidParameter | リソースに対して指定したいずれかの値が、予期される値に一致しません。 このエラーはさまざまな状況が原因となって発生する可能性があります。 たとえば、パスワードが十分でない場合や、BLOB 名が正しくない場合があります。 エラー メッセージから、どの値を修正する必要があるかがわかるはずです。 | |
| InvalidRequestContent | デプロイの値に認識されない値が含まれているか、必要な値が不足しています。 リソースの種類の値を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidRequestFormat | デプロイを実行するときにデバッグ ログを有効にし、要求の内容を確認してください。 | [デバッグ ログ](enable-debug-logging.md) |
| InvalidResourceLocation | ストレージ アカウントに一意の名前を指定してください。 | [ストレージ アカウント名の解決](error-storage-account-name.md) |
| InvalidResourceNamespace | **type** プロパティに指定したリソース名前空間を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidResourceReference | リソースがまだ存在しないか、正しく参照されていません。 依存関係を追加する必要があるかどうかを確認してください。 シナリオに必要なパラメーターを含めて **reference** 関数を使用しているかどうかを確認してください。 | [依存関係を解決する](error-not-found.md) |
| InvalidResourceType | **type** プロパティに指定したリソースの種類を確認してください。 | [テンプレート リファレンス](/azure/templates/) |
| InvalidSubscriptionRegistrationState | リソース プロバイダーにサブスクリプションを登録してください。 | [登録を解決する](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | テンプレートの構文にエラーがないか確認してください。 | [無効なテンプレートを解決する](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 不要な依存関係を削除します。 | [循環依存関係を解決する](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | テンプレートを簡略化して、サイズを小さくします。 | [テンプレートのサイズ エラーの解決](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | デプロイ先のリソース グループと同じテナントに自分のアカウントが属しているかどうかを確認してください。 | |
| LinkedInvalidPropertyId | リソースのリソース ID が解決されていません。 リソース ID に必要なすべての値を指定していることを確認してください。 たとえば、サブスクリプション ID、リソース グループ名、リソースの種類、親リソース名 (必要な場合)、リソース名などです。 | |
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

## <a name="next-steps"></a>次の手順

- [エラー コードの検索](find-error-code.md)
- [デバッグ ログの有効化](enable-debug-logging.md)
- [トラブルシューティング テンプレートを作成する](create-troubleshooting-template.md)
