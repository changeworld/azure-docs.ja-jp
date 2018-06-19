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
ms.openlocfilehash: 484567dd9d9c3d050e7be25bd685a5b8d3de0687
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825526"
---
## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes でコードをビルドして実行する
コードを実行してみましょう。 ターミナル ウィンドウで、**ルート コード フォルダー**の webfrontend から次のコマンドを実行します。

```cmd
azds up
```

コマンドの出力に注目してください。進行するにつれて、次のようなことに気付きます。
- ソース コードが、Azure の開発空間と同期します。
- コード フォルダーで Docker 資産によって指定されているように、コンテナー イメージが Azure でビルドされます。
- コード フォルダーで Helm チャートによって指定されているように、コンテナー イメージを活用する Kubernetes オブジェクトが作成されます。
- コンテナーのエンドポイントに関する情報が表示されます。 ここでは、パブリック HTTP URL が表示されるはずです。
- 上記の段階が正常に完了していれば、コンテナーの起動時に`stdout` (および `stderr`) 出力の表示が開始されるはずです。

> [!Note]
> `up` コマンドを初めて実行する場合、以下の手順には時間がかかりますが、それ以降はもっと早く実行できます。

### <a name="test-the-web-app"></a>Web アプリをテストする
コンソールの出力をスキャンして、`up` コマンドで作成されたパブリック URL に関する情報を探します。 形式は次のようになります。 

`Running at public URL: http://<servicename>-<cluster-name>.<guid>.<region>.aksapp.io` 

ブラウザー ウィンドウでこの URL を開くと、Web アプリ ロードが表示されるはずです。 コンテナーが実行されると、`stdout` と `stderr` の出力がターミナル ウィンドウにストリーミングされます。
