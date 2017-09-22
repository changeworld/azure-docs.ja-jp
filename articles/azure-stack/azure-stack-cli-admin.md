---
title: "Azure Stack ユーザーに対する CLI の有効化 | Microsoft Docs"
description: "クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法について説明します"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sngun
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 2f7615e0f0928f4ef70f98b7e2b2dce823621314
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="enable-cli-for-azure-stack-users"></a>Azure Stack ユーザーに対する CLI の有効化

CLI を使用して実行できる Azure Stack オペレーターの特定のタスクはありません。 ただし、ユーザーが CLI を使用してリソースを管理できるようにするには、Azure Stack オペレーターがユーザーに以下を提供する必要があります。

* **Azure Stack の CA ルート証明書** - ユーザーが Azure Stack Development Kit の外部のワークステーションから CLI を使用する場合は、ルート証明書が必要です。  

* **仮想マシンのエイリアス エンドポイント** - CLI を使用して仮想マシンを作成するには、このエンドポイントが必要です。

これらの値の取得方法については、以下のセクションで説明します。

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書をエクスポートする

Azure Stack の CA ルート証明書は、開発キットと、開発キット環境内で実行されているテナント仮想マシンで利用できます。 開発キットまたはテナント仮想マシンにサインインし、次のスクリプトを実行して、PEM 形式で Azure Stack ルート証明書をエクスポートします。

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを設定する

Azure Stack オペレーターは、仮想マシンのイメージ エイリアスを含む、公的にアクセス可能なエンドポイントを設定する必要があります。 Azure Stack オペレーターは、イメージをイメージ エイリアス エンドポイントに追加する前に、[そのイメージを Azure Stack Marketplace にダウンロード](azure-stack-download-azure-marketplace-item.md)する必要があります。
   
たとえば、Azure では、https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json という URI を使用します。 オペレーターはマーケットプレースで使用可能なイメージを使用して、Azure Stack 用に同様のエンドポイントを設定する必要があります。

## <a name="next-steps"></a>次のステップ

[Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[PowerShell で接続する](azure-stack-connect-powershell.md)

[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)


