---
title: インクルード ファイル
description: インクルード ファイル
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 11/05/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: e066c97e12f4b8e34f66235f53857c583e4350a9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135886"
---
Azure Machine Learning は、パブリック インターネットへの受信アクセスと送信アクセスの両方が必要です。 次の表に、必要なアクセスの概要と、その目的を示します。 すべての項目の __プロトコル__ は __TCP__ です。 `.region` で終わるサービス タグでは、`region` を、ご利用のワークスペースを含む Azure リージョンに置き換えます。 `Storage.westus` の例を次に示します。

| Direction | ポート | サービス タグ | 目的 |
| ----- |:-----:| ----- | ----- |
| 受信 | 29876 から 29877 | BatchNodeManagement | Azure Machine Learning コンピューティング インスタンスとコンピューティング クラスターを作成、更新、削除します。 |
| 受信 | 44224 | AzureMachineLearning | Azure Machine Learning コンピューティング インスタンスを作成、更新、削除します。 |
| 送信 | 80、443 | AzureActiveDirectory | Azure Active Directory を使用した認証。 |
| 送信 | 443 | AzureMachineLearning | Azure Machine Learning service の使用。 |
| 送信 | 443 | AzureResourceManager | Azure Machine Learning による Azure リソースの作成。 |
| 送信 | 443 | Storage.region | Microsoft Azure Batch サービスの Azure Storage アカウントの格納データにアクセスします。 |
| 送信 | 443 | AzureFrontDoor.FrontEnd</br>* Azure China では不要です。 | [Azure Machine Learning スタジオ](https://ml.azure.com)用のグローバル エントリ ポイント。 | 
| 送信 | 443 | ContainerRegistry.region | Microsoft が提供する Docker イメージにアクセスします。 |
| 送信 | 443 | MicrosoftContainerRegistry.region | Microsoft が提供する Docker イメージにアクセスします。 Azure Kubernetes Service の Azure Machine Learning ルートのセットアップ。 |
| 送信 | 443 | Keyvault.region | Azure Batch サービスのキー コンテナーにアクセスします。 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) フラグを有効にしてワークスペースが作成された場合にのみ必要です。 |

> [!TIP]
> サービス タグの代わりに IP アドレスが必要な場合、次のいずれかのオプションを使用します。
> * [Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)から一覧をダウンロードします。
> * Azure CLI の [az network list-service-tags](/cli/azure/network#az_network_list_service_tags) コマンドを使用します。
> * Azure PowerShell の [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) コマンドを使用します。
> 
> IP アドレスは定期的に変わる可能性があります。

機械学習プロジェクトで必要なパッケージをインストールするために、Visual Studio Code サイトと Microsoft 以外のサイトへの __送信__ トラフィックを許可する必要がある場合があります。 次の表に、機械学習に一般的に使用されるリポジトリを示します。

| ホスト名 | 目的 |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
| **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
| **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |
| **cloud.r-project.org** | R 開発用の CRAN パッケージをインストールするときに使用されます。 |
| **\*pytorch.org** | PyTorch に基づくいくつかのサンプルによって使用されます。 |
| **\*.tensorflow.org** | Tensorflow に基づくいくつかのサンプルによって使用されます。 |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | セットアップ スクリプトを通じてコンピューティング インスタンスにインストールされている VS Code サーバー ビットを取得するために使用されます。|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | コンピューティング インスタンスにインストールされている Websocket サーバー ビットを取得するために使用されます。 Websocket サーバーは、Visual Studio Code クライアント (デスクトップ アプリケーション) から、コンピューティング インスタンスで実行されている Visual Studio Code サーバーに要求を送信するために使用されます。|

Azure Machine Learning で Azure Kubernetes Service (AKS) を使用する場合は、AKS VNet への次のトラフィックを許可します。

* 「[Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する](../articles/aks/limit-egress-traffic.md)」で説明されている AKS の受信または送信の一般的な要件。
* mcr.microsoft.com への __送信__。
* AKS クラスターにモデルをデプロイする場合は、「[ML モデルを Kubernetes Service にデプロイする](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md#connectivity)」記事のガイダンスを使用してください。