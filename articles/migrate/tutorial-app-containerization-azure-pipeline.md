---
title: Azure DevOps を使用したコンテナー化されたアプリケーションの継続的配置
description: 'チュートリアル: Azure DevOps を使用したコンテナー化されたアプリケーションの継続的配置'
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: rahugup
ms.openlocfilehash: 7d23b7c84cc7a7053bfa80b3f6e1b24c3bd4470c
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000570"
---
# <a name="continuous-deployment-for-containerized-applications-with-azure-devops"></a>Azure DevOps を使用したコンテナー化されたアプリケーションの継続的配置

このステップ バイ ステップ ガイドでは、Azure DevOps で Day 2 Operations を行うために、コンテナー化されたアプリを継続的にビルドしてデプロイするパイプラインを作成する方法について説明します。 Dockerfile を含むリポジトリ内のコードを変更すると、イメージが Azure Container Registry にプッシュされ、マニフェストが Azure Kubernetes Service または Azure App Service にデプロイされます。

Azure DevOps を使用すると、無料のワークフローを使用してコードをホスト、ビルド、計画、テストできます。 これらのワークフローの 1 つとして Azure Pipelines を使用すると、任意のプラットフォームとクラウドで機能する CI/CD によってアプリケーションをデプロイできます。 パイプラインは、リポジトリのルート ディレクトリ内の YAML ファイルとして定義されます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

-  Azure Migrate App Containerization を使用して、ASP.NET または Java Web アプリをコンテナー化してデプロイします。
-  リポジトリを作成できる GitHub アカウント。 アカウントをお持ちでない場合は、[無料で作成する](https://github.com/)ことができます。
-  Azure DevOps 組織。 アカウントをお持ちでない場合は、[無料で作成する](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)ことができます。 (Azure DevOps 組織は GitHub 組織とは異なります。 両者の整合を取る場合、DevOps 組織と GitHub 組織に同じ名前を付けることができます。) <br/> チームに既にある場合は、使用する Azure DevOps プロジェクトの管理者であることを確認します。
-  Microsoft によってホストされるエージェントでパイプラインを実行する機能。 [並列ジョブ](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops)を購入するか、Free レベルを要求できます。 Free レベルを要求するには、[この記事](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops)の指示に従ってください。 Free レベルが付与されるまで、2 から 3 営業日かかる場合があります。


## <a name="locate-the-artifacts"></a>成果物を見つける

Azure Migrate App Containerization ツールでは、Azure Pipelines を使用してアプリケーションの CI/CD ワークフローを構成するために使用できる成果物が自動的に生成されます。 成果物は、ツールを使用してアプリケーションのデプロイが完了すると生成されます。 成果物は次のようにして見つけることができます。 

1. Azure Migrate App Containerization ツールを実行しているマシンに移動します。 
2. **C:\ProgramData\Microsoft Azure Migrate App Containerization** ディレクトリに移動します。 C:\ProgramData に移動できない場合は、ファイル エクスプローラーの *[表示]* で *[隠しファイル]* を表示するオプションを選択してください。 
3. **ソース マシンの IP/FQDN** に対応するディレクトリを選択します。 ソース マシンは、コンテナー化されたアプリケーションを実行する App Containerization ツールで指定されたマシンです。
4. Java アプリケーションの場合 
    - **JavaTomcatWebApp\Artifacts** に移動します。    
    - **Catalina\localhost** ディレクトリに移動します。 このディレクトリが見つからない場合は、Tomcat エンジン名とホスト名に対応するディレクトリに移動してみてください。
    - このディレクトリ内のアプリケーション フォルダーを見つけます。 
5. ASP.NET アプリケーションの場合
    - **IISAspNetWebApp** に移動します。
    - このディレクトリ内のアプリケーション フォルダーを見つけます。


## <a name="upload-artifacts-to-github"></a>成果物を GitHub にアップロードする

成果物は、Azure DevOps で使用されるソース リポジトリにアップロードする必要があります。 

1. GitHub アカウントにサインインします。 
2. [この記事](https://docs.github.com/get-started/quickstart/create-a-repo)の手順に従って、新しいリポジトリを作成します。 
3. 次の手順では、次の成果物をこのリポジトリにアップロードします。
   -  Java アプリの場合は、App Containerization ツールを実行しているマシン上のアプリケーション フォルダーにある次のフォルダーとファイルを選択します。
        - MandatoryArtifacts フォルダー
        - manifests フォルダー
        - OptionalArtifacts フォルダー
        - Dockerfile 
        - Entryscript.sh ファイル
        - azure-pipelines.yml ファイル
    - ASP.NET アプリの場合は、App Containerization ツールを実行しているマシン上のアプリケーション フォルダーにある次のフォルダーとファイルを選択します。
        - manifests フォルダー
        - Build フォルダー
        - azure-pipelines.yml ファイル

## <a name="sign-in-to-azure-pipelines"></a>Azure Pipelines にサインインする

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines) にサインインします。 サインインすると、ブラウザーが https://dev.azure.com/my-organization-name に移動し、Azure DevOps ダッシュボーが表示されます。

選択した組織内で、"*プロジェクト*" を作成します。 組織にプロジェクトがない場合、 **[プロジェクトを作成して開始します]** 画面が表示されます。 それ以外の場合は、ダッシュボードの右上隅にある **[プロジェクトの作成]** ボタンを選択します。

## <a name="add-service-connections"></a>サービス接続を追加する

パイプラインを作成する前に、まずサービス接続を作成する必要があります。これは、テンプレート内で接続を選択して確認することを求めるメッセージが表示されるためです。 サービス接続を使用すると、タスク テンプレートを使用するときに Azure Container Registry に接続でき、アプリケーションをデプロイする Azure サブスクリプションに接続できます。 

1. 左下隅にある **[プロジェクトの設定] > [サービス接続]** を選択します。
2. **[新しいサービス接続]** を選択し、必要なサービス接続の種類として **[Docker レジストリ] > [Azure Container Registry]** オプションを選択し、[次へ] を選択します。
3. 認証方法を選択し、[次へ] を選択します。
4. サービス接続のパラメーターを入力します。 パラメーターの一覧は、サービス接続の種類ごとに異なります。 詳細については、[サービス接続の種類と関連パラメーターの一覧](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#common-service-connection-types)に関する記事を参照してください。
5. **[保存]** を選択して接続を作成します。
6. 接続が作成されてパラメーターが入力された後、接続を **検証** します。 検証リンクでは、入力した情報を使用した外部サービスへの REST 呼び出しが使用され、呼び出しが成功したかどうかが示されます。
7. Azure サブスクリプションへのサービス接続の作成についても、 **[新しいサービス接続] > [Azure Resource Manager]** を選択して、同じ手順を繰り返します。 
8. 両方のサービス接続のリソース ID をメモします。 

## <a name="create-the-pipeline"></a>パイプラインを作成する

両方のサービス接続を作成したので、パイプラインを構成できます。 パイプライン YAML は App Containerization ツールによって自動的に作成され、次のように構成できます。  

1. パイプラインに移動し、 **[新しいパイプライン]** を選択します。
2. 最初に、ソース コードの場所として **GitHub** を選択し、ウィザードの手順を実行します。
3. サインインするために GitHub にリダイレクトされる場合があります。 その場合は、GitHub の資格情報を入力します。
4. リポジトリの一覧が表示されたら、目的のリポジトリを選択します。
5. Azure Pipelines アプリをインストールするために、GitHub にリダイレクトされる場合があります。 その場合は、[Approve & Install]\(承認してインストール\) を選択します。
6. 新しいパイプラインが表示されたら、YAML を確認して、その動作を確認します。
7. YAML で、Azure Container Registry サービス接続のリソース ID を **dockerRegistryServiceConnection** 変数の値として指定します。
8. Azure Resource Manager サービス接続のリソース ID を **dockerRegistryServiceConnection** 変数の値として指定します。
9. 準備ができたら、 **[保存]** を選択して、新しいパイプラインをリポジトリにコミットします。 

パイプラインは、コンテナー化された Day 2 Operations をビルドしてデプロイするためにすべて設定されました。 組織のニーズに合わせて[パイプラインをカスタマイズ](/azure/devops/pipelines/customize-pipeline?view=azure-devops#prerequisite)できます。 