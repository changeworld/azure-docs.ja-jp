---
title: "CLI を使用した Azure Container Service クラスターのデプロイ | Microsoft Docs"
description: "Azure CLI 2.0 プレビューを使用して Azure Container Service クラスターをデプロイします"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Azure CLI 2.0 プレビューを使用した Azure Container Service クラスターの作成

Azure Container Service クラスターを作成するには、次が必要です。
* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* [Azure CLI v.2.0 (プレビュー)](https://github.com/Azure/azure-cli#installation) をインストールする
* Azure アカウントへのログイン (下記参照)

## <a name="log-in-to-your-account"></a>アカウントへのログイン
```azurecli
az login 
```
この[リンク](https://login.microsoftonline.com/common/oauth2/deviceauth)にアクセスして、CLI で取得したデバイス コードで認証を行う必要があります。

![コマンドの入力](media/container-service-create-acs-cluster-cli/login.png)

![の作成](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>リソース グループの作成
```azurecli
az group create -n acsrg1 -l "westus"
```

![リソース グループ作成の画像](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>使用できる Azure Container Service CLI コマンドの一覧

```azurecli
az acs -h
```

![ACS コマンドの使用方法](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Azure Container Service クラスターの作成

"*CLI の ACS create の使用方法*"

```azurecli
az acs create -h
```
コンテナー サービスの名前、前の手順で作成したリソース グループ、一意の DNS 名は必須です。 その他の入力は、それぞれスイッチを使用して上書きしない限り、既定値に設定されます (ヘルプのスナップショットを含む次の画像を参照)。
![ACS create ヘルプの画像](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

"*既定値を使用した簡単な ACS create。SSH キーがない場合は 2 番目のコマンドを使用します。--generate-ssh-keys スイッチを指定したこの 2 番目の create コマンドにより、SSH キーが生成されます*"

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

"*DNS プレフィックス (-d スイッチ) が一意であることを確認してください。エラーが発生する場合、一意の文字列を使ってもう一度やり直してください。*"

上記のコマンドを入力した後、クラスターが作成されるまで約 10 分待ちます。

![ACS create の画像](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>ACS クラスターの一覧表示 

### <a name="under-a-subscription"></a>サブスクリプション内

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>特定のリソース グループ内

```azurecli
az acs list -g acsrg1 --output table
```

![ACS list の画像](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>コンテナー サービス クラスターの詳細の表示

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![ACS list の画像](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>ACS クラスターのスケール
"*スケールインとスケールアウトの両方が可能です。パラメーター new-agent-count は、ACS クラスターの新しいエージェントの数です。*"

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![ACS scale の画像](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>コンテナー サービス クラスターの削除
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
"*この delete コマンドでは、コンテナー サービスの作成中に作成されたリソース (ネットワークとストレージ) がすべて削除されるわけではないことに注意してください。すべてのリソースを削除する場合は、リソース グループごとに単一の ACS クラスターを作成してから、acs クラスターが必要なくなったときにリソース グループそのものを削除することをお勧めします。そうすることで、関連するリソースがすべて削除され、それらに対する課金を回避できます。*"



<!--HONumber=Nov16_HO3-->


