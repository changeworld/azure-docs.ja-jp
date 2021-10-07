---
title: Azure portal を使用して Azure Stack Edge Pro と GPU に関連するアクティブ化エラーをトラブルシューティングする | Microsoft Docs
description: Azure Stack Edge Pro GPU のアクティブ化とキー コンテナー関連の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 1f6729fc0a723a21f66380a397a222bef23cba9e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750324"
---
# <a name="troubleshoot-activation-or-secret-deletion-issues-on-azure-key-vault-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスの Azure Key Vault でのアクティブ化とシークレットの削除に関する問題をトラブルシューティングする 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスのアクティブ化に関する問題をトラブルシューティングする方法について説明します。 


## <a name="activation-errors"></a>アクティブ化エラー

次の表は、デバイスのアクティブ化に関連するエラーと、対応する推奨される解決策をまとめたものです。

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| アクティブ化キーでデバイスをアクティブにする前に、アクティブ化に使用する Azure Key Vault を削除すると、このエラーが発生します。 <br> ![キー コンテナー エラー 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | キー コンテナーが削除された場合、コンテナーが消去防止期間内であれば、キー コンテナーを復元できます。 [キー コンテナーを回復する](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)手順に従います。 <br>消去防止期間が経過した場合は、[[キー コンテナーの回復]](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) ブレードを使用して新しいキー コンテナーを作成する必要があります。 |
| デバイスをアクティブにした後で Azure Key Vault を削除し、それから暗号化を含む操作 (**ユーザーの追加**、**共有の追加**、**Compute の構成** など) を実行しようとすると、このエラーが発生します。 <br> ![キー コンテナー エラー 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | キー コンテナーが削除された場合、コンテナーが消去防止期間内であれば、キー コンテナーを復元できます。 [キー コンテナーを回復する](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)手順に従います。 <br>消去防止期間が経過した場合は、[キー コンテナーの回復](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)に関するページの説明に従って新しいキー コンテナーを作成する必要があります。 |
| エラーが発生したためにアクティブ化キーの生成が失敗した場合、このエラーが発生します。 通知に詳しい情報が含まれています。 <br> ![キー コンテナー エラー 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | キー コンテナーにアクセスできるよう、[「ファイアウォールの向こう側にある Azure Key Vault へのアクセス」](../key-vault/general/access-behind-firewall.md)で指定されているポートと URL を、ファイアウォールで開いていることを確認します。 数分待ってから、再度操作を行います。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
| ユーザーの権限が読み取り専用である場合、このユーザーはアクティブ化キーを生成できず、このエラーが表示されます。 <br> ![キー コンテナー エラー 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | これは、適切なアクセス権を持っていないか、`Microsoft.KeyVault` が登録されていないことが原因である可能性があります。<li>Azure Stack Edge リソースに対して使用されるリソース グループ レベルで、所有者または共同作成者アクセス権があることを確認します。</li><li>`Microsoft.KeyVault` リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、Azure Stack Edge リソースに対して使用されているサブスクリプションに移動します。 **[リソース プロバイダー]** に移動し、*Microsoft.KeyVault* を探し、選択して **登録** します。 詳細については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」を参照してください。</li> |


## <a name="unregistered-resource-provider-errors"></a>未登録のリソース プロバイダー エラー

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| Key Vault リソース プロバイダーが登録されていない場合は、アクティブ化キーの生成中にキー コンテナーを作成するとエラーが表示されます。 <br> <!--![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | アクティブ化キーは生成されません。 <br>`Microsoft.KeyVault` リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、Azure Stack Edge リソースに対して使用されているサブスクリプションに移動します。 **[リソース プロバイダー]** に移動し、*Microsoft.KeyVault* を探し、選択して **登録** します。 <br>詳細については、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」を参照してください。</li> |
| ストレージ リソース プロバイダーが登録されていない場合は、監査ログのストレージ アカウントを作成するときにエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | このエラーはブロック エラーではなく、アクティブ化キーは生成されます。 <br>通常、ストレージ リソース プロバイダーは自動的に登録されますが、そうでない場合は、「[リソース プロバイダーを登録する](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)」の手順に従って、`Microsoft.Storage` をサブスクリプションに対して登録してください。  |

## <a name="key-vault-or-secret-deletion-errors"></a>キー コンテンツまたはシークレットの削除エラー

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| Azure Key Vault のチャネル整合性キーを削除して、暗号化を含む操作 (**ユーザーの追加**、**共有の追加**、**Compute の構成** など) を実行しようとすると、このエラーが発生します。  |キー コンテナーのチャネル整合性キーを削除した場合も、そのキーの消去期間を過ぎていなければ、[キー コンテナーのキーの削除操作を元に戻す](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)方法に関する記事の手順を実行してください。 <br>消去保護期間を過ぎても、キーのバックアップを取っていれば、そのバックアップからキーを復元できます。 それ以外の場合はキーを復元できません。 Microsoft サポートに手順をお問い合わせください。 |
<!--|Noopur を調べた後で、これを削除します。お客様は何も行う必要はありません。デバイスのアクティブ化の前に Azure Key Vault のチャネル整合性キーが検出され、アクティブ化キーが再生成された場合に、このエラーが表示されます。 <br> ![キー コンテナー エラー 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png) | チャネル整合性キーは再作成され、メタデータが更新されます。
|-->

## <a name="audit-logging-errors"></a>監査ログ エラー

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| キー コンテナーの診断設定の作成に失敗した場合は、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | これはブロック エラーではなく、アクティブ化キーは生成されます。 <br> 手動で、[監査ログを保存するための診断設定を作成](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)できます。 |
| たとえば、指定した名前のアカウントが既に存在するために、ストレージ アカウントの作成が失敗した場合、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | ストレージ アカウントを手動で作成し、キー コンテナー上の診断設定にリンクさせることができます。 このアカウントは、監査ログを格納するために使用されます。 <br> 詳しくは、「[ログ用のストレージ アカウントを作成する](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)」をご覧ください。  |
|Azure Stack Edge リソースのシステム割り当てマネージド ID が削除されると、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge リソースの [セキュリティ] ブレードにアラートが表示されます。 [[キー コンテナーの回復] ブレードを通じて新しいマネージド ID を作成](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)するにはこのアラートを選択します  |
| マネージド ID がキー コンテナーにアクセスできない場合、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | Azure Stack Edge リソースの [セキュリティ] ブレードにアラートが表示されます。 [[キー コンテナーの回復] ブレードを通じてキー コンテナーへのアクセス権をマネージド ID に付与](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)するには、このアラートを選択します。  |

## <a name="resource-move-errors"></a>リソースの移動エラー

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| キー コンテナー リソースがリソース グループまたはサブスクリプション間で移動されると、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | キー コンテナー リソースの移動は、キー コンテナーの削除と同じように扱われます。 コンテナーが消去防止期間内であれば、キー コンテナーを復元できます。 消去防止期間が経過した場合は、新しいキー コンテナーを作成する必要があります。 上記のいずれかのケースの詳細については、[キー コンテナーの回復](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)に関するページを参照してください。    |
| 使用しているサブスクリプションをテナント間で移動した場合、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | マネージド ID を再構成し、新しいキーコンテナーを作成します。 また、キー コンテナー リソースを移動することもできます。この場合、マネージド ID だけを再構成する必要があります。 上記のそれぞれのケースで、[キー コンテナーの回復](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault)に関するページを参照してください。   |
| 監査ログに使用されるストレージ アカウント リソースをリソース グループまたはサブスクリプション間で移動した場合、エラーは表示されません。  | [新しいストレージ アカウントを作成し、監査ログを保存するように構成](../key-vault/general/howto-logging.md#create-a-storage-account-for-your-logs)できます。 |

## <a name="other-errors"></a>その他のエラー

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| キー コンテナーに対してネットワークの制限が構成されている場合、このエラーが表示されます。 <!--<br> ![Key vault error 3](./media/azure-stack-edge-gpu-activation-key-vault/placeholder.png)--> | サービスでは、ネットワークの制限により、キー コンテナーの削除と、キー コンテナーにアクセスできないシナリオとを区別できません。 どちらの場合にも、[[キー コンテナーの回復]](azure-stack-edge-gpu-activation-key-vault.md#recover-key-vault) ブレードにリダイレクトされます。     |


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro with GPU をインストールする](azure-stack-edge-gpu-deploy-install.md)。
