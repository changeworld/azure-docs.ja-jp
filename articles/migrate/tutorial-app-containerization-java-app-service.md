---
title: 'Azure App Containerization Java: Java Web アプリケーションのコンテナー化と Azure App Service への移行'
description: 'チュートリアル: Java Web アプリケーションをコンテナー化し、Azure App Service に移行する'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 768bcc39505671c452d99dc1d8d1d50ec3f3b9e5
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259648"
---
# <a name="java-web-app-containerization-and-migration-to-azure-app-service"></a>Java Web アプリのコンテナ化と Azure App Service への移行

この記事では、Azure Migrate: App Containerization ツールを使用して、(Apache Tomcat で実行されている) Java Web アプリケーションをコンテナー化し、それらを [Azure App Service](https://azure.microsoft.com/services/app-service/) に移行する方法について説明します。 このコンテナー化プロセスでは、コードベースへのアクセスは不要で、既存のアプリケーションを簡単な方法でコンテナー化できます。 このツールは、サーバー上のアプリケーションの実行状態を使用して動作し、アプリケーション コンポーネントを判別し、それらをコンテナー イメージにパッケージ化できるように支援します。 コンテナー化されたアプリケーションは、Azure App Service にデプロイできます。

Azure Migrate: App Containerization ツールは、現時点で以下をサポートしています。

- (Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナー化し、それらを App Service の Linux コンテナーにデプロイする。
- (Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナー化し、それらを AKS の Linux コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-java-kubernetes.md)
- ASP.NET アプリをコンテナー化し、それらを AKS の Windows コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-aspnet-kubernetes.md)
- ASP.NET アプリをコンテナー化し、それらを App Service の Windows コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-aspnet-app-service.md)


Azure Migrate: App Containerization ツールを使用すると、以下を行うことができます。

- **アプリケーションを検出する**: このツールは、(Apache Tomcat で実行されている) Java Web アプリケーションを実行しているアプリケーション サーバーにリモート接続し、アプリケーション コンポーネントを検出します。 このツールにより、アプリケーションのコンテナー イメージを作成するために使用できる Dockerfile が作成されます。
- **コンテナー イメージをビルドする**: Dockerfile を検査し、アプリケーションの要件に従ってさらにカスタマイズし、それを使用してアプリケーション コンテナー イメージをビルドできます。 アプリケーション コンテナー イメージは、指定した Azure Container Registry にプッシュされます。
- **Azure App Service にデプロイする**: ツールにより、コンテナー化されたアプリケーションを Azure App Service にデプロイするために必要なデプロイ ファイルが生成されます。

> [!NOTE]
> Azure Migrate: App Containerization ツールを使用すると、アプリケーション サーバー上の特定のアプリケーションの種類 (ASP.NET と Apache Tomcat 上の Java Web アプリ) とそのコンポーネントを検出できます。 オンプレミスのマシンで実行されているサーバーと、アプリ、ロール、機能のインベントリを検出するには、Azure Migrate: 検出および評価機能を使用します。 [詳細情報](./tutorial-discover-vmware.md)

どのアプリケーションも大幅な再設計なしでコンテナーに直接シフトしてもメリットを得られませんが、既存のアプリを再記述せずにコンテナーに移動した場合、次のようなメリットがあります。

- **インフラストラクチャ使用率の向上:** コンテナーを使用すると、複数のアプリケーションがリソースを共有でき、それらのアプリケーションを同じインフラストラクチャでホストできます。 これにより、インフラストラクチャを統合し、使用率を向上させることができます。
- **簡略化された管理:** AKS や App Service などの最新のマネージド プラットフォームでアプリケーションをホストすることにより、管理作業を簡略化できます。 所有するインフラストラクチャでこれまで実行していた、インフラストラクチャの保守および管理のプロセスを廃止または削減することで、これを実現できます。
- **アプリケーションの移植性:** コンテナー仕様の形式およびプラットフォームの導入と標準化が改善されたため、アプリケーションの移植性は問題になりません。
- **DevOps を使用した最新の管理の導入:** 管理とセキュリティおよび DevOps への移行のための最新の手法を導入し、それらに基づいて標準化できます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * Azure Migrate: App Containerization ツールをインストールします。
> * Java Web アプリケーションを検出します。
> * コンテナー イメージをビルドします。
> * コンテナー化されたアプリケーションを App Service にデプロイします。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

**要件** | **詳細**
--- | ---
**ツールをインストールするコンピューターを特定する** | Azure Migrate: App Containerization ツールをインストールして実行する Windows コンピューター。 Windows コンピューターとして、サーバー (Windows Server 2016 以降) またはクライアント (Windows 10) オペレーティング システムを使用できます。これは、このツールをデスクトップでも実行できることを意味します。 <br/><br/> ツールを実行する Windows マシンには、コンテナー化する Java Web アプリケーションをホストするサーバーまたは仮想マシンへのネットワーク接続が必要です。<br/><br/> アプリケーション アーティファクトを保存するために、Azure Migrate: App Containerization ツールを実行する Windows コンピューターで 6 GB の領域が使用可能であることを確認します。 <br/><br/> Windows コンピューターは、直接またはプロキシ経由でインターネットにアクセスできる必要があります。
**アプリケーション サーバー** | - コンテナー化する Java アプリケーションを実行するサーバーのポート 22 で Secure Shell (SSH) 接続を有効にします。 <br/>
**Java Web アプリケーション** | このツールは、現在以下をサポートしています。 <br/><br/> - Tomcat 8 以降で実行されるアプリケーション。<br/> - Ubuntu Linux 16.04/18.04/20.04、Debian 7/8、CentOS 6/7、Red Hat Enterprise Linux 5/6/7 上のアプリケーション サーバー。 <br/> - Java バージョン 7 以降を使用するアプリケーション。  <br/><br/> このツールは現在以下をサポートしていません。 <br/><br/> - 複数の Tomcat インスタンスを実行するアプリケーション サーバー <br/>  


## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

サブスクリプションがセットアップされたら、以下を備えた Azure ユーザー アカウントが必要になります。
- Azure サブスクリプションの所有者アクセス許可
- Azure Active Directory アプリを登録するためのアクセス許可

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックス。](./media/tutorial-discover-vmware/search-subscription.png)

2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。

    ![ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てます。](./media/tutorial-discover-vmware/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    ![[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てます。](./media/tutorial-discover-vmware/assign-role.png)

7. Azure アカウントには、**Azure Active Directory アプリを登録するためのアクセス許可** も必要です。
8. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** に移動します。
9. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

      ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認します。](./media/tutorial-discover-vmware/register-apps.png)

10. [アプリの登録] 設定が [いいえ] に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、Azure Active Directory アプリの登録を許可するために、**アプリケーション開発者** ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Azure Migrate: App Containerization ツールをダウンロードしてインストールする

1. Windows コンピューターで Azure Migrate: App Containerization のインストーラーを[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2134571)します。
2. 管理者モードで PowerShell を起動し、PowerShell ディレクトリをインストーラーが格納されているフォルダーに変更します。
3. コマンドを使用してインストール スクリプトを実行します。

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>App Containerization ツールを起動する

1. App Containerization ツールを実行する Windows マシンに接続できる任意のマシンでブラウザーを開き、ツールの URL: **https://<*コンピューター名または IP アドレス*>: 44369** を開きます。

   または、アプリのショートカットを選択して、デスクトップからアプリを開くこともできます。

2. 接続がプライベートではないことを示す警告が表示された場合は、[詳細設定] をクリックして、Web サイトに進むことを選択します。 この警告は、Web インターフェイスが自己署名の TLS/SSL 証明書を使用している場合に表示されます。
3. サインイン画面で、マシンのローカル管理者アカウントを使用してサインインします。
4. コンテナー化するアプリケーションの種類として **[Java web apps on Tomcat]\(Tomcat 上の Java Web アプリ\)** を選択します。
5. ターゲットの Azure サービスを指定するには、 **[Containers on Azure App Service]\(Azure App Service 上のコンテナー\)** を選択します。
![App Containerization ツールの既定のロードアップ。](./media/tutorial-containerize-apps-aks/tool-home.png)

### <a name="complete-tool-pre-requisites"></a>ツールの前提条件を満たす
1. **ライセンス条項** に同意し、サード パーティの情報を確認します。
6. ツールの [web app]\(Web アプリ\) > **[前提条件のセットアップ]** で、次の手順を実行します。
   - **接続**: ツールによって、Windows コンピューターがインターネットにアクセスできるかどうかがチェックされます。 コンピューターがプロキシを使用する場合:
     - **[プロキシの設定]** をクリックし、プロキシ アドレス (IP アドレスまたは FQDN の形式) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - サポートされるのは HTTP プロキシのみです。
     - プロキシの詳細を追加した場合、あるいはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **更新プログラムをインストールする**: ツールによって、最新の更新プログラムが自動的にチェックされ、インストールされます。 [こちら](https://go.microsoft.com/fwlink/?linkid=2134571)から、最新バージョンのツールを手動でインストールすることもできます。
   - **Secure Shell (SSH) を有効にする**: ツールによって、コンテナー化する Java Web アプリケーションを実行するアプリケーション サーバーで Secure Shell (SSH) が有効になっていることが確認され通知されます。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

**[サインイン]** をクリックして Azure アカウントにログインします。

1. Azure で認証するには、デバイス コードが必要です。 [サインイン] をクリックすると、デバイス コードを含むモーダルが開きます。
2. **[Copy code & sign in]\(コードのコピーとサインイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure サインイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。

    ![デバイス コードを示すモーダル。](./media/tutorial-containerize-apps-aks/login-modal.png)

3. 新しいタブで、デバイス コードを貼り付け、Azure アカウントの資格情報を使用してサインインを完了します。 サインインが完了したら、[ブラウザー] タブを閉じて、App Containerization ツールの Web インターフェイスに戻ることができます。
4. 使用する **Azure テナント** を選択します。
5. 使用する **Azure サブスクリプション** を指定します。

## <a name="discover-java-web-applications"></a>Java Web アプリケーションを検出する

App Containerization ヘルパー ツールは、指定された資格情報を使用してアプリケーション サーバーにリモート接続し、アプリケーション サーバーでホストされている (Apache Tomcat で実行されている) Java Web アプリケーションを検出しようとします。

1. Java Web アプリケーションを実行するサーバーの **IP アドレス/FQDN と資格情報** を指定します。アプリケーション検出のために、これらを使用してサーバーにリモート接続する必要があります。
    - 指定する資格情報は、アプリケーション サーバーのルート アカウント (Linux) のものである必要があります。
    - ドメイン アカウントの場合 (ユーザーはアプリケーション サーバーの管理者である必要があります)、 *<ドメイン\ユーザー名>* の形式で、ユーザー名の前にドメイン名をプレフィックスとして付加します。
    - アプリケーションの検出は、最大 5 台のサーバーに対して同時に実行できます。

2. **[検証]** をクリックして、ツールを実行するコンピューターからアプリケーション サーバーに到達できること、および資格情報が有効であることを確認します。 検証が正常に完了すると、状態列に **[マップ済み]** として状態が表示されます。  

    ![サーバー IP と資格情報のスクリーンショット。](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. **[続行]** をクリックして、選択したアプリケーション サーバーでアプリケーションの検出を開始します。

4. アプリケーションの検出が正常に完了した後、コンテナー化するアプリケーションの一覧を選択できます。

    ![検出された Java Web アプリケーションのスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. チェックボックスを使用して、コンテナー化するアプリケーションを選択します。
5. **コンテナー名を指定する**: 選択した各アプリケーションのターゲット コンテナーの名前を指定します。 コンテナー名は <*名前:タグ*> として指定する必要があります。タグはコンテナー イメージに使用されます。 たとえば、*appname:v1* というターゲット コンテナー名を指定できます。   

### <a name="parameterize-application-configurations"></a>アプリケーションの構成をパラメーター化する
構成をパラメーター化すると、デプロイ時のパラメーターとしてその構成を使用できます。 これにより、アプリケーションをデプロイするときに、この設定を構成できます。これは、コンテナー イメージで設定を特定の値にハードコーディングするのとは対照的です。 たとえば、このオプションは、データベース接続文字列などのパラメーターに便利です。
1. **[アプリの構成]** をクリックして、検出された構成を確認します。
2. チェックボックスをオンにして、検出されたアプリケーションの構成をパラメーター化します。
3. パラメーター化する構成を選択した後、 **[適用]** をクリックします。

   ![アプリ構成パラメーター化の Java アプリケーションのスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>ファイル システムの依存関係を外部化する

 アプリケーションで使用する他のフォルダーを追加できます。 それらをコンテナー イメージの一部にするか、Azure ファイル共有を通じて永続ストレージに外部化するかを指定します。 外部永続ストレージの使用は、コンテナーの外部に状態を保存する、またはファイル システムに他の静的コンテンツを保存するステートフル アプリケーションに適しています。

1. [App Folders]\(アプリ フォルダー\) の下の **[編集]** をクリックして、検出されたアプリケーション フォルダーを確認します。 検出されたアプリケーション フォルダーは、アプリケーションに必要な必須のアーティファクトとして識別され、コンテナー イメージにコピーされます。

2. **[フォルダーの追加]** をクリックし、追加するフォルダーのパスを指定します。
3. 同じボリュームに複数のフォルダーを追加するには、コンマ (`,`) 区切りの値を指定します。
4. フォルダーをコンテナーの外部の永続ストレージ上に保存する場合は、ストレージ オプションとして **[Azure ファイル共有]** を選択します。
5. アプリケーション フォルダーを確認した後、 **[保存]** をクリックします。
   ![アプリのボリューム ストレージを選択するスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. **[続行]** をクリックして、コンテナー イメージのビルド フェーズに進みます。

## <a name="build-container-image"></a>コンテナー イメージの構築


1. **Azure Container Registry を選択する**: ドロップダウンを使用して、アプリのコンテナー イメージのビルドおよび保存に使用する [Azure Container Registry](../container-registry/index.yml) を選択します。 既存の Azure Container Registry を使用すること、または [Create new registry]\(新しいレジストリの作成\) オプションを使用して、新しいレジストリを作成することができます。

    ![アプリの ACR 選択のスクリーンショット。](./media/tutorial-containerize-apps-aks/build-java-app.png)

> [!NOTE]
> 管理者ユーザーが有効になっている Azure コンテナー レジストリのみが表示されます。 Azure コンテナー レジストリから Azure App Service にイメージをデプロイするには、管理者アカウントが現在必要です。 [詳細情報](../container-registry/container-registry-authentication.md#admin-account)

2. **Dockerfile を確認する**: 選択した各アプリケーションのコンテナー イメージをビルドするために必要な Dockerfile がビルド ステップの開始時に生成されます。 **[確認]** をクリックして Dockerfile を確認します。 ビルド プロセスを開始する前に、確認ステップで、必要なカスタマイズを Dockerfile に追加して変更を保存することもできます。

3. **Application Insights の構成**: コードをインストルメント化することなく、App Service で実行されている Java アプリの監視を有効にすることができます。 このツールを使用すると、Java スタンドアロン エージェントがコンテナー イメージの一部としてインストールされます。 Java エージェントがデプロイ中に構成されると、Application Insights での監視に使用できる、アプリケーションに関する多くの要求、依存関係、ログ、およびメトリックが自動的に収集されます。 このオプションは、すべての Java アプリケーションで既定で有効になっています。  

4. **ビルド プロセスをトリガーする**: イメージをビルドするアプリケーションを選択し、 **[ビルド]** をクリックします。 [ビルド] をクリックすると、各アプリケーションのコンテナー イメージのビルドが開始されます。 このツールはビルドの状態を継続的に監視します。ビルドが正常に完了したら、次の手順に進むことができます。

5. **ビルドの状態を追跡する**: 状態列の下にある **[進行中のビルド]** リンクをクリックすることで、ビルド ステップの進行状況を監視することもできます。 ビルド プロセスをトリガーした後、リンクがアクティブになるまで数分かかります。  

6. ビルドが完了したら、 **[続行]** をクリックしてデプロイ設定を指定します。

    ![アプリのコンテナー イメージのビルドが完了したスクリーンショット。](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-azure-app-service"></a>コンテナー化されたアプリを Azure App Service にデプロイする

コンテナー イメージがビルドされたら、次の手順は、[Azure App Service](https://azure.microsoft.com/services/app-service/) にアプリケーションをコンテナーとしてデプロイすることです。

1. **Azure App Service プランを選択する**: アプリケーションで使用する Azure App Service プランを指定します。

     - App Service プランがない場合、または新しい App Service プランを作成して使用する場合、 **[新しい App Service プランの作成]** をクリックして、ツールから作成することを選択できます。      
     - App Service プランを選択した後、 **[続行]** をクリックします。

2. **シークレット ストアと監視ワークスペースを指定する**: アプリケーション構成のパラメーター化を選択した場合は、アプリケーションに使用するシークレット ストアを指定します。 アプリケーション シークレットの管理には、Azure Key Vault または App Service アプリケーションの設定を選択できます。 [詳細情報](../app-service/configure-common.md#configure-connection-strings)

     - シークレットを管理するために App Service アプリケーション設定を選択した場合は、 **[続行]** をクリックします。
     - アプリケーション シークレットの管理に Azure Key Vault を使用する場合は、使用する Azure Key Vault を指定します。     
         - Azure Key Vault がない場合、または新しい Key Vault を作成する場合は、 **[新規作成]** をクリックして、ツールからの作成を選択できます。
         - このツールを使用すると、Key Vault を通じてシークレットを管理するために必要なアクセス許可が、自動的に割り当てられます。
    - **監視ワークスペース**: Application Insights での監視を有効にすることを選択した場合は、使用する Application Insights リソースを指定します。 このオプションは、監視統合を無効にした場合は表示されません。
         - Application Insights リソースがない場合、または新しいリソースを作成する場合は、 **[新規作成]** をクリックして、ツールからの作成を選択できます。

3. **Azure ファイル共有を指定する**: ディレクトリまたはフォルダーをさらに追加し、永続ストレージとして Azure ファイル共有オプションを選択した場合は、デプロイ プロセス中に Azure Migrate: App Containerization ツールで使用される Azure ファイル共有を指定します。 ツールでは Azure Files 用に構成されたアプリケーション ディレクトリまたはフォルダーをコピーし、デプロイ中にこれらをアプリケーション コンテナーにマウントします。 

     - Azure ファイル共有がない場合、または新しい Azure ファイル共有を作成する場合は、 **[Create new Storage Account and file share]\(新しいストレージ アカウントとファイル共有の作成\)** をクリックすることで、ツールから作成できます。  

4. **アプリケーションのデプロイ構成**: 上記の手順が完了したら、アプリケーションのデプロイ構成を指定する必要があります。 **[構成]** をクリックして、アプリケーションのデプロイをカスタマイズします。 構成ステップでは、次のカスタマイズを行うことができます。
     - **名前**: アプリケーションの一意のアプリ名を指定します。 この名前はアプリケーションの URL を生成するために使用され、このデプロイの一部として作成される他のリソースのプレフィックスとして使用されます。
     - **アプリケーションの構成**: パラメーター化されたすべてのアプリケーションの構成について、現在のデプロイに使用する値を指定します。
     - **ストレージの構成**: 永続ストレージ用に構成されたアプリケーション ディレクトリまたはフォルダーの情報を確認します。

    ![デプロイ アプリ構成のスクリーンショット。](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

5. **アプリケーションをデプロイする**: アプリケーションのデプロイ構成が保存されると、ツールによって、アプリケーションの Kubernetes デプロイ YAML が生成されます。
     - **[確認]** をクリックして、アプリケーションのデプロイ構成を確認します。
     - デプロイするアプリケーションを選択します。
     - **[デプロイ]** をクリックして、選択したアプリケーションのデプロイを開始します。

         ![アプリのデプロイ構成のスクリーンショット。](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - アプリケーションがデプロイされたら、 *[デプロイ ステータス]* 列をクリックして、アプリケーション用にデプロイされたリソースを追跡できます。


## <a name="troubleshoot-issues"></a>問題のトラブルシューティング

ツールに関する問題をトラブルシューティングするには、App Containerization ツールを実行している Windows コンピューターにあるログ ファイルを確認します。 ツールのログ ファイルは *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* フォルダーにあります。

## <a name="next-steps"></a>次のステップ

- (Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナー化し、それらを AKS の Linux コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-java-kubernetes.md)
- ASP.NET Web アプリをコンテナー化し、それらを AKS の Windows コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-aspnet-kubernetes.md)
- ASP.NET Web アプリをコンテナー化し、それらを Azure App Service の Windows コンテナーにデプロイする。 [詳細情報](./tutorial-app-containerization-aspnet-app-service.md)