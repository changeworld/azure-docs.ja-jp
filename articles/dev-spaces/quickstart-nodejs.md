---
title: クラウドに Kubernetes 開発環境を作成する | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: quickstart
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 9c9a485a5c59342149027798e118b97b7305c640
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361534"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-nodejs"></a>クイック スタート: Azure Dev Spaces (Node.js) を使用して Kubernetes 開発環境を作成する


[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Azure で Kubernetes ベースの開発環境を作成する準備ができました。

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Azure CLI のインストール
Azure Dev Spaces には、ローカル マシンの最小限のセットアップが必要です。 開発環境の構成の大半はクラウドに保存され、他のユーザーと共有することができます。 まず、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) をダウンロードして実行します。 

> [!IMPORTANT]
> Azure CLI が既にインストールされている場合は、バージョン 2.0.32 以降を使用していることを確認してください。

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

環境が作成されるのを待つ間に、コードの記述を開始することができます。

## <a name="create-a-nodejs-container-in-kubernetes"></a>Kubernetes に Node.js コンテナーを作成する

このセクションでは、Node.js Web アプリを作成し、Kubernetes のコンテナーで実行します。

### <a name="create-a-nodejs-web-app"></a>Node.js Web アプリを作成する
GitHub からコードをダウンロードします。そのためには、https://github.com/Azure/dev-spaces に移動して **[Clone or download]\(複製またはダウンロード\)** を選択し、GitHub リポジトリをローカル環境にダウンロードします。 このガイドのコードは、`samples/nodejs/getting-started/webfrontend` にあります。

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>コンテンツ ファイルを更新する
Azure Dev Spaces は、Kubernetes でコードを実行できるようにするだけでなく、コードの変更がクラウド内の Kubernetes 環境に反映されるのを迅速かつ反復的に確認できるようにします。

1. `./public/index.html` ファイルを見つけ、HTML を編集します。 たとえば、ページの背景色を青の網掛けに変更します。

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. ファイルを保存します。 しばらくすると、ターミナル ウィンドウに、実行中のコンテナー内のファイルが更新されたことを示すメッセージが表示されます。
1. ブラウザーに移動し、ページを更新します。 色が更新されます。

なぜでしょうか? HTML や CSS などのコンテンツ ファイルの編集では、Node.js プロセスを再起動する必要がないので、アクティブな `azds up` コマンドは変更されたコンテンツ ファイルを、Azure で実行中のコンテナーに自動的に直接同期させます。そのため、コンテンツの編集をすばやく確認することができます。

### <a name="test-from-a-mobile-device"></a>モバイル デバイスからテストする
モバイル デバイスで Web アプリを開くと、小さなデバイスでは UI が正しく表示されないことがわかります。

これを解決するには、`viewport` メタ タグを追加します。
1. `./public/index.html` ファイルを開きます。
1. 既存の `head` 要素に `viewport` メタ タグを追加します。

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. ファイルを保存します。
1. デバイスのブラウザーを更新します。 Web アプリが正しく表示されます。 

これは、いくつかの問題はアプリが使用されるデバイスでテストするまで見つからないという例です。 VS Azure Dev Spaces を使用すると、コードに対する反復作業を迅速に行い、ターゲット デバイスでの変化を検証できます。

### <a name="update-a-code-file"></a>コード ファイルを更新する
サーバー側のコード ファイルを更新するには、Node.js アプリを再起動する必要があるため、もう少し作業が必要です。

1. ターミナル ウィンドウで `Ctrl+C` キーを押します (`azds up`を停止します)。
1. `server.js` という名前のコード ファイルを開き、サービスの hello メッセージを編集します。 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. ファイルを保存します。
1. ターミナル ウィンドウで `azds up` を実行します。 

コンテナー イメージが再構築され、Helm チャートが再デプロイされます。 ブラウザーのページを読み込み直して、コードの変更を反映させます。

コードを開発するには、さらに "*迅速な方法*" もあります。これについては、次のセクションで説明します。 

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>AZDS デバッグ構成を選択する
1. VS Code の左側の**アクティビティ バー**で [デバッグ] アイコンをクリックして、デバッグ ビューを開きます。
1. アクティブなデバッグ構成として **[Launch Program (AZDS)]\(プログラムの起動 (AZDS)\)** を選択します。

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> コマンド パレットに Azure Dev Spaces コマンドが表示されない場合は、Azure Dev Spaces 用 VS Code 拡張機能がインストールされていることを確認します。

### <a name="debug-the-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
**F5** キーを押して、Kubernetes でコードをデバッグします。

`up` コマンドと同様に、デバッグを開始するとコードが開発環境に同期され、コンテナーがビルドされて Kubernetes にデプロイされます。 今度は、デバッガーがリモート コンテナーに接続されます。

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

サーバー側のコード ファイル (たとえば、`server.js` の `app.get('/api'...` 内) にブレークポイントを設定します。 ブラウザーのページを更新するか、[Say It Again]\(繰り返す\) ボタンをクリックすると、ブレークポイントに到達し、コードをステップ実行することができます。

コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

### <a name="edit-code-and-refresh-the-debug-session"></a>コードを編集し、デバッグ セッションを更新する
デバッガーをアクティブにして、コードを編集します。たとえば、hello メッセージをもう一度次のように変更します。

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

ファイルを保存し、**デバッグ操作ウィンドウ**で **[最新の情報に更新]** ボタンをクリックします。 

![](media/get-started-node/debug-action-refresh-nodejs.png)

コードを編集するたびに新しいコンテナー イメージを再ビルドして再デプロイすると、多くの場合、かなりの時間がかかります。代わりに、Azure Dev Spaces ではデバッグ セッション間に Node.js プロセスを再開することで、編集/デバッグ ループを高速化します。

ブラウザーで Web アプリを更新するか、*[Say It Again]\(繰り返す\)* ボタンをクリックします。 UI にカスタム メッセージが表示されます。

### <a name="use-nodemon-to-develop-even-faster"></a>NodeMon を使用して開発をさらに迅速化する
*NodeMon* は、Node.js 開発者が開発を迅速に行うために使用する一般的なツールです。 開発者は、サーバー側でコードを編集するたびに Node プロセスを手動で再開するのではなく、多くの場合、Node プロジェクトを構成して、*nodemon* によってファイルの変更が監視され、サーバー プロセスが自動的に再開されるようにします。 このスタイルの作業では、開発者はコードの編集後にブラウザーを更新するだけです。

Azure Dev Spaces を使用すると、ローカルでの開発時と同じ開発ワークフローの多くを使用することができます。 そのことを示すために、サンプル `webfrontend` プロジェクトは *nodemon* を使用するように構成されています (`package.json` では開発依存関係として構成されています)。

次の手順を試してみてください。
1. VS Code デバッガーを停止します。
1. VS Code の左側の**アクティビティ バー**で [デバッグ] アイコンをクリックします。 
1. アクティブなデバッグ構成として、**[Attach (AZDS)]\(アタッチ (AZDS)\)** を選択します。
1. F5 キーを押します。

この構成では、コンテナーは *nodemon* を起動するように構成されています。 サーバー コードが編集されると、ローカルで開発する場合と同様に、*nodemon* によって Node プロセスが自動的に再開されます。 
1. `server.js` で hello メッセージをもう一度編集し、ファイルを保存します。
1. ブラウザーを更新するか、*[Say It Again]\(繰り返す\)* ボタンをクリックして、変更が反映されることを確認します。

**これで、コードに対する操作を迅速に反復し、Kubernetes で直接デバッグすることができるようになりました。**

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](get-started-nodejs.md#call-a-service-running-in-a-separate-container)