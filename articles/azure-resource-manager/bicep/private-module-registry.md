---
title: Bicep モジュール用のプライベート レジストリを作成する
description: プライベート Bicep モジュール用に Azure コンテナー レジストリを設定する方法について説明します
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: b667a80a155355ea206c4804d6eb98200491e3f8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551370"
---
# <a name="create-private-registry-for-bicep-modules-preview"></a>Bicep モジュール用のプライベート レジストリを作成する (プレビュー)

組織内で[モジュール](modules.md)を共有するには、プライベート モジュール レジストリを作成します。 そのレジストリにモジュールを公開し、モジュールをデプロイする必要があるユーザーに読み取りアクセス権を与えます。 レジストリでモジュールを共有した後は、Bicep ファイルからそれらを参照できます。

モジュール レジストリを使用するには、Bicep CLI バージョン **0.4.1008 以降** が必要です。

## <a name="configure-private-registry"></a>プライベート レジストリを構成する

Bicep レジストリは [Azure Container Registry (ACR)](../../container-registry/container-registry-intro.md) でホストされます。 モジュール用にレジストリを構成するには、以下の手順を使用します。

1. コンテナー レジストリが既に存在する場合は、それを使用できます。 コンテナー レジストリを作成する必要がある場合は、「[クイック スタート: Bicep ファイルを使用したコンテナー レジストリの作成](../../container-registry/container-registry-get-started-bicep.md)」を参照してください。 

   モジュール レジストリには、使用可能な任意のレジストリ SKU を使用できます。 レジストリ [geo レプリケーション](../../container-registry/container-registry-geo-replication.md)を使用すると、ユーザーにローカル プレゼンスまたはホット バックアップが提供されます。

1. ログイン サーバー名を取得します。 この名前は、Bicep ファイルからレジストリにリンクするときに必要です。 

   ログイン サーバー名を取得するには、[Get-AzContainerRegistry](/powershell/module/az.containerregistry/get-azcontainerregistry) を使用します。

   ```azurepowershell
   Get-AzContainerRegistry -ResourceGroupName "<resource-group-name>" -Name "<registry-name>"  | Select-Object LoginServer
   ```

   または、[az acr show](/cli/azure/acr#az_acr_show) を使用します。

   ```azurecli
   az acr show --resource-group <resource-group-name> --name <registry-name> --query loginServer
   ```

   ログイン サーバー名の形式は `<registry-name>.azurecr.io` です。

- レジストリにモジュールを公開するには、イメージを **プッシュする** アクセス許可が必要です。 レジストリからモジュールをデプロイするには、イメージを **プルする** アクセス許可が必要です。 適切なアクセス権を付与するロールの詳細については、「[Azure Container Registry のロールとアクセス許可](../../container-registry/container-registry-roles.md)」を参照してください。

- モジュールのデプロイに使用するアカウントの種類によっては、どの資格情報を使用するかのカスタマイズが必要な場合があります。 これらの資格情報は、レジストリからモジュールを取得するために必要です。 既定では、資格情報は Azure CLI または Azure PowerShell から取得されまし。 **bicepconfig.json** ファイルで資格情報を取得するための優先順位をカスタマイズできます。 詳細については、「[モジュールを復元するための資格情報](bicep-config-modules.md#credentials-for-restoring-modules)」を参照してください。

- セキュリティを強化するために、プライベート エンドポイント経由のアクセスを要求することができます。 詳細については、「[Azure Private Link を使用して Azure Container Registry にプライベートで接続する](../../container-registry/container-registry-private-link.md)」を参照してください。

## <a name="publish-files-to-registry"></a>レジストリにファイルを公開する

コンテナー レジストリを設定した後、そこにファイルを公開できます。 [publish](bicep-cli.md#publish) コマンドを使用し、モジュールとして使用する Bicep ファイルを指定します。 レジストリ内のモジュールのターゲットの場所を指定します。

```azurecli
az bicep publish storage.bicep --target br:exampleregistry.azurecr.io/bicep/modules/storage:v1
```

## <a name="view-files-in-registry"></a>レジストリ内のファイルを表示する

公開済みのモジュールをポータルで表示するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **コンテナー レジストリ** を検索します。
1. お使いのレジストリを選択します。
1. 左側のメニューから **[リポジトリ]** を選択します。
1. モジュール パス (リポジトリ) を選択します。  前の例では、モジュール パス名は **bicep/modules/storage** です。
1. タグを選択します。 前の例では、タグは **v1** です。
1. **[成果物参照]** が、Bicep ファイルで使用する参照と一致します。

   ![Bicep モジュール レジストリの成果物参照](./media/private-module-registry/bicep-module-registry-artifact-reference.png)

これで、レジストリ内のファイルを Bicep ファイルから参照する準備ができました。 外部モジュールを参照するために使用する構文の例については、「[Bicep モジュール](modules.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* モジュールの詳細については、「[Bicep モジュール](modules.md)」を参照してください。
* モジュール レジストリのエイリアスを構成するには、「[Bicep 構成ファイルにモジュールの設定を追加する](bicep-config-modules.md)」を参照してください。
* モジュールの公開と復元の詳細については、「[Bicep CLI コマンド](bicep-cli.md)」を参照してください。
