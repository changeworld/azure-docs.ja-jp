---
title: チュートリアル - Azure Service Fabric mesh Web アプリケーションをデバッグする | Microsoft Docs
description: このチュートリアルでは、ローカル クラスター上で実行している Azure Service Fabric mesh アプリケーションをデバッグします。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ad6812f25ee33bf723ed86d4ec32ca6898d01774
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186740"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>チュートリアル: Service Fabric mesh Web アプリケーションをデバッグする

このチュートリアルはシリーズの第 2 部です。ローカル開発クラスター上の Azure Service Fabric mesh Web アプリケーションをデバッグする方法について説明します。

このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
> * Azure Service Fabric mesh アプリケーションをビルドする際の動作
> * サービス間の呼び出しを確認するためのブレークポイントを設定する方法

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Service Fabric mesh アプリケーションをビルドする](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * アプリをローカルでデバッグする
> * [アプリを Azure に発行する](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 がインストールされた[開発環境の設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)が済んでいることを確認します。

## <a name="download-the-to-do-sample-application"></a>To Do サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-mesh-tutorial-create-dotnetcore.md)で To Do サンプル アプリケーションを作成しなかった場合は、ダウンロードできます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

アプリケーションは `src\todolistapp` ディレクトリにあります。

## <a name="build-and-debug-on-your-local-cluster"></a>ローカル クラスターでビルドおよびデバッグする

プロジェクトが読み込まれると、すぐに自動で Docker イメージがビルドされ、ローカル クラスターにデプロイされます。 このプロセスにはしばらく時間がかかることがあります。 Visual Studio の **[出力]** ウィンドウ内で進行状況を確認するには、[出力] ウィンドウの **[出力元:]** ボックスの一覧を **[Service Fabric Tools]** に設定します。

**F5** キーを押して、サービスをローカルでコンパイルおよび実行します。 プロジェクトがローカルで実行およびデバッグされている場合、Visual Studio は次のように動作します。

* Docker for Windows が実行中で、コンテナーのオペレーティング システムとして Windows を使用するように設定されていることを確認してください。
* 不足している Docker ベースイメージをダウンロードします。 これには時間がかかる場合があります。
* コード プロジェクトをホストするために使用する Docker イメージをビルド (またはリビルド) します。
* ローカルの Service Fabric 開発クラスター上のコンテナーをデプロイおよび実行します。
* サービスを実行し、設定したブレークポイントに到達します。

ローカルのデプロイが完了し、Visual Studio がアプリを実行している状態になると、ブラウザー ウィンドウで既定のサンプル Web ページが開きます。

**デバッグのヒント**

* "**No Service Fabric local cluster is running**" (Service Fabric のローカル クラスターが実行されていません) というエラーが表示された場合、サービスのローカル クラスター マネージャー (SLCM) が実行中になっていることを確認し、タスク バーの SLCM アイコンを右クリックして、**[Start Local Cluster]\(ローカル クラスターを起動する\)** をクリックします。 起動したら、Visual Studio に戻り、**F5** キーを押します。
* アプリの起動時に **404** エラーが表示された場合には、**service.yaml** 内の環境変数が正しくない可能性があります。 `ApiHostPort` と `ServiceName` が「[環境変数を作成する](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables)」の手順に従って正しく設定されていることを確認します。
* **service.yaml** でビルド エラーが表示された場合、行のインデントにタブではなくスペースを使用していることを確認してください。 また、現在、アプリをビルドするときは、英語のロケールを使用する必要があります。

### <a name="debug-in-visual-studio"></a>Visual Studio でのデバッグ

Visual Studio で Service Fabric mesh アプリケーションをデバッグするときは、ローカルの Service Fabric 開発クラスターを使用します。 バックエンド サービスから To Do 項目が取得される方法を表示するには、OnGet() メソッドをデバッグします。
1. **WebFrontEnd** プロジェクト内で **[Pages]** > **[Index.cshtml]** > **[Index.cshtml.cs]** の順に開き、**Get** メソッド (17 行目) 内にブレークポイントを設定します。
2. **ToDoService** プロジェクト内で **[TodoController.cs]** を開き、**OnGet** メソッド (15 行目) 内にブレークポイントを設定します。
3. ブラウザーに戻り、ページを更新します。 Web フロントエンドの `OnGet()` メソッドでブレークポイントに到達します。 `backendUrl` 変数を検査すると、**service.yaml** ファイル内で定義した環境変数が、バックエンド サービスに接続するために使用する URL にどのように組み込まれているかを確認できます。
4. `client.GetAsync(backendUrl).GetAwaiter().GetResult())` の呼び出しをステップ オーバー (F10) し、コントローラーの `Get()` ブレークポイントに到達します。 このメソッドでは、メモリ内のリストから To Do 項目のリストがどのように取得されているかを確認できます。
5. 完了したら、Visual Studio で **Shift + F5** キーを押して、プロジェクトのデバッグを停止します。
 
## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Service Fabric mesh アプリケーションをビルドする際の動作
> * サービス間の呼び出しを確認するためのブレークポイントを設定する方法

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric mesh Web アプリケーションを発行する](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)