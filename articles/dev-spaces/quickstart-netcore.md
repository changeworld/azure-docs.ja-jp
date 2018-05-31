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
ms.openlocfilehash: 279b7a8c20717668c0ff4be541e9168e2d8706fd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361581"
---
# <a name="quickstart-create-a-kubernetes-development-environment-with-azure-dev-spaces-net-core-and-vs-code"></a>クイック スタート: Azure Dev Spaces (.NET Core および VS Code) を使用して Kubernetes 開発環境を作成する


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

クラスターが作成されるのを待っている間に、コードの開発を開始できます。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成
[.NET Core](https://www.microsoft.com/net) がインストールされている場合は、`webfrontend` という名前のフォルダーに ASP.NET Core Web アプリを迅速に作成できます。

```cmd
   dotnet new mvc --name webfrontend
```

または、**GitHub からサンプル コードをダウンロード**します。そのためには、https://github.com/Azure/dev-spaces に移動して、**[Clone or download]\(複製またはダウンロード\)** を選択し、GitHub リポジトリをローカル環境にダウンロードします。 このガイドのコードは、`samples/dotnetcore/getting-started/webfrontend` にあります。

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>コンテンツ ファイルを更新する
Azure Dev Spaces は、Kubernetes でコードを実行できるようにするだけでなく、コードの変更がクラウド内の Kubernetes 環境に反映されるのを迅速かつ反復的に確認できるようにします。

1. `./Views/Home/Index.cshtml` ファイルを見つけ、HTML を編集します。 たとえば、行 70 の `<h2>Application uses</h2>` を `<h2>Hello k8s in Azure!</h2>` のように変更します。
1. ファイルを保存します。 しばらくすると、ターミナル ウィンドウに、実行中のコンテナー内のファイルが更新されたことを示すメッセージが表示されます。
1. ブラウザーに移動し、ページを更新します。 Web ページに更新された HTML が表示されます。

なぜでしょうか? HTML や CSS などのコンテンツ ファイルを編集した場合、.NET Core Web アプリでの再コンパイルは必要ありません。そのため、アクティブな `azds up` コマンドによって、変更されたコンテンツ ファイルが Azure で実行中のコンテナーに自動的に同期されるので、コンテンツの編集をすぐに確認できます。

## <a name="update-a-code-file"></a>コード ファイルを更新する
コード ファイルを更新するには、.NET Core アプリを再ビルドし、更新されたアプリケーション バイナリを生成する必要があるため、もう少し作業が必要です。

1. ターミナル ウィンドウで `Ctrl+C` キーを押します (`azds up`を停止します)。
1. `Controllers/HomeController.cs` という名前のコード ファイルを開き、[About]\(バージョン情報\) ページに表示されるメッセージを `ViewData["Message"] = "Your application description page.";` のように編集します。
1. ファイルを保存します。
1. ターミナル ウィンドウで `azds up` を実行します。 

このコマンドにより、コンテナー イメージが再ビルドされ、Helm チャートが再デプロイされます。 コードの変更が実行中のアプリケーションに反映されていることを確認するには、Web アプリの [About]\(バージョン情報\) メニューに移動します。


コードを開発するには、さらに "*迅速な方法*" もあります。これについては、次のセクションで説明します。 

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>AZDS デバッグ構成を選択する
1. VS Code の左側の**アクティビティ バー**で [デバッグ] アイコンをクリックして、デバッグ ビューを開きます。
1. アクティブなデバッグ構成として **[.NET Core Launch (AZDS)]\(.NET Core の起動 (AZDS)\)** を選択します。

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> コマンド パレットに Azure Dev Spaces コマンドが表示されない場合は、Azure Dev Spaces 用 VS Code 拡張機能がインストールされていることを確認します。 VS Code で開いているワークスペースが、azds.yaml が含まれているフォルダーであることを確認してください。


### <a name="debug-the-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
**F5** キーを押して、Kubernetes でコードをデバッグします。

`up` コマンドと同様に、コードが開発環境に同期され、コンテナーがビルドされて Kubernetes にデプロイされます。 今度は、当然、デバッガーがリモート コンテナーに接続されます。

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

サーバー側のコード ファイル (たとえば、`Controllers/HomeController.cs` ソース ファイルの `Index()` 関数内) にブレークポイントを設定します。 ブラウザーのページを更新すると、ブレークポイントに到達します。

コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

### <a name="edit-code-and-refresh"></a>コードを編集して更新する
デバッガーをアクティブにしてコードを編集します。 たとえば、`Controllers/HomeController.cs` で [About]\(バージョン情報\) ページのメッセージを変更します。 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

ファイルを保存し、**デバッグ操作ウィンドウ**で **[最新の情報に更新]** ボタンをクリックします。 

![](media/get-started-netcore/debug-action-refresh.png)

コードを編集するたびに新しいコンテナー イメージを再ビルドして再デプロイすると、多くの場合、かなりの時間がかかります。Azure Dev Spaces では、既存のコンテナー内のコードを増分再コンパイルすることで、編集/デバッグのループを高速化します。

ブラウザーで Web アプリを更新し、[About]\(バージョン情報\) ページに移動します。 UI にカスタム メッセージが表示されます。

**これで、コードに対する操作を迅速に反復し、Kubernetes で直接デバッグすることができるようになりました。**

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](get-started-netcore.md#call-a-service-running-in-a-separate-container)