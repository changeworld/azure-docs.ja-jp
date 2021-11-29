---
title: SAP on Azure デプロイ自動化フレームワークの概要
description: Azure で SAP デプロイ自動化フレームワークをすばやく使用開始します。 サンプル パラメーター ファイルを使用して構成例をデプロイします。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 29c7b229020306b085000ed0596814f1cb8e818e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725219"
---
# <a name="get-started-with-sap-automation-framework-on-azure"></a>Azure での SAP 自動化フレームワークの概要

[Azure で SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)をすばやく使用開始します。

## <a name="prerequisites"></a>前提条件


- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます。
- Azure 環境に [SAP ソフトウェアがダウンロードされている](automation-software.md)。
- [Terraform のインストール](https://www.terraform.io/)。 詳細については、「[Azure 上の Terraform のドキュメント](/azure/developer/terraform/)」も参照してください。
- ローカル コンピューターに [Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure/install-azure-cli) がインストールされている。
- (オプション) PowerShell を使用する場合:
    - ローカル コンピューターに [Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module) がインストールされている。
    - 最新の PowerShell モジュール。 必要に応じて、[PowerShell モジュールを更新します](/powershell/azure/install-az-ps#update-the-azure-powershell-module)。

いくつかの前提条件は、デプロイ環境に既にインストールされている場合があります。 Cloud Shell と配置機能の両方に、Terraform と Azure CLI がインストールされています。
## <a name="clone-the-repository"></a>リポジトリの複製

次の手順に従って、リポジトリを複製し、実行環境を準備します。

1. 自動化フレームワークのデプロイ用に `Azure_SAP_Automated_Deployment` というディレクトリを作成します。 

# <a name="linux"></a>[Linux](#tab/linux)

```bash
mkdir ~/Azure_SAP_Automated_Deployment; cd $_
git clone https://github.com/Azure/sap-hana.git 
```

次の手順に従って、環境を準備します。

```bash
export DEPLOYMENT_REPO_PATH=~/Azure_SAP_Automated_Deployment/sap-hana
export ARM_SUBSCRIPTION_ID=<subscriptionID>
```
> [!NOTE]
> サンプルの値 `<subscriptionID>` を必ず実際の値に置き換えてください。

サンプル構成ファイルをコピーして、デプロイ自動化フレームワークのテストを開始することができます。

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-hana/deploy/samples/WORKSPACES WORKSPACES

```


# <a name="windows"></a>[Windows](#tab/windows)

```powershell
mkdir C:\Azure_SAP_Automated_Deployment
    
cd Azure_SAP_Automated_Deployment
    
git clone https://github.com/Azure/sap-hana.git 
```

PowerShell モジュールをインポートします

```powershell
Import-Module             C:\Azure_SAP_Automated_Deployment\sap-hana\deploy\scripts\pwsh\SAPDeploymentUtilities\Output\SAPDeploymentUtilities\SAPDeploymentUtilitiespsd1
```

---

> [!TIP]
> [SAP デプロイ自動化フレームワーク リポジトリ](https://github.com/Azure/sap-hana)は、配置機能によって既に複製されています。 

## <a name="copy-the-samples"></a>サンプルをコピーする

サンプル構成ファイルをコピーして、デプロイ自動化フレームワークのテストを開始することができます。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-hana/deploy/samples/WORKSPACES WORKSPACES
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
cd C:\Azure_SAP_Automated_Deployment
mkdir WORKSPACES

xcopy sap-hana\deploy\samples\WORKSPACES WORKSPACES
```

---


## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [展開を計画する](automation-plan-deployment.md)
