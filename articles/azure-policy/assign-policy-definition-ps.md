---
title: "PowerShell を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する | Microsoft Docs"
description: "PowerShell を使用して Azure Policy の割り当てを作成し、準拠していないリソースを特定します。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 1/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 67c779b96dab088d810d22ad3053ade106aec56a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>PowerShell を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイックスタートでは、ポリシー割り当てを作成して、管理ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、管理ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシー割り当てに*準拠していません*。

PowerShell は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、PowerShell を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する方法について詳しく説明します。

このガイドには、Azure PowerShell モジュール バージョン 4.0 以降が必要です。 バージョンを確認するには、 `Get-Module -ListAvailable AzureRM`  を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

開始する前に、最新バージョンの PowerShell がインストールされていることを確認してください。 詳細については、[Azure PowerShell の概要](/powershell/azureps-cmdlets-docs)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。


## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシー割り当てを作成し、"*管理ディスクのない仮想マシンを監査*" 定義を割り当てます。 このポリシー定義で、ポリシー定義に設定されている条件に準拠していないリソースを特定します。

次の手順で新しいポリシー割り当てを作成します。

1. サブスクリプションがリソース プロバイダーで確実に動作するようにするには、Policy Insights リソース プロバイダーを登録します。 リソース プロバイダーを登録するには、リソース プロバイダーのアクションの登録操作を実行するためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。

    リソース プロバイダーを登録する以下のコマンドを実行します。

    ```
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
```

    サブスクリプション内にリソース プロバイダーからのリソースの種類がある場合、そのリソース プロバイダーの登録を解除することはできません。

    リソース プロバイダーの登録と表示の詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」を参照してください。

2. リソース プロバイダーの登録後、次のコマンドを実行して、すべてのポリシー定義を表示し、割り当てる定義を探します。

    ```powershell
$definition = Get-AzureRmPolicyDefinition
```

    Azure Policy には、使用できる組み込みのポリシー定義があらかじめ含まれています。 次のような組み込みのポリシー定義が表示されます。

    - Enforce tag and its value (タグとその値を強制する)
    - タグとその値を適用
    - SQL Server バージョン 12.0 が必要

3. 次に、`New-AzureRmPolicyAssignment` コマンドレットを使用してポリシー定義を目的のスコープに割り当てます。

このチュートリアルでは、コマンドに対して次の情報を使用します。

- ポリシー割り当ての表示用の**名前**。 ここでは、"管理ディスクのない仮想マシンを監査" を使用します。
- **ポリシー** - 割り当ての作成に使用しているポリシー定義です。 ここでは、"*管理ディスクのない仮想マシンを監査*" というポリシー定義です
- **スコープ** - スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 この例では、ポリシー定義を **FabrikamOMS** リソース グループに割り当てています。
- **$definition** - ポリシー定義のリソース ID を指定する必要があります。この例では、ポリシー定義 "*管理ディスクのない仮想マシンを監査*" の ID を使用しています。

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

1. Azure Policy のランディング ページに戻ります。
2. 左側のウィンドウで **[Compliance]\(コンプライアンス\)** を選択し、作成した**ポリシー割り当て**を検索します。

   ![ポリシーのコンプライアンス](media/assign-policy-definition/policy-compliance.png)

   この新しい割り当てに準拠していない既存のリソースがある場合、**[準拠していないリソース]** タブの下に表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のガイドは、このクイックスタートに基づいています。 引き続きチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、次のコマンドを実行して、作成した割り当てを削除してください。

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

ポリシーの割り当てについて詳しく学び、**今後**作成されるリソースが準拠していることを確認するには、チュートリアルを続行してください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./create-manage-policy.md)
