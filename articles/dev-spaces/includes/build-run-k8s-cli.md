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
ms.openlocfilehash: b18cfce173da562aa7cffa48f336ff623c868f21
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129268"
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

`Service 'webfrontend' port 'http' is available at <url>` 

ブラウザー ウィンドウでこの URL を開くと、Web アプリ ロードが表示されるはずです。 コンテナーが実行されると、`stdout` と `stderr` の出力がターミナル ウィンドウにストリーミングされます。

> [!Note]
> 最初の実行では、パブリック DNS が準備されるまでに数分かかることがあります。 公開 URL が解決されない場合は、コンソール出力に表示される代替 http://localhost:<portnumber> URL を使用することができます。 localhost URL を使用する場合、コンテナーはローカルで実行されているように見えるかもしれませんが、実際には AKS で実行されています。 利便性を考慮し、また、ローカル コンピューターからのサービスとの対話を容易にするために、Azure Dev Spaces では、Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されます。 後で DNS レコードが準備できたら、戻って公開 URL を試してみることができます。
