---
title: Azure Stack ユーザーに対する Azure CLI の有効化 | Microsoft Docs
description: クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: c2827a4badd61aeb8de556795834dee39769e85e
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554505"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure Stack ユーザーに対する Azure CLI の有効化

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack のユーザーに CA ルート証明書を提供して、開発マシンで Azure CLI を使用できるようにすることができます。 ユーザーが CLI を使用してリソースを管理するには証明書が必要になります。

* ユーザーが Azure Stack Development Kit の外部のワークステーションから CLI を使用する場合は、**Azure Stack の CA ルート証明書**が必要です。  

* **仮想マシンのエイリアス エンドポイント**は、VM をデプロイするときに、1 つのパラメーターとしてイメージ発行者、プラン、SKU、およびバージョンを参照する "UbuntuLTS" または "Win2012Datacenter" などのエイリアスを提供します。  

これらの値の取得方法については、以下のセクションで説明します。

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書をエクスポートする

Azure Stack の CA ルート証明書は、開発キットと、開発キット環境内で実行されているテナント仮想マシンで見つかります。 PEM 形式で Azure Stack ルート証明書をエクスポートするには、開発キットまたはテナント仮想マシンにサインインし、次のスクリプトを実行します。

```powershell
$label = "<Your Azure Stack CA root certificate name>"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを設定する

Azure Stack オペレーターは、仮想マシンのエイリアス ファイルをホストする、公的にアクセス可能なエンドポイントを設定する必要があります。 仮想マシンのエイリアス ファイルは、イメージの共通名が指定された JSON ファイルです。 この名前は、Azure CLI パラメーターとして VM をデプロイするときに後で指定します。  

エイリアス ファイルにエントリを追加する前に、[Azure Marketplace からイメージをダウンロード](azure-stack-download-azure-marketplace-item.md)するか、[独自のカスタム イメージを発行](azure-stack-add-vm-image.md)してください。 カスタム イメージを発行する場合は、発行時に指定した発行者、プラン、SKU、およびバージョン情報をメモしておいてください。 Marketplace のイメージである場合は、```Get-AzureVMImage``` コマンドレットを使用して情報を表示できます。  

多くの一般的なイメージの別名を含む[サンプル エイリアス ファイル](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)が使用できます。 それを出発点として使用できます。 このファイルを CLI クライアントがアクセスできる場所にホストします。 そのためには、BLOB ストレージ アカウント内でファイルをホストし、ユーザーと URL を共有する方法があります。

1. GitHub から[サンプル ファイル](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)をダウンロードします。
2. Azure Stack に新しいストレージ アカウントを作成します。 完了したら、新しい BLOB コンテナーを作成します。 アクセス ポリシーを [パブリック] に設定します。  
3. その新しいコンテナーに JSON ファイルをアップロードします。 完了したら、BLOB 名を選択し、BLOB のプロパティから URL を選択すると、BLOB の URL を表示できます。

## <a name="next-steps"></a>次の手順

- [Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)
- [PowerShell で接続する](azure-stack-connect-powershell.md)
- [ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)
