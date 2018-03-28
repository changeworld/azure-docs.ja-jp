---
title: Jenkins と Azure Container Instances を使用して Azure でプロジェクトをビルドする
description: Jenkins 用の Azure Container Agent プラグインと Azure Container Instances を使用し、Azure でプロジェクトをビルドする方法について説明します。
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: a18136afc096f8315310fae134d3f683994b6d0a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Jenkins と Azure Container Instances を使用して Azure でプロジェクトをビルドする

Azure Container Instances を使用すると、仮想マシンをプロビジョニングしたり、より高度なレベルのサービスを採用したりしなくても、簡単に着手し、実行することができます。 Azure Container Instances は、必要な容量に基づいて 1 秒あたりで課金されます。ビルドの実行など、一時的に作業負荷が発生する場合に適したオプションです。

学習内容は次のとおりです。
> [!div class="checklist"]
> * Azure に Jenkins サーバーをインストールして構成する
> * Jenkins の Azure Container Agents プラグインのインストールと構成
> * Azure Container Instances を使用して [Spring PetClinic サンプル アプリケーション](https://github.com/spring-projects/spring-petclinic)をビルドする

## <a name="prerequisites"></a>前提条件

- **Azure のサブスクリプション** - Azure 購入オプションの詳細については、「[Azure の購入方法](https://azure.microsoft.com/pricing/purchase-options/)」または [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- **Azure CLI 2.0 または Azure Cloud Shell** - Azure コマンドを入力できる次の製品のいずれかをインストールします。

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) - コマンドまたは端末ウィンドウから Azure コマンドを実行できます。
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - ブラウザーベースのシェル環境 Cloud Shell は、Azure の管理タスクを視野に入れて開発されたブラウザーベースのコマンド ライン環境です。

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Jenkins Marketplace イメージを使用して Azure に Jenkins サーバーをインストールする

Jenkins は、Jenkins サーバーが 1 つ以上のエージェントに作業を委任するモデルをサポートしているので、1 つの Jenkins インストールで多数のプロジェクトをホストすることや、ビルドやテストに必要なさまざまな環境を提供することができます。 このセクションの手順では、Azure に Jenkins サーバーをインストールして構成する方法を説明します。

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Azure 上で動作する Jenkins サーバーに接続する

Azure に Jenkins をインストールしたら、Jenkins に接続する必要があります。 次の手順では、Azure 上で実行されている Jenkins VM への SSH 接続を設定する手順を説明します。 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Jenkins DNS を更新する

Jenkins は、自身を指すリンクを作成するときに、自身の URL を知っている必要があります。 たとえば、Jenkins からビルド結果への直接リンクを含む電子メールを送信するときに URL を使用する必要があります。 

ここでは、Jenkins の URL の設定手順について説明します。

1. ブラウザーで Jenkins のダッシュボード (`http://localhost:8080`) に移動します。

1. **[Manage Jenkins]\(Jenkins の管理\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **[Configure System]\(システムの構成\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\) プラグイン オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. **[Jenkins Location]\(Jenkins の場所\)** に、Jenkins サーバーの URL を入力します。

1. **[保存]** を選択します。

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Java Network Launch Protocol (JNLP) を許可するように Jenkins を更新する

> [!NOTE]
> このチュートリアルでは、JNLP エージェントを構成する方法を示します。 バージョン 0.4.x 以降、[Jenkins Azure コンテナー エージェント プラグイン](https://plugins.jenkins.io/azure-container-agents)は、SSH もサポートします。
>
>

Jenkins エージェントは、Java Network Launch Protocol (JNLP) を介して Jenkins サーバーに接続します。 ここでは、Jenkins サーバーと通信するときに JNLP エージェントが使用するポートを指定する方法について説明します。

1. Jenkins ダッシュボードで、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **[Configure Global Security]\(グローバル セキュリティの構成\)** を選択します。

    ![Jenkins ダッシュボードの [Configure Global Security]\(グローバル セキュリティの構成\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. **[Agents]\(エージェント\)** で **[Fixed]\(固定\)** を選択し、ポートを入力します。 このスクリーン ショットは、例としてポート値 12345 を使用しています。 環境に適したポートを指定する必要があります。

    ![JNLP を許可するように Jenkins のグローバル セキュリティ設定を更新する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. **[保存]** を選択します。

1. Azure CLI 2.0 または Cloud Shell を使用して、次のコマンドを入力して、Jenkins ネットワーク セキュリティ グループの受信ルールを作成します。

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Azure サービス プリンシパルを作成して Jenkins の資格情報に追加する

Azure にデプロイするには、Azure サービス プリンシパルが必要です。 以下の手順では、サービス プリンシパルを作成していない場合は作成し、サービス プリンシパルを使用して Jenkins を更新する手順を説明します。

1. 既にサービス プリンシパルがある (サブスクリプション ID、テナント、appId、およびパスワードを知っている) 場合は、この手順をスキップできます。 セキュリティ プリンシパルを作成する必要がある場合は、「[Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)」(Azure CLI 2.0 で Azure サービス プリンシパルを作成する) の記事を参照してください。 プリンシパルを作成するときに、サブスクリプション ID、テナント、appId、パスワードの値を書き留めておきます。

1. **[Credentials]\(資格情報\)** を選択します。

    ![Jenkins ダッシュボードの [Credentials]\(資格情報\) の管理オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. **[Credentials]\(資格情報\)** で、**[System]\(システム\)** を選択します。

    ![Jenkins ダッシュボードのシステム資格情報の管理オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. **[Global credentials(unrestricted)]\(グローバル資格情報 (制限なし)\)** を選択します。

    ![Jenkins ダッシュボードのグローバル システム資格情報の管理オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. **[Adding some credentials]\(資格情報の追加\)** を選択します。

    ![Jenkins ダッシュボードの [Adding some credentials]\(資格情報の追加\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. **[Kind]\(種類\)** ドロップダウンリストで **[Microsoft Azure Service Principal]\(Microsoft Azure サービス プリンシパル\)** を選択すると、ページにサービス プリンシパルの追加に関するフィールドが表示されます。 要求された値を次のように指定します。

    - **[Scope]\(スコープ\)** - **[Global]\(グローバル\) (Jenkins、ノード、アイテム、すべての子アイテムなど)** のオプションを選択します。
    - **[Subscription ID]\(サブスクリプション ID\)** - `az account set` の実行時に指定した Azure サブスクリプション ID を使用します。
    - **[Client ID]\(クライアント ID\)** - `az ad sp create-for-rbac` から返された `appId` 値を使用します。
    - **[Client Secret]\(クライアント シークレット\)** - `az ad sp create-for-rbac` から返された `password` 値を使用します。
    - **[Tenant ID]\(テナント ID\)** - `az ad sp create-for-rbac` から返された `tenant` 値を使用します。
    - **[Azure Environment]\(Azure 環境\)** - `Azure` を選択します。Azure 環境
    - **[ID]** - 「`myTestSp`」と入力します。 この値は、このチュートリアルで後ほど使用します。
    - **[Description]\(説明\)** - (省略可能) このプリンシパルを説明を入力します。

    ![Jenkins ダッシュボードで新しいサービス プリンシパル プロパティを指定する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. プリンシパルを定義する情報を入力したら、必要に応じて **[Verify Service Principal]\(サービス プリンシパルの検証\)** を選択し、すべてが正しく機能していることを確認できます。 サービス プリンシパルが正しく定義されている場合は、"Successfully verified the Microsoft Azure Service Principal."\(Microsoft Azure サービス プリンシパルが正常に検証されました。\) というメッセージが **[Description]\(説明\)** フィールドの下に表示されます。

1. 完了したら、**[OK]** を選択してプリンシパルを Jenkins に追加します。 Jenkins ダッシュボードには、新しく追加されたプリンシパルが **[Global Credentials]\(グローバル資格情報\)** ページに表示されます。

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Azure Container Instances 用の Azure リソース グループを作成する

Azure Container Instances は、Azure リソース グループに配置する必要があります。 Azure リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。

Azure CLI 2.0 または Cloud Shell を使用して次のコマンドを入力し、位置 `eastus` に `JenkinsAciResourceGroup` リソース グループを作成します。

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

`az group create` コマンドの実行が完了すると、次のような結果が表示されます。

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Jenkins 用の Azure Container Agents プラグインをインストールする

Azure Container Agents プラグインを既にインストールしている場合は、このセクションをスキップできます。

Jenkins エージェント用に Azure リソース グループを作成したら、次の手順で Azure Container Agents プラグインをインストールします。

1. Jenkins ダッシュボードで、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **[Manage Plugins]\(プラグインの管理\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\) プラグイン オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. **[Available]\(使用可能\)** を選択します。

    ![Jenkins ダッシュボードの [Available]\(使用可能\) プラグイン オプションの表示](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. **[Filter]\(フィルター\)** テキストボックスに「`Azure Container Agents`」と入力します (テキストを入力すると、リストの表示は絞り込まれます)。

    ![Jenkins ダッシュボードの [Available]\(使用可能\) Jenkins プラグインの絞り込み](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. **Azure Container Agents** プラグインの横にあるチェックボックスをオンにして、インストール オプションのいずれかを選択します。 ここでは、デモの目的から **[Install without restart]\(再起動なしでインストールする\)** オプションを選択しました。

    ![Jenkins ダッシュボードから Azure Container Agents プラグインをインストールする](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  目的のプラグインをインストールするオプションを選択すると、インストールしている内容の詳細な状態のページが Jenkins ダッシュボードに表示されます。

    ![Jenkins ダッシュボードから Azure Container Agents プラグインをインストールする処理の状態](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Jenkins ダッシュボードのメイン ページに戻るには、**[Go back to the top page]\(トップページに戻る\)** を選択します。

## <a name="configure-the-azure-container-agents-plugin"></a>Azure Container Agents プラグインの構成

Azure Container Agents プラグインがインストールされたので、ここでは、Jenkins ダッシュボードでプラグインを設定する方法について説明します。

1. Jenkins ダッシュボードで、**[Manage Jenkins]\(Jenkins の管理\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\)](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. **[Configure System]\(システムの構成\)** を選択します。

    ![Jenkins ダッシュボードの [Manage Jenkins]\(Jenkins の管理\) プラグイン オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. ページの下部にある **[Cloud]\(クラウド\)** セクションを見つけて、**[Add a new cloud]\(新しいクラウドの追加\)** ドロップダウン リストから **[Azure Container Instance]** を選択します。

    ![Jenkins ダッシュボードから新しいクラウド プロバイダーを追加する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. **[Azure Container Instance]** セクションで、次の値を指定します。

    - **[Cloud name]\(クラウド名\)** - (この値は既定で名前が自動生成されるので、入力は任意)。このインスタンスの名前を指定します。 
    - **[Azure Credential]\(Azure の資格情報\)** - ドロップダウンの矢印を選択し、前の手順で作成した Azure サービス プリンシパルを識別する `myTestSp` エントリを選択します。
    - **[Resource Group]\(リソース グループ\)** - ドロップダウンの矢印を選択し、前の手順で作成した Azure Container Instances のリソース グループを識別する `JenkinsAciResourceGroup` エントリを選択します。

    ![Azure Container Instances のプロパティを定義する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. **[Add Container Template]\(コンテナー テンプレートの追加\)** ドロップダウンの矢印を選択し、**[Aci Container Template]\(Aci コンテナー テンプレート\)** を選択します。

1. **[Aci Container Template]\(Aci コンテナー テンプレート\)** セクションで、次の値を指定します。

    - **[Name]\(名前\)** - 「`ACI-container`」と入力します。
    - **[Labels]\(ラベル\)** - 「`ACI-container`」と入力します。
    - **[Docker Image]\(Docker イメージ\)** - 「`cloudbees/jnlp-slave-with-java-build-tools`」と入力します。

    ![Azure Container Instances のイメージのプロパティを定義する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. **[Advanced] \(詳細設定)** を選択します。

1. **[Retention Strategy]\(リテンション戦略\)** ドロップダウンの矢印を選択し、**[Container Idle Retention Strategy]\(コンテナー アイドル リテンション戦略\)** を選択します。 このオプションを選択すると、Jenkins はエージェントで新しいジョブが実行されなくなってから、指定されたアイドル時間が経過するまでエージェントは保持されます。

    ![Azure Container Instances のリテンション戦略を定義する](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. **[保存]** を選択します。

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Jenkins で Spring PetClinic Application ジョブを作成する

ここでは、フリースタイル プロジェクトとして Jenkins ジョブを作成し、Spring PetClinic Application をビルドする手順について説明します。

1. Jenkins ダッシュボードで **[New Item]\(新しい項目\)** を選択します。

    ![Jenkins ダッシュボードの [New Item]\(新しい項目\) オプション](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. 項目名に「`myPetClinicProject`」と入力し、**[Freestyle project]\(フリースタイル プロジェクト\)** 選択します。

    ![Jenkins ダッシュボードの新しいフリースタイル プロジェクト](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. **[OK]**を選択します。

1. **[General]\(全般\)** タブを選択し、次の値を指定します。

    - **[Restrict where project can be run]\(プロジェクトを実行できる場所を制限する\)** - このオプションをオンにします。
    - **[Label Expression]\(ラベル式\)** - 「`ACI-container`」と入力します。 このフィールドから離れると、前の手順で作成したクラウドの構成によってラベルが提供されていることを確認するメッセージが表示されます。

    ![Jenkins ダッシュボードの新しいフリースタイル プロジェクトの全般設定](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. **[Source Code Management]\(ソース コードの管理\)** タブを選択し、次の値を指定します。

    - **[Source Code Management]\(ソース コードの管理\)** - **[Git]** を選択します。
    - **[Repository URL]\(リポジトリの URL\)** - Spring PetClinic Sample Application の GitHub リポジトリの URL を次のように入力します。`https://github.com/spring-projects/spring-petclinic.git`

1. **[Build]\(ビルド\)** タブを選択し、次のタスクを実行します。

    - **[Add build step]\(ビルド ステップの追加\)** ドロップダウンの矢印を選択し、**[Invoke top-level Maven targets]\(最上位の Maven ターゲットを呼び出す\)** を選択します。

    - **[Goals]\(目標\)** に「`package`」と入力します。

1. **[Save]\(保存\)** を選択して新しいプロジェクト定義を保存します。

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Jenkins で Spring PetClinic Application ジョブをビルドする

次はプロジェクトのビルドです。 ここでは、Jenkins ダッシュボードからプロジェクトをビルドする方法について説明します。

1. Jenkins ダッシュボードで、[`myPetClinicProject`] を選択します。

    ![Jenkins ダッシュボードからビルドするプロジェクトを選択します。](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. **[Build now]\(今すぐビルド\)** を選択します。 

    ![Jenkins ダッシュボードからビルドします。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Jenkins でビルドを開始すると、ビルドはキューに格納されます。 Azure Container Agent の場合、Azure Container Agent が起動され、オンラインになるまで、ビルドは実行できません。 それまでは、エージェント名とそのビルドが保留中であることを示すメッセージが表示されます (このプロセスには約 5 分かかりますが、エージェントをビルドに初めて使用する場合にのみ必要です。 以降のビルドは、エージェントがその時点でオンラインになっているため、はるかに高速になります)。

    ![エージェントが作成され、オンラインになるまで、ビルドはキューに格納されます。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    ビルドが開始されると、ビルドが実行中であることを示す不定量進行状況バーが表示されます。

    ![不定量進行状況バーが表示されていれば、ビルドは実行中です。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. 不定量進行状況バーが表示されなくなったら、ビルド番号の横にある矢印を選択します。 コンテキストメニューから、**[Console output]\(コンソール出力\)** を選択します。

    ![[Console Log]\(コンソール ログ\) メニュー項目をクリックすると、ビルド情報が表示されます。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. ビルド プロセスから生成されたコンソール ログ情報。 (作成した Azure エージェントでリモートで実行されているビルドに関する情報を含む) すべてのビルド情報を表示するには、**[Full log]\(完全なログ\)** を選択します。

    ![[Full log]\(完全なログ\) をクリックすると、詳細なビルド情報が表示されます。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    [Full log]\(完全なログ\) には、より詳細なビルド情報が表示されます。

    ![[Full log]\(完全なログ\) には、より詳細なビルド情報が表示されます。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. ビルドのディスポジションを表示するには、ログの一番下までスクロールします。

    ![ビルドのディスポジションは、ビルド ログの一番下に表示されます。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Azure リソースをクリーンアップする

このチュートリアルでは、2 つの Azure リソース グループに含まれるリソースを作成しました。 
    - `JenkinsResourceGroup` - Jenkins サーバーの Azure リソースが含まれています。
    - `JenkinsAciResourceGroup` - Jenkins エージェントの Azure リソースが含まれています。
    
Azure リソース グループ内のリソースを使用する必要がなくなった場合は、次のように `az group delete` コマンドを使用してリソース グループを削除できます (&lt;resourceGroup> は、削除するリソース グループの名前で置き換えてください)。

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure ハブの Jenkins にアクセスして最新の記事とサンプルを見る](https://docs.microsoft.com/azure/jenkins/)