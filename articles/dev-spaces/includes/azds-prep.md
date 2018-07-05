---
title: インクルード ファイル
description: インクルード ファイル
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938174"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Docker および Kubernetes 開発用コードの準備
ここまでで、ローカルで実行できる基本的な Web アプリを用意しました。 ここで、アプリのコンテナーと、それを Kubernetes にデプロイする方法を定義するアセットを作成して、それをコンテナー化します。 このタスクは Azure Dev Spaces で行うのが容易です。 

1. VS Code を起動し、`webfrontend` フォルダーを開きます。 (デバッグ アセットの追加やプロジェクトの復元を行うための既定のプロンプトはすべて無視できます。)
1. VS Code で統合ターミナルを開きます (**[表示]、[統合ターミナル]** メニューを使用)。
1. このコマンドを実行します (**webfrontend** が現在のフォルダーであることを確認します)。

    ```cmd
    azds prep --public
    ```

Azure CLI の `azds prep` コマンドにより、既定の設定で Docker と Kubernetes のアセットが生成されます。
* `./Dockerfile` は、アプリのコンテナー イメージと、ソース コードがどのようにコンテナー内でビルドされ、実行されるかを記述しています。
* `./charts/webfrontend` の下の [Helm チャート](https://docs.helm.sh)は、Kubernetes にコンテナーを展開する方法を記述しています。

ここでは、これらのファイルの内容をすべて理解する必要はありません。 ただし、開発から運用まで、**コードとしての構成である同じ Kubernetes および Docker アセットを使用できるため、異なる環境にわたってより高い一貫性が提供されることに注意してください。**
 
`./azds.yaml` という名前のファイルが `prep` コマンドによって生成されます。これが Azure Dev Spaces の構成ファイルです。 これは、Azure で反復開発エクスペリエンスを実現する追加の構成によって、Docker と Kubernetes の成果物を補完するものです。
