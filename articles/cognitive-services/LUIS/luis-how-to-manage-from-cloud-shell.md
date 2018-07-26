---
title: Azure Cloud Shell から LUIS の使用状況を表示する | Microsoft Docs
description: Azure Cloud Shell で LUIS の使用状況に関する情報を取得する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: diberry
ms.openlocfilehash: f9d3a489328263bd300d60935bfde2f0dfc0245c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224043"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Azure Cloud Shell から LUIS サービスを管理する
Azure portal で PowerShell コマンドレットを使って、LUIS リソースを操作することができます。 

これらのコマンドレットを使うと、LUIS サブスクリプションを[作成](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0)し、サブスクリプションに関する[使用状況](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0)などの情報を取得し、サブスクリプションを[削除](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0)することができます。 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud Shell のストレージ アカウントと認証
Azure portal の [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell) で PowerShell を使うには、Azure ストレージ アカウントが必要です。 [ストレージ アカウント](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share)がない場合は、作成するよう要求されます。 ストレージ アカウントがあると、Cloud Shell に PowerShell スクリプトを保存することができます。  

また、リソースにアクセスするには、Cloud Shell 内の Azure に対して認証を行う必要があります。 

ストレージ アカウントがあり、認証が済んでいれば、PowerShell コマンドレットを実行することができます。

## <a name="open-cloud-shell"></a>Cloud Shell を開く
Azure portal の Cloud Shell を使用するとき、PowerShell のバージョンは常に最新です。 

**[Launch Cloud Shell]** ボタンを使って Cloud Shell を開くか、または [https://shell.azure.com](https://shell.azure.com) でブラウザーを開きます。 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>LUIS エンドポイントの使用状況情報

PowerShell 6.x コマンドレット `Get-AzureRmCognitiveServicesAccountUsage` は、LUIS などの Microsoft Cognitive Services の使用状況の情報を提供します。 [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) を実行するには、サービスのリソース グループとリソース名が必要です。 

コマンド構文は次のとおりです。

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

次の例で、リソース グループ名は `luis-westus-rg`、LUIS サービスのサブスクリプション名は `luis-westus-1` です。 これらの名前はどちらも、LUIS サービスの作成時に選択されます。 

このコマンドレットは、6 月 7 日に終わる 30 日の期間内に使用された 10,000 エンドポイント ヒットのうちの 16 の使用情報を返します。

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Cloud Shell に関連付けられている Azure ストレージ アカウントに PowerShell ファイル *.ps1 としてコマンドを保存すると、いつでも実行できます。 

![ストレージからスクリプトを実行する](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

スクリプトをクラウドのドライブに保存した後は、Azure 電話アプリの Cloud Shell から PowerShell スクリプトを実行できます。 

![電話アプリのストレージからスクリプトを実行する](./media/luis-how-to-manage-from-powershell/phone-app.png)