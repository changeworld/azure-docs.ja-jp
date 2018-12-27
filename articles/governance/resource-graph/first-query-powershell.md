---
title: Azure PowerShell を使用して最初のクエリを実行する
description: この記事では、Azure PowerShell の Resource Graph モジュールを有効にして、最初のクエリを実行する手順について説明します。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5ffc93afdfff1a069d00b61868b5ae025121198c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310727"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Azure PowerShell を使用して最初の Resource Graph クエリを実行します

Azure Resource Graph を使用する最初の手順では、Azure PowerShell のモジュールがインストールされていることを確認します。 このクイック スタートでは、Azure PowerShell のインストールにモジュールを追加するプロセスについて説明します。

このプロセスの最後では、選択した Azure PowerShell のインストールにモジュールを追加して、最初の Resource Graph クエリを実行しています。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="add-the-resource-graph-module"></a>Resource Graph モジュールを追加する

Azure PowerShell を Azure Resource Graph のクエリに対して有効にするには、モジュールを追加する必要があります。 このモジュールは、ローカルにインストールされた Windows PowerShell および PowerShell Core、または [Azure PowerShell Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell/)で使用できます。

### <a name="base-requirements"></a>基本要件

Azure Resource Graph モジュールには、次のソフトウェアが必要です。

- Azure PowerShell 6.3.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-azurerm-ps)に従ってください。

  - PowerShell Core については、Azure PowerShell モジュールの**Az**バージョンを使用してください。

  - Windows PowerShell については、Azure PowerShell モジュールの**AzureRm**バージョンを使用してください。

- PowerShellGet 2.0.1 以上。 インストールされていない場合、または更新されていない場合は、こちらの[手順](/powershell/gallery/installing-psget)に従ってください。

### <a name="cloud-shell"></a>Cloud Shell

Azure Resource Graph モジュールを Cloud Shell に追加するには、次の PowerShell Core 用の手順に従ってください。

### <a name="powershell-core"></a>PowerShell Core

PowerShell Core の Resource Graph モジュールは**Az.ResourceGraph**です。

1. **管理** PowerShell Core プロンプトで次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. モジュールがインポートされており、適切なバージョン (0.3.0) であることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. 次のコマンドを使用し、**Az**の下位のエイリアスを**AzureRm**に対して有効にします。

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Windows PowerShell の Resource Graph モジュールは**AzureRm.ResourceGraph**です。

1. **管理** Windows PowerShell プロンプトで次のコマンドを実行します。

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. モジュールがインポートされており、適切なバージョン (0.1.1-preview) であることを確認します。

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

Azure PowerShell モジュールが選択した環境に追加されたので、簡単な Resource Graph クエリを試してみましょう。 クエリは、各リソースの**名前**および**リソースの種類**を使用して、最初の 5 つの Azure リソースを返します。

1. `Search-AzureRmGraph` コマンドレットを使用して最初の Resource Graph クエリを実行します。

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `order by`**名前**プロパティに対するクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 これによりクエリの結果をまず制限し、それからそれらを注文します。

1. 最初に**名前**プロパティで並べ替え (`order by`) を行ってから結果を上位 5 件に制限 (`limit`) するようにクエリを更新します。

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

最終的なクエリを複数回実行したとき、環境内で何も変更がないと仮定すると、返される結果は一貫性があり、想定どおりになります。つまり、結果は**名前**プロパティで並べ替えられますが、上位 5 件に制限されます。

## <a name="cleanup"></a>クリーンアップ

Resource Graph モジュールを Azure PowerShell 環境から削除する場合は、次のコマンドを使用して行うことができます。

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> これにより、以前にダウンロードしたモジュールのファイルは削除されません。 実行中の PowerShell セッションから削除されるだけです。

## <a name="next-steps"></a>次の手順

- [クエリ言語](./concepts/query-language.md)に関するさらなる情報を入手します
- [その他のリソース](./concepts/explore-resources.md)
- [Azure CLI ](first-query-azurecli.md)を使用して最初のクエリを実行します
- [Starter クエリ](./samples/starter.md)のサンプルを参照してください
- [高度なクエリ](./samples/advanced.md)のサンプルを参照してください
- [UserVoice ](https://feedback.azure.com/forums/915958-azure-governance)にフィードバックを提供してください