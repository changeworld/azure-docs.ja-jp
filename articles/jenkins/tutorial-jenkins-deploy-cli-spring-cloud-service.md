---
title: Jenkins と Azure CLI を使用して Azure Spring Cloud にアプリをデプロイする
description: 継続的インテグレーションおよびデプロイ パイプラインで Azure CLI を使用してマイクロサービスを Azure Spring Cloud サービスにデプロイする方法について学習します
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732855"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>チュートリアル:Jenkins と Azure CLI を使用して Azure Spring Cloud にアプリをデプロイする

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) は、組み込みのサービス検出および構成管理を備えたフル マネージド マイクロサービス開発環境です。 このサービスを使用すると、Spring Boot ベースのマイクロサービス アプリケーションを簡単に Azure にデプロイできます。 このチュートリアルでは、Jenkins で Azure CLI を使用して Azure Spring Cloud の継続的インテグレーションと継続的デリバリー (CI/CD) を自動化する方法を示します。

このチュートリアルでは、以下のタスクを完了します。

> [!div class="checklist"]
> * サービス インスタンスをプロビジョニングし、Java Spring アプリケーションを起動する
> * Jenkins サーバーを準備する
> * Jenkins のパイプラインで Azure CLI を使用して、マイクロサービス アプリケーションをビルドおよびデプロイする 

このチュートリアルでは、コア Azure サービス、Azure Spring Cloud、Jenkins の[パイプライン](https://jenkins.io/doc/book/pipeline/)とプラグイン、および GitHub に関する中級の知識があることを前提としています。

## <a name="prerequisites"></a>前提条件

>[!Note]
> Azure Spring Cloud は現時点ではパブリック プレビューとして提供されています。 パブリック プレビュー オファリングにより、お客様は公式リリースの前に新機能を試すことができます。  パブリック プレビューの機能とサービスは、運用環境での使用を目的としたものではありません。  プレビュー期間中のサポートの詳細については、[FAQ](https://azure.microsoft.com/support/faq/) のページを参照するか、詳細について[サポート リクエスト](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)を提出してください。

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* GitHub アカウント。 GitHub アカウントをお持ちでない場合は、開始する前に[無料アカウント](https://github.com/)を作成してください。

* Jenkins マスター サーバー Jenkins マスターがまだない場合は、この [クイックスタート](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)の手順に従って、Azure で [Jenkins](https://aka.ms/jenkins-on-azure)  をデプロイしてください。 Jenkins のノードまたはエージェントで、次のものが必要です (たとえば、 ビルド サーバー)。

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 以降](https://maven.apache.org/download.cgi)
    * [Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest) (バージョン 2.0.67 以上)

    >[!TIP]
    > Git、JDK、Az CLI、Azure プラグインなどのツールは、Azure Marketplace の [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) ソリューション テンプレートに既定で含まれています。
    
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>サービス インスタンスをプロビジョニングし、Java Spring アプリケーションを起動する

ここでは、サンプル Microsoft サービス アプリケーションとして [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) を使用し、「[クイックスタート: Azure CLI を使用して Java Spring アプリケーションを起動する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)」と同じ手順に従ってサービス インスタンスをプロビジョニングして、アプリケーションを設定します。 同じプロセスを既に完了している場合は、次のセクションにスキップできます。 それ以外の場合、Azure CLI コマンドは次のとおりです。 「[クイックスタート: Azure CLI を使用して Java Spring アプリケーションを起動する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)」を参照して、追加の背景情報を確認してください。

お使いのローカル コンピューターは、Jenkins ビルド サーバーと同じ前提条件を満たしている必要があります。 マイクロサービス アプリケーションをビルドしてデプロイするために、次のものがインストールされていることを確認します。
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 以降](https://maven.apache.org/download.cgi)
    * [Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest) (バージョン 2.0.67 以上)

1. Azure Spring Cloud 拡張機能をインストールします。

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Azure Spring Cloud サービスを格納するリソース グループを作成します。

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Azure Spring Cloud のインスタンスをプロビジョニングします。

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) リポジトリを自分の GitHub アカウントにフォークします。 お使いのローカル コンピューターで、対象のリポジトリを `source-code` というディレクトリにクローンします。

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. 対象の構成サーバーを設定します。 &lt;your GitHub id&gt; を適切な値に置き換えてください。

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. プロジェクトをビルドします。

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. 3 つのマイクロサービス (**gateway**、**auth-service**、および **account-service**) を作成します。

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. アプリケーションをデプロイします。 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. ゲートウェイにパブリック エンドポイントを割り当てます。

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. アプリケーションが実行されていることを確認できるように、gateway アプリケーションに照会して URL を取得します。

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    前のコマンドによって提供された URL に移動し、PiggyMetrics アプリケーションを実行します。 

## <a name="prepare-jenkins-server"></a>Jenkins サーバーの準備

このセクションでは、ビルドを実行するために、テストに最適な Jenkins サーバーを準備します。 ただし、セキュリティへの影響があるため、Azure でエージェントを活用してお使いのビルドを実行するのに [Azure VM エージェント](https://plugins.jenkins.io/azure-vm-agents)または [Azure Container エージェント](https://plugins.jenkins.io/azure-container-agents)を使用する必要があります。 詳細については、Jenkins の「[security implications of building on master](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master)」 (マスターでの構築におけるセキュリティへの影響) の記事を参照してください。

### <a name="install-plug-ins"></a>プラグインをインストールする

1. 自分の Jenkins サーバーにサインインします。 **[Manage Jenkins]\(Jenkins の管理\)、[Manage Plugins]\(プラグインの管理\)** の順に選択します。
2. **[Available]\(利用可能\)** タブで、次のプラグインを選択します。
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)
    * [Azure 資格情報](https://plugins.jenkins.io/azure-credentials)

    これらのプラグインが一覧に表示されない場合、 **[Installed]\(インストール済み\)** タブで、これらが既にインストール済みであるかどうかを確認してください。

3. プラグインをインストールするには、 **[Download now and install after restart]\(今すぐダウンロードし、再起動後にインストール\)** を選択します。

4. 自分の Jenkins サーバーを再起動して、インストールを完了します。

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Azure サービス プリンシパルの資格情報を Jenkins 資格情報ストアに追加する

1. Azure にデプロイするには、Azure サービス プリンシパルが必要です。 詳細については、Azure App Service へのデプロイに関するチュートリアルの「 [サービス プリンシパルの作成](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) 」セクションを参照してください。 `az ad sp create-for-rbac` からの出力は次のようになります。

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Jenkins ダッシュボードで、 **[Credentials]\(資格情報\)**  >  **[System]\(システム\)** を選択します。 次に、 **[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** を選択します。

3. **[Add Credentials]\(資格情報の追加\)** を選択します。 

4. 種類として **[Microsoft Azure Service Principal]\(Microsoft Azure サービス プリンシパル\)** を選択します。

5. 次の値を指定します。* [Subscription ID]\(サブスクリプション ID\): お使いの Azure サブスクリプション ID を使用します。* [Client ID]\(クライアント ID\): `appId` を使用します。* [Client Secret]\(クライアント シークレット\): `password` を使用します。* [Tenant ID]\(テナント ID\): `tenant` を使用します。* [Azure Environment]\(Azure 環境\): 事前設定された値を選択します。 たとえば、Azure Global には **Azure** を使用します。* [ID]: **azure_service_principal** として設定します。 この ID は、この記事の後の手順で使用します。* [Description]\(説明\) は、オプションのフィールドです。 ここには、意味のある値を指定することをお勧めします。

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Maven と Az CLI spring-cloud 拡張機能をインストールする

サンプル パイプラインでは、Maven を使用してビルドし、Az CLI を使用してサービス インスタンスにデプロイします。 Jenkins のインストール時に、*jenkins* という名前の管理者アカウントが作成されます。 ユーザー *jenkins* に spring-cloud 拡張機能を実行するアクセス許可があることを確認します。

1. SSH 経由で Jenkins マスターに接続します。 

2. Maven をインストールします

    ```bash
        sudo apt-get install maven 
    ```

3. Azure CLI をインストールします。 詳細については、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。 [Azure 上で Jenkins Master](https://aka.ms/jenkins-on-azure) を使用する場合、Azure CLI は既定でインストールされます。

4. `jenkins` ユーザーに切り替えます。

    ```bash
        sudo su jenkins
    ```

5. **spring-cloud** 拡張機能を追加します。

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Jenkinsfile を作成する
1. 自分のリポジトリ (https://github.com/&lt ;対象の GitHub ID&gt; /piggymetrics) で、ルートに **Jenkinsfile** を作成します。

2. ファイルを次のように更新します。 **\<resource group name>** および **\<service name>** の値を必ず置き換えてください。 Jenkins で資格情報を追加したときに別の値を使用する場合は、**azure_service_principal** を適切な ID に置き換えます。 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. 変更を保存してコミットします。

## <a name="create-the-job"></a>ジョブを作成する

1. Jenkins ダッシュボードで、 **[New Item]\(新しい項目\)** をクリックします。

2. ジョブの名前として *Deploy-PiggyMetrics* を指定し、 **[Pipeline]\(パイプライン\)** を選択します。 [OK] をクリックします。

3. **[パイプライン]** タブをクリックします。

4. **[定義]** で、 **[Pipeline script from SCM]\(SCM からのパイプライン スクリプト\)** を選択します。

5. **[SCM]** で、 **[Git]** を選択します。

6. フォークしたリポジトリの GitHub URL を入力します ( **https://github.com/&lt ;対象の GitHub ID&gt; /piggymetrics.git**)

7. **[Branch Specifier (black for 'any')]\(ブランチ指定子 (空欄は "すべて" を指定)\)** が * **/Azure** になっていることを確認してください

8. **[Script path]\(スクリプトのパス\)** は **Jenkinsfile** のままにしておきます

7. **[保存]**

## <a name="validate-and-run-the-job"></a>ジョブを検証して実行する

ジョブを実行する前に、ログイン入力ボックスのテキストを **enter login ID** に更新しましょう。

1. 自分のリポジトリで、 **/gateway/src/main/resources/static/** 内の `index.html` を開きます

2. "enter your login" を検索し、"enter login ID" に更新します

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. 変更をコミットする

4. Jenkins でジョブを手動で実行します。 Jenkins ダッシュボードで、ジョブ *Deploy-PiggyMetrics* をクリックし、次に **[Build Now]\(今すぐビルド\)** をクリックします。

ジョブが完了したら、**gateway** アプリケーションのパブリック IP に移動し、アプリケーションが更新されたことを確認します。 

![更新された Piggy Metrics](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要になったら、削除してください。

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>次のステップ

この記事では、Jenkins で Azure CLI を使用して Azure Spring Cloud の継続的インテグレーションと継続的デリバリー (CI/CD) を自動化する方法を学習しました。

Azure Jenkins プロバイダーの詳細については、Azure 上の Jenkins に関するサイトを参照してください。

> [!div class="nextstepaction"]
> [Azure 上の Jenkins](/azure/jenkins/)
