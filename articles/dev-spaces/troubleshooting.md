---
title: トラブルシューティング | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: b66e43c0f40f184bfb2c62327f5742346ff8b187
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841611"
---
# <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

このガイドでは、Azure Dev Spaces 使用時の一般的な問題についての情報を示します。

## <a name="enabling-detailed-logging"></a>詳細なログ記録の有効化

問題のトラブルシューティングをより効果的に行ううえで、レビュー用のより詳細なログを作成することが役に立つ場合があります。

Visual Studio 拡張機能では、`MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境変数を 1 に設定することで、これを実行できます。 環境変数を有効にするために Visual Studio を再起動してください。 有効になったら、詳細なログが `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` ディレクトリに書き込まれます。

CLI では、`--verbose` スイッチを使用してコマンド実行中に詳細な情報を出力できます。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>エラー "Azure Dev Spaces コントローラーを作成できませんでした"

コントローラーの作成で問題が発生した場合に、このようなエラーが表示されることがあります。 これが一時的なエラーの場合は、コントローラーを削除して再作成することでエラーが修正されます。

### <a name="try"></a>次の操作を試してください。

コントローラーを削除するには、Azure Dev Spaces の CLI を使用します。 Visual Studio や Cloud Shell でこれを行うことはできません。 AZDS CLI をインストールするには、まず、Azure CLI をインストールして、以下のコマンドを実行します。

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

次に、以下のコマンドを実行してコントローラーを削除します。

```cmd
azds remove -g <resource group name> -n <cluster name>
```

コントローラーの再作成は、CLI や Visual Studio から行うことができます。 初めての場合と同じように、チュートリアルの手順に従ってください。


## <a name="error-service-cannot-be-started"></a>エラー "サービスを開始できません。"

サービス コードを起動できない場合、このエラーが表示されることがあります。 通常、原因はユーザー コードです。 詳しい診断情報を取得するには、コマンドや設定に次の変更を行います。

### <a name="try"></a>次の操作を試してください。

コマンド ラインで次の操作を行います。

_azds.exe_ を使用した場合、--verbose コマンド ライン オプションを使用し、--output コマンド ライン オプションを使用して出力形式を指定します。
 
    ```cmd
    azds up --verbose --output json
    ```

Visual Studio で次の操作を行います。

1. **[ツール] > [オプション]** を開き、**[Projects and Solutions]\(プロジェクトとソリューション\)** で、**[Build and Run]\(ビルドおよび実行\)** を選択します。
2. **[MSBuild プロジェクト ビルドの出力の詳細]** の設定を **[詳細]** または **[診断]** に変更します。

    ![ツール オプション ダイアログのスクリーンショット](media/common/VerbositySetting.PNG)
    
## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces サービスに関連付けられたパブリック URL で DNS の名前解決が失敗します。

これが発生するときは、Dev Spaces サービスに関連付けられたパブリック URL に接続を試行するときに、Web ブラウザーに「ページを表示できない」または「このサイトにリーチできない」のエラーが表示される場合があります。

### <a name="try"></a>次の操作を試してください。

次のコマンドを使用して、お使いの Dev Spaces サービスに関連付けられたすべての URL をリストアップします。

```cmd
azds list-uris
```

URL が*保留中*の状態にある場合、これはまだ Dev Spaces が DNS の登録が完了するのを待っている状態にあることを示します。 これが発生するには数分かかる場合があります。 また、Dev Spaces は各サービスの localhost トンネルを開き、これを DNS の登録を待っている間に使用できます。

URL が*保留中*状態の期間が 5 分を超える場合、公開エンドポイントを作成する外部 DNS ポッドや、公開エンドポイントを取得する nginx 入力コントローラー ポッドに問題がある可能性があります。 次のコマンドを使用して、これらのポッドを削除できます。 これは自動的に再作成されます。

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>エラー "必要なツールと構成が見つからない"

このエラーは、VS Code を起動するときに発生する場合があります。"[[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing.]\([Azure Dev Spaces] '[プロジェクト名]' をビルドして出バックするために必要なツールと構成がありません。\)"
エラーは、VS Code に示されるように、azds.exe が PATH 環境変数に無いことを意味します。

### <a name="try"></a>次の操作を試してください。

PATH 環境変数が正しく設定されているコマンド プロンプトから VS Code を起動します。

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>エラー 'azds' が、内部または外部コマンド、操作可能プログラム、またはバッチ ファイルとして認識されません
 
azds.exe が正しくインストールされていないか、構成されていない場合に、このエラーが表示されることがあります。

### <a name="try"></a>次の操作を試してください。

1. %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (プレビュー) で azds.exe を探してください。 見つかった場合は、その場所を PATH 環境変数に追加します。
2. azds.exe がインストールされていない場合は、次のコマンドを実行します。

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告 "Dockerfile could not be generated due to unsupported language"
Azure Dev Spaces では、C# と Node.js がネイティブでサポートされます。 これらのいずれかの言語で記述されたコードが含まれているディレクトリで *azds prep* を実行すると、Azure Dev Spaces によって適切な Dockerfile が自動的に作成されます。

その他の言語で記述されたコードを使って Azure Dev Spaces を使用することもできますが、初めて *azds up* を実行する前に、Dockerfile を自分で作成しておく必要があります。

### <a name="try"></a>次の操作を試してください。
Azure Dev Spaces でネイティブにサポートされる言語でアプリケーションが記述されていない場合、コードが実行されるコンテナー イメージをビルドするために適切な Dockerfile を設定する必要があります。 これを実行する際には、Docker によって用意された、[Dockerfile の記述に関するベスト プラクティスの一覧](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)と [Dockerfile リファレンス](https://docs.docker.com/engine/reference/builder/)が役に立ちます。

適切な Dockerfile を用意できたら、*azds up* の実行に進んで、Azure Dev Spaces でアプリケーションを実行できます。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>エラー 'upstream connect error or disconnect/reset before headers'
このエラーは、サービスへのアクセスを試みたときに発生する場合があります。 たとえば、ブラウザーでサービスの URL にアクセスしたときです。 

### <a name="reason"></a>理由 
コンテナーのポートが使用できません。 この問題は、以下の理由で発生する可能性があります。 
* コンテナーがまだビルドとデプロイの処理中です。 `azds up` を実行またはデバッガーを起動した後、コンテナーが正常にデプロイされる前にコンテナーにアクセスしようとした場合、この問題が発生する可能性があります。
* _Dockerfile_、Helm チャート、およびポートを開くサーバー コード間でポート構成が整合していません。

### <a name="try"></a>次の操作を試してください。
1. コンテナーがビルド/デプロイ処理中の場合、2 ～ 3 秒待ってからサービスへのアクセスを再試行できます。 
1. ポート 構成を確認します。 次のすべてのアセットで、指定されたポート番号が**同一**である必要があります:
    * **Dockerfile:** `EXPOSE` 命令によって指定されます。
    * **[Helm チャート](https://docs.helm.sh):** サービスの `externalPort` および `internalPort` 値 (多くの場合、`values.yml` ファイルにある) で指定されます。
    * Node.js などのアプリケーション コードで開かれているポートがあります: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>構成ファイルが見つかりません
`azds up` を実行すると次のエラーが表示されます: `Config file not found: .../azds.yaml`

### <a name="reason"></a>理由
実行するコードのルート ディレクトリから `azds up` を実行する必要があります。また、Azure Dev Spaces で実行するようにコード フォルダーを初期化する必要があります。

### <a name="try"></a>次の操作を試してください。
1. サービス コードが含まれているルート フォルダーにカレント ディレクトリを変更します。 
1. _azds.yaml_ ファイルがコード フォルダーにない場合、`azds prep` を実行して Docker、Kubernetes、および Azure Dev Spaces アセットを生成します。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>エラー: 'The pipe program 'azds' exited unexpectedly with code 126.'
VS Code デバッガーを起動すると、このエラーが発生する場合があります。 これは既知の問題です。

### <a name="try"></a>次の操作を試してください。
1. VS Code を閉じて、もう一度開きます。
2. もう一度 F5 キーを押します。

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>デバッグ エラー 'Failed to find debugger extension for type:coreclr'
VS Code デバッガーを実行すると次のエラーが報告されます: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>理由
.NET Core (CoreCLR) のデバッグ サポートを含む C# 用 VS Code 拡張機能がお使いの開発マシンにインストールされていません。

### <a name="try"></a>次の操作を試してください。
[C# 用 VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)をインストールします。

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>デバッグ エラー 'Configured debug type 'coreclr' is not supported'
VS Code デバッガーを実行すると次のエラーが報告されます: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>理由
Azure Dev Spaces 用の VS Code 拡張機能が開発用コンピューターにインストールされていません。

### <a name="try"></a>次の操作を試してください。
[Azure Dev Spaces 用の VS Code 拡張機能](get-started-netcore.md)をインストールします。

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>型または名前空間名 'MyLibrary' が見つかりませんでした

### <a name="reason"></a>理由 
ビルド コンテキストは既定でプロジェクト/サービスレベルのため、使用しているライブラリ プロジェクトが見つかりません。

### <a name="try"></a>次の操作を試してください。
必要な手順:
1. _azds.yaml_ ファイルを変更して、ビルド コンテキストをソリューション レベルに設定します。
2. _Dockerfile_ および _Dockerfile.develop_ ファイルを変更して、新しいビルド コンテキストからの相対位置でプロジェクト (_.csproj_) ファイルを正しく参照します。
3. _.dockerignore_ ファイルを .sln ファイルと同じ場所に置き、必要に応じて変更します。

https://github.com/sgreenmsft/buildcontextsample に例があります

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 承認エラー
Azure Dev Space を管理していて、所有者または共同作成者のアクセス権がない Azure サブスクリプションを操作しているとき、次のエラーが発生する場合があります。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>理由
選択した Azure サブスクリプションが `Microsoft.DevSpaces` 名前空間を登録していません。

### <a name="try"></a>次の操作を試してください。
Azure サブスクリプションの所有者または共同作成者のアクセス権を持つユーザーが、次の Azure CLI コマンドを実行して `Microsoft.DevSpaces` 名前空間を手動で登録できます。

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces が既存の Dockerfile を使用してコンテナーをビルドしていないと思われる 

### <a name="reason"></a>理由
プロジェクト内の特定の _Dockerfile_ を指示するように Azure Dev Spaces を構成できます。 Azure Dev Spaces が、想定した _Dockerfile_ を使用してコンテナーをビルドしていないと思われる場合、その場所を Azure Dev Spaces に明示的に知らせることが必要な可能性があります。 

### <a name="try"></a>次の操作を試してください。
プロジェクトで Azure Dev Spaces によって生成された _azds.yaml_ ファイルを開きます。 `configurations->develop->build->dockerfile` ディレクティブを使用して、使用する Dockerfile を指示します:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
