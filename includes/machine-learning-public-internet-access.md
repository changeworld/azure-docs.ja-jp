---
title: インクルード ファイル
description: インクルード ファイル
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443396"
---
Azure Machine Learning は、パブリック インターネットへの受信アクセスと送信アクセスの両方が必要です。 次の表に、必要なアクセスの概要と、その目的を示します。 すべての項目の __プロトコル__ は __TCP__ です。 `.region` で終わるサービス タグでは、`region` を、ご利用のワークスペースを含む Azure リージョンに置き換えます。 `Storage.westus` の例を次に示します。

| Direction | ポート | サービス タグ | 目的 |
| ----- |:-----:| ----- | ----- |
| 受信 | 29876 から 29877 | BatchNodeManagement | Azure Machine Learning コンピューティング インスタンスおよびコンピューティング クラスター。 |
| 受信 | 44224 | AzureMachineLearning | Azure Machine Learning コンピューティング インスタンス。 |
| 送信 | * | AzureActiveDirectory | Azure Active Directory 認証。 |
| 送信 | 443 | AzureMachineLearning | Azure Machine Learning |
| 送信 | 443 | AzureResourceManager | Azure Resource Manager。 |
| 送信 | 443 | Storage.region | Azure ストレージ アカウント |
| 送信 | 443 | AzureFrontDoor.FrontEnd</br>* Azure China では不要です。 | Azure Front Door。 | 
| 送信 | 443 | ContainerRegistry.region | Azure Container Registry。 カスタム Docker イメージを使用する場合にのみ必要です。 Microsoft が提供する基本イメージへの小さな変更 (追加のパッケージなど) を含む。 |
| 送信 | 443 | MicrosoftContainerRegistry.region | Microsoft が提供する Docker イメージを使用し、ユーザーによって管理される依存関係を有効にする場合にのみ必要です。 |

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