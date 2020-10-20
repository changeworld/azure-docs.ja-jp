---
title: PowerShell:FHIR Server for Azure をデプロイする - Azure API for FHIR
description: このクイックスタートでは、PowerShell を使用して Microsoft のオープンソースの FHIR サーバーをデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817957"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>クイック スタート:PowerShell を使用してオープンソースの FHIR サーバーをデプロイする

このクイックスタートでは、PowerShell を使用して、オープンソースの Microsoft FHIR Server for Azure をデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

プロビジョニングしたリソースの追加先となるリソース グループの名前を選んで、リソース グループを作成します。

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>FHIR サーバー テンプレートをデプロイする

Microsoft FHIR Server for Azure の [GitHub リポジトリ](https://github.com/Microsoft/fhir-server)には、必要なリソースをすべてデプロイするテンプレートが存在します。 デプロイ プロセスには、必要な Azure リソースの作成と構成が伴うため数分かかります。 デプロイには、次のコマンドを使用します。

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> まだログインしていない場合は、先に次のコマンドを実行してください。

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

既存のリソース グループを使用するには、次のコードに示したように、$rg 変数が定義されている行で $rg の値を変更し、Azure ARM テンプレートのデプロイ コマンド ラインを変更します。

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>FHIR サーバーが実行されていることを確認する

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

サーバーから最初の応答が返されるまでに 1 分ほど時間がかかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ご利用のサブスクリプションに Microsoft Open Source FHIR Server for Azure をデプロイしました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](access-fhir-postman-tutorial.md)
