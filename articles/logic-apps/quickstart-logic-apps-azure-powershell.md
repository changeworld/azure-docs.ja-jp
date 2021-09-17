---
title: クイック スタート - マルチテナント Azure Logic Apps で Azure PowerShell を使用してワークフローを作成して管理する
description: PowerShell を使用して、マルチテナント Azure Logic Apps でロジック アプリ ワークフローを作成して管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2
ms.date: 07/26/2021
ms.openlocfilehash: 42f6ac6ce06f1b852af2027e6ef79f347d8050a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787388"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>クイック スタート: マルチテナント Azure Logic Apps で Azure PowerShell を使用してワークフローを作成して管理する

このクイックスタートでは、[Azure PowerShell](/powershell/azure/install-az-ps) を使用してロジック アプリを作成し、管理する方法について説明します。 ロジック アプリ ワークフロー定義用の JSON ファイルを使用して、PowerShell からロジック アプリを作成することができます。 その後、[Az.LogicApp](/powershell/module/az.logicapp/) PowerShell モジュールでコマンドレットを実行して、ロジック アプリを管理できます。

Azure Logic Apps を初めて使用する場合は、[Azure portal](quickstart-create-first-logic-app-workflow.md)、[Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)、および [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) を使用して初めてのロジック アプリを作成する方法を学習することもできます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ローカル コンピューターにインストールされている [Az PowerShell](/powershell/azure/install-az-ps) モジュール。
* ロジック アプリを作成する [Azure リソース グループ](#example---create-resource-group)。

### <a name="prerequisite-check"></a>前提条件のチェック

開始する前に、環境を検証します。

* Azure portal にサインインし、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を実行して、ご利用のサブスクリプションがアクティブであることを確認します。
* `Get-InstalledModule -Name Az` を実行して Azure PowerShell のバージョンを確認します。 最新バージョンについては、[最新のリリース ノート](/powershell/azure/migrate-az-6.0.0)を参照してください。
  * 最新バージョンでない場合は、「[Azure PowerShell モジュールの更新](/powershell/azure/install-az-ps#update-the-azure-powershell-module)」に従ってインストールを更新します。

### <a name="example---create-resource-group"></a>例 - リソース グループの作成

ロジック アプリのリソース グループがまだない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを実行してグループを作成します。 たとえば、次のコマンドを実行すると、場所 `westus` に `testResourceGroup` という名前のリソース グループが作成されます。

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

リソース グループが正常に作成された場合、出力には、`ProvisioningState` が `Succeeded` として示されます。

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>ワークフロー定義

Azure PowerShell を使用して、[新しいロジック アプリを作成する](#create-logic-apps-from-powershell)か、または[既存のロジック アプリを更新する](#update-logic-apps-from-powershell)前に、ロジック アプリのワークフロー定義が必要です。 Azure portal で、**デザイナー** ビューから **コード ビュー** に切り替えると、ロジック アプリの基になるワークフロー定義を JSON 形式で表示できます。

コマンドを実行してロジック アプリを作成または更新する場合、ワークフロー定義はパラメーター セットに応じて必須パラメーター (`Definition`) または (`DefinitionFilePath`) としてアップロードされます。 ワークフロー定義は、[ワークフロー定義言語のスキーマ](./logic-apps-workflow-definition-language.md)に従う JSON ファイルとして作成する必要があります。

## <a name="create-logic-apps-from-powershell"></a>PowerShell からロジック アプリを作成する

コマンドレット [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) と定義の JSON ファイルを使用して、Azure PowerShell からロジック アプリのワークフローを作成できます。

### <a name="example---create-logic-app"></a>例 - ロジック アプリの作成

この例では、`testLogicApp` という名前のワークフローを場所 `westus` にあるリソース グループ `testResourceGroup` に作成します。 JSON ファイル `testDefinition.json` には、ワークフロー定義が含まれています。

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

ワークフローが正常に作成されると、PowerShell によって、新しいワークフロー定義が表示されます。

## <a name="update-logic-apps-from-powershell"></a>PowerShell からロジック アプリを更新する

コマンドレット [`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp) を使用して、Azure PowerShell からロジック アプリのワークフローを更新することもできます。

### <a name="example---update-logic-app"></a>例 - ロジック アプリの更新

この例では、別の JSON 定義ファイル `newTestDefinition.json` を使用して、[前のセクションで作成したサンプル ワークフロー](#example---create-logic-app)を更新する方法を示します。

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

ワークフローが正常に更新されると、PowerShell によって、ロジック アプリの更新されたワークフロー定義が表示されます。

## <a name="delete-logic-apps-from-powershell"></a>PowerShell からロジック アプリを削除する

コマンドレット [`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp) を使用して、Azure PowerShell からロジック アプリのワークフローを削除することができます。

### <a name="example---delete-logic-app"></a>例 - ロジック アプリの削除

この例では、[前のセクションで作成したサンプル ワークフロー](#example---create-logic-app)を削除します。

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

確認プロンプトに `y` で応答すると、ロジック アプリが削除されます。

### <a name="considerations---delete-logic-app"></a>考慮事項 - ロジック アプリの削除

ロジック アプリを削除すると、ワークフロー インスタンスに次のような影響が生じます。

* 進行中および保留中の実行があれば、それらのキャンセルを Logic Apps サービスがベスト エフォートで試みます。

  大量のボリュームやバックログがあったとしても、ほとんどの実行は完了前または開始前にキャンセルされます。 ただし、キャンセル プロセスは完了までに時間がかかる場合があります。 その間、ランタイムがキャンセル プロセスに対処する傍ら、いくつかの実行が実行対象として選択されてしまう可能性があります。

* Logic Apps サービスは、新しいワークフロー インスタンスを作成することも実行することもありません。

* ワークフローを削除してから同じワークフローを再作成しても、再作成されたワークフローに、削除したワークフローと同じメタデータが割り当てられることはありません。 削除したワークフローの呼び出し元となったワークフローを再保存する必要があります。 これにより、呼び出し元は、再作成されたワークフローの正しい情報を取得します。 それ以外の場合、再作成したワークフローの呼び出しは、`Unauthorized` エラーで失敗します。 この動作は、統合アカウントのアーティファクトを使用するワークフローや、Azure 関数を呼び出すワークフローにも当てはまります。

## <a name="show-logic-apps-in-powershell"></a>PowerShell でロジック アプリを表示する

コマンド [`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp) を使用して、特定のロジック アプリ ワークフローを取得することができます。

### <a name="example---get-logic-app"></a>例 - ロジック アプリの取得

この例では、リソース グループ `testResourceGroup` 内のロジック アプリ `testLogicApp` が返されます。

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)をご覧ください。

その他の Logic Apps スクリプトのサンプルについては、[Microsoft のコード サンプル ブラウザー](/samples/browse/?products=azure-logic-apps)を参照してください。