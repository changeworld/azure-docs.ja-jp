---
title: SAP デプロイ自動化フレームワークの PowerShell リファレンス |Microsoft Docs
description: Azure PowerShell リファレンス上の SAP デプロイ自動化フレームワーク
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: baff0193394e64a4699e519565117e1dd3996afa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725151"
---
# <a name="using-powershell-in-sap-deployment-automation-framework"></a>SAP デプロイ自動化フレームワークでの PowerShell の使用

Microsoft PowerShell を使用して、すべての[SAP デプロイ自動化フレームワークを Azure](automation-deployment-framework.md) コンポーネントにデプロイできます。

## <a name="control-plane-operations"></a>コントロール プレーン操作

[New-SAPAutomationRegion](module/automation-new-sapautomationregion.md) PowerShell コマンドを使って、コントロール プレーンをデプロイまたは更新することができます。

[`Remove-SAPAutomationRegion`](module/automation-remove-sapautomationregion.md) PowerShell コマンドを使用してコントロール プレーンを削除します。

[New-SAPDeployer](module/automation-new-sapdeployer.md) PowerShell シェル コマンドを使用して、コントロール プレーンで Deployer をブートストラップできます。

[New-SAPLibrary](module/automation-new-saplibrary.md) PowerShell シェル コマンドを使用して、コントロール プレーンで SAP ライブラリをブートストラップできます。

## <a name="workload-zone-operations"></a>ワークロード ゾーンの操作

[`New-SAPWorkloadZone`](module/automation-new-sapworkloadzone.md) PowerShell コマンドを使用して、ワークロード ゾーンをデプロイまたは更新します。

[`Remove-SAPSystem`](module/automation-remove-sapsystem.md) PowerShell コマンドを使用して、ワークロード ゾーンを削除します。


## <a name="sap-system-operations"></a>SAP システムの操作

[`New-SAPSystem`](module/automation-new-sapsystem.md) PowerShell コマンドを使用して、SAP システムをデプロイまたは更新します。

[`Remove-SAPSystem`](module/automation-remove-sapsystem.md) PowerShell コマンドを使用して、SAP システムを削除します。


## <a name="other-operations"></a>その他の操作

[`Set-SAPSecrets`](module/automation-set-sapsecrets.md) PowerShell コマンドを使用して、デプロイ資格情報を設定します。

[`Update-TFState`](module/automation-update-tfstate.md) PowerShell コマンドを使用して Terraform 状態ファイルを更新します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用したコントロール プレーンのデプロイ](module/automation-new-sapautomationregion.md)




