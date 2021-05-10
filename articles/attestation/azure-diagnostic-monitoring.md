---
title: Azure Attestation の Azure 診断監視
description: Azure Attestation の Azure 診断監視
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168350"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Azure Attestation のトラステッド プラットフォーム モジュール (TPM) エンドポイントを使用して診断を設定する

この記事は、プラットフォーム メトリックとプラットフォーム ログをさまざまな送信先に送信するための診断設定を作成して構成するのに役立ちます。 Azure のアクティビティ ログとリソース ログを含む Azure の[プラットフォーム ログ](/azure/azure-monitor/platform/platform-logs-overview)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 [プラットフォーム メトリック](/azure/azure-monitor/platform/data-platform-metrics)は、既定で収集され、Azure Monitor メトリック データベースに格納されます。

開始する前に、[Azure PowerShell を使用して Azure Attestation を設定](quickstart-powershell.md)していることを確認してください。

トラステッド プラットフォーム モジュール (TPM) エンドポイント サービスは、診断設定で有効になっており、アクティビティの監視に使用できます。 次のコードを使用して、TPM サービス エンドポイントに対する [Azure Monitor による監視](/azure/azure-monitor/overview)を設定します。

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

アクティビティ ログは、ストレージ アカウントの **[コンテナー]** セクションにあります。 詳細については、[Azure リソースからリソース ログを収集して分析する](/azure/azure-monitor/learn/tutorial-resource-logs)方法に関するページを参照してください。
