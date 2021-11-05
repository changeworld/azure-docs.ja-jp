---
title: ARM テンプレートと Bicep ファイルのトラブルシューティングの概要
description: Azure Resource Manager テンプレート (ARM テンプレート) と Bicep ファイルを使用した Azure リソースのデプロイのトラブルシューティングについて説明します。
ms.topic: overview
ms.date: 10/26/2021
ms.custom: troubleshooting-overview
ms.openlocfilehash: 604ba2be3fc1adcf22df9774400237a1b07f93e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092246"
---
# <a name="what-is-deployment-troubleshooting"></a>デプロイのトラブルシューティングの概要

Bicep ファイルまたは Azure Resource Manager テンプレート (ARM テンプレート) のデプロイ時に、エラーが発生する場合があります。 このドキュメントは、エラーの考えられる解決策を見つけるのに役立ちます。

## <a name="error-types"></a>エラーの種類

発生する可能性のあるエラーには、**検証エラー** と **デプロイ エラー** の 2 種類があります。

検証エラーは、デプロイが開始される前に発生します。 これらのエラーは、現在の Azure 環境を操作しなくても確認できます。 たとえば、検証により、デプロイを開始する前に、関数の構文エラーや欠落している引数がわかります。

デプロイ エラーは、デプロイを試行し、Azure 環境を操作することでのみ確認できます。 たとえば、仮想マシン (VM) にはネットワーク インターフェイス カード (NIC) が必要です。 VM のデプロイ時に NIC が存在しない場合は、デプロイ エラーが発生します。

## <a name="troubleshooting-tools"></a>トラブルシューティング ツール

デプロイの前に構文エラーを特定するには、最新バージョンの [Visual Studio Code](https://code.visualstudio.com) を使用します。 次のいずれかの最新バージョンをインストールします。

* [Bicep 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)
* [Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

デプロイのトラブルシューティングを行う場合、リソース プロバイダーのプロパティまたは API バージョンについて学習すると役立ちます。 詳細については、「[Bicep と ARM テンプレートを使用してリソースを定義する](/azure/templates)」を参照してください。

テンプレート開発のベスト プラクティスに従うには、次のいずれかを使用します。

* [Bicep リンター](../bicep/linter.md)
* [ARM テンプレート テスト ツールキット](../templates/test-toolkit.md)

デプロイ時に、Azure portal のリソース グループの **[デプロイ]** または **[アクティビティ ログ]** でエラーの原因を確認できます。 Azure PowerShell を使用している場合は、[Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) や [Get-AzActivityLog](/powershell/module/az.monitor/get-azactivitylog) などのコマンドを使用します。 Azure CLI の場合は、[az deployment operation group](/cli/azure/deployment/operation/group) や [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) などのコマンドを使用します。

## <a name="next-steps"></a>次のステップ

- エラー コードに基づく解決策については、「[一般的な Azure デプロイ エラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
- エラー コードの見つけ方の概要については、「[クイックスタート: ARM テンプレートのデプロイのトラブルシューティング](quickstart-troubleshoot-arm-deployment.md)」または「[クイックスタート: Bicep ファイルのデプロイのトラブルシューティング](quickstart-troubleshoot-bicep-deployment.md)」を参照してください。
