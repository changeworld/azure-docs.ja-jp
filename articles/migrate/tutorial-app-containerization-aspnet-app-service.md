---
title: ASP.NET アプリのコンテナ化と App Service への移行
description: このチュートリアルでは、ASP.NET アプリケーションをコンテナ化し、Azure App Service に移行する方法について説明します。
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 07/02/2021
ms.author: rahugup
ms.openlocfilehash: 14af442ff9a25178da2f8c6289bd988cae3bafd7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046945"
---
# <a name="aspnet-app-containerization-and-migration-to-azure-app-service"></a>ASP.NET アプリのコンテナー化と Azure App Service への移行

この記事では、Azure Migrate App Containerization ツールを使用して、ASP.NET アプリケーションをコンテナ化し、[Azure App Service](https://azure.microsoft.com/services/app-service/) に移行する方法について説明します。 このコンテナ化プロセスでは、コードベースへのアクセスは不要で、既存のアプリケーションを簡単な方法でコンテナ化できます。 このツールは、サーバー上のアプリケーションの実行状態を使用して動作し、アプリケーション コンポーネントを判別します。 次に、それらをコンテナー イメージにパッケージ化することを支援します。 その後、コンテナ化されたアプリケーションを、Azure App Service にデプロイできます。

Azure Migrate App Containerization ツールは、現時点で以下をサポートしています。

- ASP.NET アプリをコンテナー化し、それらを App Service の Windows コンテナーにデプロイする。
- ASP.NET アプリをコンテナ化し、Azure Kubernetes Service (AKS) の Windows コンテナーにデプロイする。 [このコンテナ化シナリオについて詳細を確認してください。](./tutorial-app-containerization-aspnet-kubernetes.md)
- (Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナー化し、それらを AKS の Linux コンテナーにデプロイする。 [このコンテナ化シナリオについて詳細を確認してください。](./tutorial-app-containerization-java-kubernetes.md)
- (Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナー化し、それらを App Service の Linux コンテナーにデプロイする。 [このコンテナ化シナリオについて詳細を確認してください。](./tutorial-app-containerization-java-app-service.md)

App Containerization ツールを使用すると、次のことができます。

- **アプリケーション コンポーネントを検出する。** このツールは、ASP.NET アプリケーションが実行されているアプリケーション サーバーにリモート接続し、アプリケーション コンポーネントを検出します。 これにより、アプリケーションのコンテナー イメージを作成するために使用できる Dockerfile が作成されます。
- **コンテナー イメージをビルドします。** Dockerfile を検査し、アプリケーションの要件に基づいてさらにカスタマイズし、それを使用してアプリケーション コンテナー イメージをビルドできます。 アプリケーション コンテナー イメージは、指定した Azure コンテナー レジストリにプッシュされます。
- **Azure App Service にデプロイする。**  その後、ツールにより、コンテナ化されたアプリケーションを Azure App Service にデプロイするために必要なデプロイ ファイルが生成されます。

> [!NOTE]
> Azure Migrate App Containerization ツールを使用すると、アプリケーション サーバー上の特定のアプリケーションの種類 (ASP.NET と Apache Tomcat 上の Java Web アプリ) とそのコンポーネントを検出できます。 オンプレミスのコンピューターで実行されているサーバーと、アプリ、ロール、機能のインベントリを検出するには、[Azure Migrate 検出および評価のツール](./tutorial-discover-vmware.md)を使用します。

すべてのアプリケーションが、大幅な再設計なしでコンテナーに直接シフトすることの恩恵を受けるわけではありません。 しかし、既存のアプリを書き換えずにコンテナーに移動する利点としては、次のようなものがあります。

- **インフラストラクチャ使用率の向上。** コンテナーを使用すると、複数のアプリケーションがリソースを共有でき、同じインフラストラクチャ上でホストされるようにできます。 これにより、インフラストラクチャを統合し、使用率を向上させることができます。
- **簡略化された管理。** AKS や App Service などの最新のマネージド プラットフォームでアプリケーションをホストすることにより、管理作業を簡略化できます。 この簡略化は、所有するインフラストラクチャでこれまで実行していた、インフラストラクチャのメンテナンスおよび管理のプロセスを廃止または削減することで実現できます。
- **アプリケーションの移植性。** コンテナー仕様の形式およびプラットフォームの導入と標準化が進んだことで、アプリケーションの移植性は問題ではなくなりました。
- **DevOps を使用した最新の管理の導入。** コンテナーを使用すると、管理、セキュリティ、DevOps への移行のために最新の手法を導入し、標準とすることができます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * Azure Migrate App Containerization ツールをインストールします。
> * ASP.NET アプリケーションを検出します。
> * コンテナー イメージをビルドします。
> * コンテナー化されたアプリケーションを App Service にデプロイします。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルでは、可能であれば既定のオプションが使用され、設定とパスがすべて示されているわけではありません。

## <a name="prerequisites"></a>前提条件

このため、このチュートリアルの前に、以下を行う必要があります。

**要件** | **詳細**
--- | ---
**ツールをインストールするマシンを特定する** | Azure Migrate App Containerization ツールをインストールして実行するには、Windows マシンが必要です。 Windows マシンでは、サーバー (Windows Server 2016 以降) またはクライアント (Windows 10) オペレーティング システムを実行できます。 (このツールはデスクトップで実行できます。) <br/><br/> ツールが実行される Windows マシンには、コンテナ化する ASP.NET アプリケーションをホストするサーバーまたは仮想マシンへのネットワーク接続が必要です。<br/><br/> Azure Migrate App Containerization ツールが実行される Windows マシンで 6 GB が使用可能であることを確認します。 この領域は、アプリケーション成果物の格納用です。 <br/><br/> Windows コンピューターは、直接またはプロキシ経由でインターネットにアクセスできる必要があります。 <br/> <br/>App Containerization ツールとアプリケーション サーバーが実行されるマシンに Microsoft Web 配置ツールをインストールします (まだインストールされていない場合)。 [このツールをダウンロード](https://aka.ms/webdeploy3.6)できます。
**アプリケーション サーバー** | アプリケーション サーバーで PowerShell リモート処理を有効にする: アプリケーション サーバーにサインインし、[PowerShell リモート処理を有効にするこちらの手順](/powershell/module/microsoft.powershell.core/enable-psremoting)に従います。 <br/><br/> アプリケーション サーバーで Windows Server 2008 R2 が実行されている場合は、そのアプリケーション サーバーに PowerShell 5.1 がインストールされていることを確認します。 アプリケーション サーバーに [PowerShell 5.1 をダウンロードしてインストールするこちら](/powershell/scripting/windows-powershell/wmf/setup/install-configure)の手順に従います。 <br/><br/> App Containerization ツールとアプリケーション サーバーが実行されるマシンに Microsoft Web 配置ツールをインストールします (まだインストールされていない場合)。 [このツールをダウンロード](https://aka.ms/webdeploy3.6)できます。
**ASP.NET アプリケーション** | このツールでは現在、次のものがサポートされています。 <br> <ul><li> .NET Framework 3.5 以降を使用している ASP.NET アプリケーション。<br/> <li>Windows Server 2008 R2 以降を実行しているアプリケーション サーバー。 (アプリケーション サーバーは PowerShell 5.1 を実行している必要があります。) <br/><li> インターネット インフォメーション サービス 7.5 以降で実行されているアプリケーション。</ul> <br/><br/> このツールでは現在、次のものはサポートされていません。 <br/> <ul><li>Windows 認証を必要とするアプリケーション。 (現在、AKS では gMSA がサポートされていません。) <br/> <li> インターネット インフォメーション サービスの外部でホストされている他の Windows サービスに依存するアプリケーション。


## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/pricing/free-trial/)してください。

サブスクリプションが設定されたら、以下を備えた Azure ユーザー アカウントが必要になります。
- Azure サブスクリプションの所有者アクセス許可。
- Azure Active Directory アプリを登録するためのアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で「サブスクリプション」と検索します。 **[サービス]** で、 **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックスを示すスクリーンショット。](./media/tutorial-discover-vmware/search-subscription.png)

2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。
3. サブスクリプションの左側のペインで、 **[アクセス制御 (IAM)]** を選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で **[追加]** を選択します。

    ![アクセスの確認とロールの割り当てを行うユーザー アカウントの検索を示すスクリーンショット。](./media/tutorial-discover-vmware/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、 **[所有者]** ロールを選択し、アカウント (この例では **azmigrateuser**) を選択します。 次に、 **[保存]** を選択します。

    ![[ロールの割り当てを追加する] ページを示すスクリーンショット。](./media/tutorial-discover-vmware/assign-role.png)

   Azure アカウントには、Azure Active Directory アプリを登録するためのアクセス許可も必要です。
8. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
9. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します。 (このオプションは、既定で **[はい]** に設定されています。)

      ![[ユーザー設定] ページを示すスクリーンショット。](./media/tutorial-discover-vmware/register-apps.png)

10. **[アプリの登録]** オプションが **[いいえ]** に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、Azure Active Directory アプリの登録を許可するために、アプリケーション開発者ロールをアカウントに割り当てることもできます。 詳細については、「[ユーザーにロールを割り当てる](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)」を参照してください。

## <a name="download-and-install-the-azure-migrate-app-containerization-tool"></a>Azure Migrate App Containerization ツールをダウンロードしてインストールする

1. Windows マシンに [Azure Migrate App Containerization インストーラーをダウンロードします](https://go.microsoft.com/fwlink/?linkid=2134571)。
2. 管理者モードで PowerShell を開き、PowerShell ディレクトリをインストーラーが格納されているフォルダーに変更します。
3. このコマンドを使用して、インストール スクリプトを実行します。

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="open-the-app-containerization-tool"></a>App Containerization ツールを開く

1. App Containerization ツールが実行されている Windows マシンに接続できるマシンでブラウザーを開きます。 ツールの URL: **https:// *<マシン名または IP アドレス>* : 44369** に移動します。

   または、アプリのショートカットを使用して、デスクトップからアプリを開くこともできます。

2. 接続がプライベートではないという警告が表示された場合は、 **[詳細設定]** を選択して、Web サイトに進みます。 この警告が表示されるのは、Web インターフェイスで自己署名の TLS/SSL 証明書が使用されているためです。
3. サインイン画面で、マシンのローカル管理者アカウントを使用してサインインします。
4. コンテナー化するアプリケーションの種類として **[ASP.NET Web アプリ]** を選択します。
5. **[Target Azure service]\(ターゲット Azure サービス\)** リストから **[Containers on Azure App Service]\(Azure App Service 上のコンテナー\)** を選択します。

   ![アプリケーションの種類とターゲット リストを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/tool-home.png)

### <a name="complete-the-tool-prerequisites"></a>ツールの前提条件を満たす
1. ライセンス条項に同意し、サードパーティの情報に目を通します。
6. ツール Web アプリの **[前提条件のセットアップ]** で、こちらの手順を実行します。
   - **接続性。** ツールによって、Windows マシンがインターネットにアクセスできるかどうかがチェックされます。 コンピューターがプロキシを使用する場合:
     1. **[プロキシの設定]** を選択し、プロキシ アドレス (IP アドレスまたは FQDN の形式) とリスニング ポートを指定します。
     1. プロキシで認証が必要な場合は、資格情報を指定します。
       
     1. プロキシの詳細を追加した場合、あるいはプロキシまたは認証を無効にした場合は、 **[保存]** を選択して接続チェックを再度トリガーします。
     
     サポートされるのは HTTP プロキシのみです。
   - **更新プログラムをインストールする。** ツールによって、最新の更新プログラムが自動的にチェックされ、インストールされます。 [最新バージョンのツールを手動でインストールする](https://go.microsoft.com/fwlink/?linkid=2134571)こともできます。
   - **Microsoft Web 配置ツールをインストールする。** ツールによって、Azure Migrate App Containerization ツールが実行されている Windows マシンに Microsoft Web 配置ツールがインストールされているかどうかがチェックされます。
   - **PowerShell リモート処理を有効にする。** ツールによって、コンテナ化する ASP.NET アプリケーションが実行されているアプリケーション サーバーで PowerShell リモート処理が有効になっていることを確認するようプロンプトが表示されます。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. **[サインイン]** を選択して、Azure アカウントにサインインします。

   Azure で認証するには、デバイス コードが必要です。 **[サインイン]** を選択すると、デバイス コードを含むウィンドウが開きます。 ウィンドウが表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. **[Copy code and Sign in]\(コードのコピーとサインイン\)** を選択してデバイス コードをコピーし、新しいブラウザー タブで Azure サインイン プロンプトを開きます。

    ![[Device code for Azure Sign in]\(Azure サインインのデバイス コード\) ウィンドウを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/login-modal.png)

3. 新しいタブで、デバイス コードを貼り付け、Azure アカウントの資格情報を使用してサインインを完了します。 サインインしたら、ブラウザー タブを閉じて、App Containerization ツールの Web インターフェイスに戻ることができます。
4. 使用する **Azure テナント** を選択します。
5. 使用する **Azure サブスクリプション** を指定します。

## <a name="discover-aspnet-applications"></a>ASP.NET アプリケーションを検出する

App Containerization ツールは、指定された資格情報を使用してアプリケーション サーバーにリモート接続し、アプリケーション サーバーでホストされている ASP.NET アプリケーションの検出を試みます。

1. ASP.NET アプリケーションが実行されているサーバーの **サーバー IP アドレスまたは FQDN** と資格情報を指定します。アプリケーション検出のために、これらを使用してサーバーにリモート接続する必要があります。
    - 指定する資格情報は、アプリケーション サーバーのローカル管理者 (Windows) のものである必要があります。
    - ドメイン アカウントの場合 (ユーザーはアプリケーション サーバーの管理者である必要があります)、 *<ドメイン\ユーザー名>* の形式で、ユーザー名の前にドメイン名をプレフィックスとして付加します。
    - ローカル アカウントの場合 (ユーザーはアプリケーション サーバーの管理者である必要があります)、 *<ホスト名\ユーザー名>* の形式で、ユーザー名の前にホスト名をプレフィックスとして付加します。
    - アプリケーションの検出は、5 台のサーバーに対して同時に実行できます。

2. **[検証]** を選択して、ツールが実行されているマシンからアプリケーション サーバーに到達できること、および資格情報が有効であることを確認します。 検証が正常に完了すると、 **[状態]** 列に **[マップ済み]** として状態が表示されます。

    ![サーバーの状態が [マップ済み] として示されているスクリーンショット。](./media/tutorial-containerize-apps-aks/discovery-credentials-asp.png)

3. **[続行]** を選択して、選択したアプリケーション サーバーに対するアプリケーションの検出を開始します。

4. アプリケーションの検出が完了したら、コンテナ化するアプリケーションを選択します。

    ![検出された ASP.NET アプリケーションを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app-asp.png)

6. 選択した各アプリケーションのターゲット コンテナーの名前を指定します。 コンテナー名は <"*名前:タグ*"> として指定します。ここで、"*タグ*" はコンテナー イメージに使用されます。 たとえば、*appname:v1* というターゲット コンテナー名を指定できます。   

### <a name="parameterize-application-configurations"></a>アプリケーションの構成をパラメーター化する
構成をパラメーター化すると、デプロイ時のパラメーターとして使用できます。 パラメーター化により、設定をコンテナー イメージ内の特定の値にハードコーディングするのではなく、アプリケーションのデプロイ時に構成できます。 たとえば、このオプションは、データベース接続文字列などのパラメーターに便利です。
1. **[app configurations]\(アプリの構成\)** を選択して、検出された構成を確認します。
2. パラメーター化するパラメーターを選択し、 **[適用]** を選択します。

   ![検出された構成の一覧を示すスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app-configs-asp.png)

### <a name="externalize-file-system-dependencies"></a>ファイル システムの依存関係を外部化する

 アプリケーションで使用する他のフォルダーを追加できます。 それらをコンテナー イメージの一部にするか、Azure ファイル共有を使用して永続ストレージに外部化するかを指定します。 外部永続ストレージの使用は、コンテナーの外部に状態を保存する、またはファイル システムに他の静的コンテンツを保存するステートフル アプリケーションに適しています。

1. **[Application folders]\(アプリケーション フォルダー\)** の下の **[編集]** を選択して、検出されたアプリケーション フォルダーを確認します。 これらのフォルダーは、アプリケーションに必要な必須の成果物として識別されています。 これらはコンテナー イメージにコピーされます。

2. **[フォルダーの追加]** を選択し、追加するフォルダーのパスを指定します。
3. 複数のフォルダーを同じボリュームに追加するには、値をコンマで区切ります。
4. フォルダーをコンテナーの外部の永続ストレージ上に保存する場合は、ストレージ オプションとして **[Azure ファイル共有]** を選択します。
5. アプリケーション フォルダーを確認した後、 **[保存]** を選択します。

   ![[Edit application folders]\(アプリケーション フォルダーの編集\) ウィンドウを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/discovered-app-volumes-asp.png)

6. **[続行]** を選択して、コンテナー イメージのビルド フェーズに進みます。

## <a name="build-container-image"></a>コンテナー イメージの構築


1. ドロップダウン リストで、アプリのコンテナー イメージのビルドおよび保存に使用する [Azure コンテナー レジストリ](../container-registry/index.yml)を選択します。 既存の Azure コンテナー レジストリを使用することも、 **[Create new registry]\(新しいレジストリの作成\)** を選択して新規に作成することもできます。

    ![[Build images]\(イメージのビルド\) ウィンドウを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)

   > [!NOTE]
   > 管理者ユーザー アカウントが有効になっている Azure コンテナー レジストリのみが表示されます。 Azure コンテナー レジストリから Azure App Service にイメージをデプロイするには、管理者ユーザー アカウントが現在必要です。 詳細については、「[Azure コンテナー レジストリでの認証](../container-registry/container-registry-authentication.md#admin-account)」を参照してください。

2. 選択した各アプリケーションのコンテナー イメージをビルドするために必要な Dockerfile がビルド ステップの開始時に生成されます。 **[確認]** を選択して、Dockerfile を確認します。 ビルド プロセスを開始する前に、確認ステップで、Dockerfile に必要なカスタマイズを追加して変更を保存することもできます。

3. イメージをビルドするアプリケーションを選択し、 **[ビルド]** を選択します。 **[ビルド]** を選択すると、各アプリケーションのコンテナー イメージのビルドが開始されます。 このツールはビルドの状態を監視します。ビルドが終了したら、次の手順に進むことができます。

4.  状態列の下にある **[Build in Progress]\(進行中のビルド\)** を選択することで、ビルドの進行状況を監視できます。 このリンクは、ビルド プロセスをトリガーした数分後にアクティブになります。  

5. ビルドが完了したら、 **[続行]** を選択して、デプロイ設定を指定します。

    ![[確認] リンク、コンテナー イメージの状態、[ビルド] と [続行] ボタンを示すスクリーンショット。](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-azure-app-service"></a>コンテナー化されたアプリを Azure App Service にデプロイする

コンテナー イメージがビルドされたら、次の手順は、[Azure App Service](https://azure.microsoft.com/services/app-service/) にアプリケーションをコンテナーとしてデプロイすることです。

1. アプリケーションで使用する Azure App Service プランを選択します。

   App Service プランがない場合、または新しい App Service プランを作成して使用する場合、 **[新しい App Service プランの作成]** を選択して作成できます。      
1. App Service プランを選択した後、 **[続行]** を選択します。

2. アプリケーション構成をパラメーター化した場合は、アプリケーションに使用するシークレット ストアを指定します。 アプリケーション シークレットを管理するには、Azure Key Vault または App Service アプリケーション設定を選択できます。 詳細については、「[接続文字列の構成](../app-service/configure-common.md#configure-connection-strings)」を参照してください。

     - シークレットを管理するために App Service アプリケーション設定を選択した場合は、 **[続行]** を選択します。
     - Azure キー コンテナーを使用してアプリケーション シークレットを管理する場合は、使用するキー コンテナーを指定します。     
         - Azure キー コンテナーがない場合、または新しいキー コンテナーを作成する場合は、 **[Create new Azure Key Vault]\(Azure キー コンテナーの新規作成\)** を選択して作成できます。
         - ツールにより、キー コンテナーを使用してシークレットを管理するために必要なアクセス許可が自動的に割り当てられます。

3. フォルダーをさらに追加し、永続ストレージとして Azure ファイル共有オプションを選択した場合は、デプロイ中に App Containerization ツールで使用される Azure ファイル共有を指定します。 Azure Files 用に構成したアプリケーション フォルダーがツールによってコピーされ、デプロイ中にアプリケーション コンテナーにマウントされます。 

   Azure ファイル共有がない場合、または新しい Azure ファイル共有を作成する場合は、 **[Create new Storage Account and file share]\(新しいストレージ アカウントとファイル共有の作成\)** を選択して作成できます。  

4. ここで、アプリケーションのデプロイ構成を指定する必要があります。 アプリケーションのデプロイをカスタマイズするには、 **[構成]** を選択します。 構成ステップでは、こちらのカスタマイズを行うことができます。
     - **名前。** アプリケーションの一意のアプリ名を指定します。 この名前は、アプリケーションの URL を生成するために使用されます。 また、デプロイの一部として作成された他のリソースのプレフィックスとしても使用されます。
     - **アプリケーションの構成。** パラメーター化されたアプリケーションの構成について、現在のデプロイに使用する値を指定します。
     - **ストレージの構成。** 永続ストレージ用に構成されたアプリケーション フォルダーの情報を確認します。

    ![デプロイ構成を示すスクリーンショット。](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

5. アプリケーションのデプロイ構成を保存すると、ツールによって、アプリケーションの Kubernetes デプロイ YAML が生成されます。
     - **[確認]** を選択して、アプリケーションのデプロイ構成を確認します。
     - デプロイするアプリケーションを選択します。
     - **[デプロイ]** を選択して、選択したアプリケーションのデプロイを開始します。

         ![[デプロイ] ボタンを示すスクリーン ショット。](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - アプリケーションがデプロイされたら、 **[展開状態]** 列を選択して、アプリケーション用にデプロイされたリソースを追跡できます。


## <a name="troubleshoot-problems"></a>問題のトラブルシューティング

App Containerization ツールに関する問題をトラブルシューティングするには、ツールが実行されている Windows マシン上のログ ファイルを調べます。 ツールのログ ファイルは *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* にあります。

## <a name="next-steps"></a>次のステップ

- [ASP.NET Web アプリをコンテナ化し、それらを AKS の Windows コンテナーにデプロイする](./tutorial-app-containerization-aspnet-kubernetes.md)
- [(Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナ化し、それらを AKS の Linux コンテナーにデプロイする](./tutorial-app-containerization-java-kubernetes.md)
- [(Linux サーバーの) Apache Tomcat 上の Java Web アプリをコンテナ化し、それらを App Service の Linux コンテナーにデプロイする](./tutorial-app-containerization-java-app-service.md)
