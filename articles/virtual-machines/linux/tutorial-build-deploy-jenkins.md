---
title: "VSTS を使用して Jenkins から Azure VM への CI/CD を設定する | Microsoft Docs"
description: "Visual Studio Team Services (VSTS) または Microsoft Team Foundation Server (TFS) の Release Management から Jenkins を使用して、Azure VM に対する Node.js アプリの継続的インテグレーション (CI) と継続的配置 (CD) を設定します"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Jenkins とVSTS を使用してアプリを Linux VM にデプロイする

継続的インテグレーション (CI) と継続的配置 (CD) は、コードの構築、リリース、配置に使用できるパイプラインです。 Visual Studio Team Services (VSTS) には、Azure へのデプロイに使用できる機能が一式そろった CI/CD 自動化ツールが用意されています。 Jenkins は、よく使われているサードパーティの CI/CD サーバーベースのツールであり、CI/CD 自動化機能も備えています。 CI/CD の両方を使用して、クラウド アプリまたはサービスの提供方法をカスタマイズすることができます。

このチュートリアルでは、Jenkins を使用して **Node.js Web アプリ**を構築し、VSTS または Team Foundation Server (TFS) を使用して Linux 仮想マシンを含む[デプロイ グループ](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)にデプロイします。

このチュートリアルの内容は次のとおりです。

> [!div class="checklist"]
> * サンプル アプリを入手する
> * Jenkins プラグインを構成する
> * Node.js の Jenkins フリースタイル プロジェクトを構成する
> * VSTS との統合のために Jenkins を構成する
> * Jenkins サービス エンドポイントを作成する
> * Azure 仮想マシンの配置グループを作成する
> * VSTS リリース定義の作成
> * 手動デプロイおよび CI によってトリガーされるデプロイを実行する

## <a name="before-you-begin"></a>開始する前に

* Jenkins サーバーにアクセスする必要があります。 Jenkins サーバーをまだ作成していない場合は、[Azure 仮想マシンへの Jenkins マスターの作成](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template)に関するページをご覧ください。 

* ご利用の VSTS アカウント (`https://{youraccount}.visualstudio.com`) にサインインします。 
  [無料の VSTS アカウント](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)を取得できます。

  > [!NOTE]
  > 詳細については、[VSTS への接続](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)に関するページをご覧ください。

*  デプロイ ターゲットの Linux 仮想マシンが必要です。  詳細については、「[Azure CLI を使用した Linux VM の作成と管理](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm)」をご覧ください。

*  仮想マシンの受信ポート 80 を開きます。  詳細については、「[Azure Portal を使用したネットワーク セキュリティ グループの作成](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)」をご覧ください。

## <a name="get-the-sample-app"></a>サンプル アプリを入手する

配置するアプリを Git リポジトリに格納する必要があります。
このチュートリアルでは、[GitHub で入手できるこのサンプル アプリ](https://github.com/azooinmyluggage/fabrikam-node)を使用することをお勧めします。  このチュートリアルには、Node.js のインストールに使用するサンプル スクリプトとアプリケーションが含まれています。  独自のリポジトリで作業する場合は、同様のサンプルを構成する必要があります。

1. このアプリのフォークを作成し、このチュートリアルの後の手順で使用できるように場所 (URL) のメモを取ります。  詳細については、「[Fork a repo (リポジトリのフォーク)](https://help.github.com/articles/fork-a-repo/)」をご覧ください。    

> [!NOTE]
> このアプリは [Yeoman](http://yeoman.io/learning/index.html) を使用して構築されています。また、**Express**、**bower**、**grunt** が使用され、依存関係としていくつかの **npm** パッケージがあります。
> このサンプルには、Nginx を設定し、アプリを配置するスクリプトも含まれています。 これは仮想マシンで実行します。 このスクリプトを実行すると、具体的には Node、Nginx、PM2 がインストールされ、Nginx と PM2 が構成され、Node アプリが開始されます。

## <a name="configure-jenkins-plugins"></a>Jenkins プラグインを構成する

まず **NodeJS** 用と **VS Team Services Continuous Deployment** 用の 2 つの Jenkins プラグインを構成する必要があります。

1. Jenkins アカウントを開き、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。
2. **[Manage Jenkins]\(Jenkins の管理\)** ページで **[Manage Plugins]\(プラグインの管理\)** をクリックします。
3. 一覧を絞り込んで **[NodeJS]** プラグインを探し、**[Install without restart]\(再起動しないでインストール\)** オプションを選択します。
    ![NodeJS プラグインを Jenkins に追加する](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 一覧を絞り込んで **[VS Team Services Continuous Deployment]** プラグインを探し、**[Install without restart]\(再起動しないでインストール\)** オプションを選択します。
5. Jenkins ダッシュボードに戻り、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。
6. **[Global Tool Configuration]\(ツールのグローバル構成\)** を選択します。  **[NodeJS]** を検索して **[NodeJS installations]\(NodeJS のインストール\)** をクリックします。
7. **[Install automatically]\(自動的にインストールする\)** オプションを有効にして、**[名前]** の値を入力します。
8. **[ Save]** をクリックします。

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Node.js の Jenkins フリースタイル プロジェクトを構成する

1. **[新しい項目]** をクリックします。  **[項目名]** を入力します。
2. **[Freestyle project]\(フリースタイル プロジェクト\)** を選択します。  **[OK]**をクリックします。
3. **[Source Code Management]\(ソース コード管理\)** タブで **[Git]** を選択し、アプリ コードを保存するリポジトリとブランチの詳細情報を入力します。    
    ![ビルドにリポジトリを追加する](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. **[Build Triggers]\(ビルド トリガー\)** タブで **[Poll SCM]\(SCM のポーリング\)** を選択し、スケジュール「`H/03 * * * *`」を入力して Git リポジトリの変更を 3 分間隔でポーリングします。 
5. **[Build Environment]\(ビルド環境\)** タブで **[Provide Node &amp; npm bin/ folder PATH]\(Node と npm bin/ フォルダーのパスを入力\)** を選択し、**[NodeJS Installation]\(Node JS のインストール\)** の値を選択します。 **[npmrc file]\(npmrc ファイル\)** は [use system default]\(システムの既定値を使用する\) のままにします。
6. **[ビルド]** タブで **[Execute shell]\(シェルの実行\)** を選択して `npm install` コマンドを入力し、すべての依存関係が更新されるようにします。


## <a name="configure-jenkins-for-vsts-integration"></a>VSTS との統合のために Jenkins を構成する

  > [!NOTE]
  次の手順で使用する個人用アクセス トークン (PAT) に **VSTS のリリース (読み取り、書き込み、実行および管理) 権限**が含まれるようにします。
 
1.  VSTS アカウントに PAT を作成します (まだお持ちでない場合)。 Jenkins から VSTS アカウントにアクセスするにはこの情報が必要です。  このセクションの、以降の手順で必要なトークンの情報を**保存**するようにします。
  生成方法については、[VSTS と TFS 用の個人用アクセス トークンを作成する方法](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)に関するページをご覧ください。
2. **[Post-build Actions]\(ビルド後のアクション\)** タブで **[Add post-build action]\(ビルド後のアクションを追加する\)** をクリックします。 **[Archive the artifacts]\(成果物のアーカイブ\)** を選択します。
3. **[Files to archive]\(アーカイブするファイル\)** に `**/*` と入力してすべてのファイルが含まれるようにします。
4. 別のアクションを作成するには **[Add post-build action]\(ビルド後のアクションを追加する\)** をクリックします。
5. **[Trigger release in TFS/Team Services]\(TFS/Team Services でリリースをトリガーする\)** を選択して、VSTS アカウントの URI (例: `https://{your-account-name}.visualstudio.com`) を入力します。
6. **[チーム プロジェクト]** 名を入力します。
7. **[リリース定義]** (後ほど VSTS でこのリリース定義を作成します) の名前を選択します。
8. VSTS または TFS 環境に接続するための資格情報を選択します。  VSTS を使用している場合は、**[ユーザー名]** を空白のままにします。
   オンプレミス版の TFS を使用している場合は、**[ユーザー名とパスワード]** を入力します。    
    ![Jenkins のビルド後のアクションを構成する](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Jenkins プロジェクトを **[保存]** します。

## <a name="create-a-jenkins-service-endpoint"></a>Jenkins サービス エンドポイントを作成する

サービス エンドポイントを使用して VSTS から Jenkins に接続できます。

1. VSTS の **[サービス]** ページを開き、**[新しいサービス エンドポイント]** の一覧を開いて **[Jenkins]** を選択します。
    ![Jenkins エンドポイントを追加する](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 接続名を入力します。
3. Jenkins サーバーの URL を入力し、**[信頼されていない SSL 証明書を受け入れる]** オプションをオンにします。  URL の例は次のとおりです。`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Jenkins アカウントの **[ユーザー名とパスワード]** を入力します。
5. **[接続の確認]** を選択して情報が正しいことを確認します。
6. **[OK]** を選択してサービス エンドポイントを作成します。

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Azure 仮想マシンのデプロイ グループを作成する

リリース定義を仮想マシンにデプロイできるように、VSTS エージェントを登録する[デプロイ グループ](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)が必要です。  デプロイ グループを使用すると、デプロイ用にターゲット マシンの論理グループを定義し、各コンピューターに必要なエージェントをインストールするのが簡単になります。

   > [!NOTE]
   > 次の手順では前提条件となるものをインストールしますが、**スクリプトは sudo 権限で実行しない**ようにします。

1. **[ビルドとリリース]** ハブの **[リリース]** タブを開き、**[Deployment groups]\(デプロイ グループ\)** を開いて **[+ 新規]** を選択します。
2. 配置グループの名前と、説明 (省略可能) を入力します。
   **[作成]** を選択します。
3. デプロイ ターゲットの仮想マシンのオペレーティング システムを選択します。  たとえば、**[Ubuntu 16.04 以上]** を選択します。
4. **[認証用にスクリプト内の個人用アクセス トークンを使用する]** にチェックを入れます。
5. **[システムの前提条件]** を選択します。  お使いのオペレーティング システムの前提条件となるものをインストールします。
6. **[スクリプトをクリップボードにコピー]** を選択してスクリプトをコピーします。
7. デプロイ ターゲットの仮想マシンに**ログイン**して、スクリプトを**実行**します。  スクリプトは sudo 権限で実行**しない**ようにします。
8. インストール後、デプロイ グループのタグを求められます。  既定値を受け入れます。
9. VSTS の **[Deployment Groups]\(デプロイ グループ\)** の **[ターゲット]** で、新たに登録された仮想マシンを確認します。

## <a name="create-a-vsts-release-definition"></a>VSTS リリース定義の作成

リリース定義には、VSTS でアプリをデプロイするときに実行するプロセスを指定します。  この例ではシェル スクリプトを実行します。

VSTS でリリース定義を作成するには、次の手順を行います。

1. **[ビルドとリリース]** ハブの **[リリース]** を開いて、**[リリース定義の作成]** を選択します。 
2. **[空のプロセス]** で開始することを選択して **[空]** のテンプレートを選択します。
3. **[成果物]** セクションで**[+ 成果物の追加]** をクリックし、**[ソースの種類]** に **[Jenkins]** を選択します。 Jenkins サービス エンドポイントの接続を選択します。 Jenkins ソース ジョブを選択し、**[追加]** を選択します。
4.  **[環境 1]** の横にある省略記号をクリックします。  **[Add deployment group phase]\(デプロイ グループ フェーズを追加\)** をクリックします。
5.  ご利用の **[デプロイ グループ]** を選択します。
5. **[+]** をクリックして **[Deployment group phase]\(デプロイ グループ フェーズ\)** にタスクを追加します。
6. **[シェル スクリプト]** タスクを選択して **[追加]** をクリックします。    
    **[シェル スクリプト]** タスクは、Node.js をインストールし、アプリを起動するために各サーバーで実行されるスクリプトの構成を指定するために使用されます。 SAP コネクタを次のように構成します。
8. **スクリプトのパス**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  **[詳細]** をクリックして、**[作業ディレクトリを指定する]** を有効にします。
10.  **[作業ディレクトリ]**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. リリース定義の名前を、Jenkins のビルドの **[Post-build Actions]\(ビルド後のアクション\)** タブで指定した名前に変更します。 ソース アーティファクトが更新されたときに、Jenkins から新しいリリースをトリガーするには、この名前を指定する必要があります。
12. **[保存]** をクリックし、**[OK]** をクリックしてリリース定義を保存します。

## <a name="execute-manual-and-ci-triggered-deployments"></a>手動デプロイおよび CI によってトリガーされるデプロイを実行する

1. **[+ リリース]** を選択し、**[リリースの作成]** を選択します。
2. 強調表示されているドロップダウン リストから完了したビルドを選択し、**[キュー]** を選択します。
3. ポップアップ メッセージでリリース リンクを選択します。 たとえば、"リリース **Release-1** が作成されました" と表示されます。
4. **[ログ]** タブを開いて、リリース コンソールの出力を確認します。
5. ブラウザーで、配置グループに追加したサーバーのいずれかについて URL を開きます。 たとえば、「`http://{your-server-ip-address}`」と入力します。
6. ソース Git リポジトリに移動し、app/views/index.jade ファイル内の **[h1]** 見出しの内容を、変更を行ったテキストで変更します。
7. 変更を **[コミット]** します。
8. 数分経つと、VSTS または TFS の **[リリース]** ページに、作成された新しいリリースが表示されます。 リリースを開き、実行された配置を確認します。 ご利用ありがとうございます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Jenkins ビルドと VSTS を使用して、Azure にリリース用としてアプリをデプロイする処理を自動化しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Jenkins でアプリを構築する
> * VSTS との統合のために Jenkins を構成する
> * Azure 仮想マシンの配置グループを作成する
> * VM の構成とアプリの配置に使用するリリース定義を作成する

LAMP を展開する方法の詳細 (Linux、Apache、MySQL、および PHP) については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [LAMP スタックのデプロイ](tutorial-lamp-stack.md)