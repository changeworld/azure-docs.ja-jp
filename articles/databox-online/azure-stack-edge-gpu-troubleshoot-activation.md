---
title: Azure portal を使用して Azure Stack Edge Pro と GPU に関連するアクティブ化エラーをトラブルシューティングする | Microsoft Docs
description: Azure Stack Edge Pro GPU のアクティブ化とキー コンテナー関連の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/09/2021
ms.author: alkohli
ms.openlocfilehash: abb941fc84a0d4473ce03969e8c5718e8aeb41ce
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982360"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスのアクティブ化に関する問題をトラブルシューティングする 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスのアクティブ化に関する問題をトラブルシューティングする方法について説明します。 


## <a name="activation-errors"></a>アクティブ化エラー

次の表は、デバイスのアクティブ化に関連するエラーと、対応する推奨される解決策をまとめたものです。

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| アクティブ化キーでデバイスをアクティブにする前に、アクティブ化に使用する Azure Key Vault を削除すると、このエラーが発生します。 <br> ![キー コンテナー エラー 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | キー コンテナーが削除されている場合、コンテナーが消去防止期間内であれば、キー コンテナーを回復できます。 [キー コンテナーを回復する](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)手順に従います。 <br>消去防止期間が経過した場合、キー コンテナーを回復することはできません。 Microsoft サポートに手順をお問い合わせください。 |
| デバイスをアクティブにした後で Azure Key Vault を削除し、それから暗号化を含む操作 (**ユーザーの追加**、**共有の追加**、**Compute の構成** など) を実行しようとすると、このエラーが発生します。 <br> ![キー コンテナー エラー 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | キー コンテナーが削除されている場合、コンテナーが消去防止期間内であれば、キー コンテナーを回復できます。 キー コンテナーを回復する手順に従います。 <br>消去防止期間が経過した場合、キー コンテナーを回復することはできません。 Microsoft サポートに手順をお問い合わせください。 |
| Azure Key Vault の Channel Integrity Key を削除して、暗号化を含む操作 (**ユーザーの追加**、**共有の追加**、**Compute の構成** など) を実行しようとすると、このエラーが発生します。 <br> ![キー コンテナー エラー 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | キー コンテナーの Channel Integrity Key を削除してしまった場合も、そのキーの消去期間を過ぎていなければ、[キー コンテナーのキーの削除操作を元に戻す](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)方法に関する記事の手順を実行してください。 <br>消去保護期間を過ぎても、キーのバックアップを取っていれば、そのバックアップからキーを復元できます。 それ以外の場合はキーを復元できません。 Microsoft サポートに手順をお問い合わせください。 |
| エラーが発生したためにアクティブ化キーの生成が失敗した場合、このエラーが発生します。 通知に詳しい情報が含まれています。 <br> ![キー コンテナー エラー 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | キー コンテナーにアクセスできるよう、[「ファイアウォールの向こう側にある Azure Key Vault へのアクセス」](../key-vault/general/access-behind-firewall.md)で指定されているポートと URL を、ファイアウォールで開いていることを確認します。 数分待ってから、再度操作を行います。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
| ユーザーの権限が読み取り専用である場合、このユーザーはアクティブ化キーを生成できず、このエラーが表示されます。 <br> ![キー コンテナー エラー 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | これは、適切なアクセス権を持っていないか、*Microsoft.KeyVault* が登録されていないことが原因である可能性があります。<li>Azure Stack Edge リソースに対して使用されるリソース グループ レベルで、所有者または共同作成者アクセス権があることを確認します。</li><li>Microsoft.KeyVault リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、Azure Stack Edge リソースに対して使用されているサブスクリプションに移動します。 **[リソース プロバイダー]** に移動し、*Microsoft.KeyVault* を探し、選択して **登録** します。</li> |

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro with GPU をインストールする](azure-stack-edge-gpu-deploy-install.md)。
