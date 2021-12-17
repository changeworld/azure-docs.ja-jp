---
title: SAP デプロイ自動化フレームワークの Bash リファレンス |Microsoft Docs
description: Azure Bash リファレンス上の SAP デプロイ自動化フレームワーク
services: virtual-machines-windows
author: kimforss
manager: kimforss
keywords: Azure, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.workload: infrastructure
ms.date: 11/17/2021
ms.author: kimforss
ms.openlocfilehash: efb02895ef6d25bd94a1d0a8f8dcf11d2e0d5181
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725210"
---
# <a name="using-sap-deployment-automation-framework-shell-scripts"></a>SAP デプロイ自動化フレームワーク シェル スクリプトの使用

シェル スクリプトを使用して、すべての[SAP デプロイ自動化フレームワークを Azure](automation-deployment-framework.md) コンポーネントにデプロイできます。

## <a name="control-plane-operations"></a>コントロール プレーン操作

コントロール プレーンをデプロイまたは更新するには、[prepare_region](bash/automation-prepare-region.md) シェル スクリプトを使用します。

[remove_region](bash/automation-remove-region.md) シェル スクリプトを使用してコントロール プレーンを削除します。

[install_deployer](bash/automation-install_deployer.md) シェル スクリプトを使用して、コントロール プレーンで Deployer をブートストラップできます。

[install_deployer](bash/automation-install_library.md) シェル スクリプトを使用して、コントロール プレーンで SAP ライブラリをブートストラップできます。

## <a name="workload-zone-operations"></a>ワークロード ゾーンの操作

[`install_workloadzone`](bash/automation-install_workloadzone.md) シェル スクリプトを使用して、ワークロード ゾーンをデプロイまたは更新します。

[`remover`](bash/automation-remover.md) シェル スクリプトを使用して、ワークロード ゾーンを削除します。


## <a name="sap-system-operations"></a>SAP システムの操作

[`installer`](bash/automation-installer.md) シェル スクリプトを使用して、SAP システムをデプロイまたは更新します。

[`remover`](bash/automation-remover.md) シェル スクリプトを使用して、SAP システムを削除します。


## <a name="other-operations"></a>その他の操作

[`Set SPN secrets`](bash/automation-set-secrets.md) シェル スクリプトを使用して、デプロイ資格情報を設定します。

[`Update Terraform state`](bash/automation-advanced_state_management.md) シェル スクリプトを使用して Terraform 状態ファイルを更新します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [bash を使用したコントロール プレーンのデプロイ](bash/automation-prepare-region.md)
