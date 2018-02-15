1. ブラウザーで、[Jenkins 用の Azure Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)を開きます。

1. **[今すぐ入手する]** を選択します。

    ![[今すぐ入手する] を選択して、Jenkins Marketplace イメージのインストール プロセスを開始する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 料金の詳細と使用条件についての情報を確認した後、**[続行]** を選択します。

    ![Jenkins Marketplace イメージの料金と使用条件の情報。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. **[作成]** を選択して、Azure ポータルで Jenkins サーバーを構成します。 

    ![Jenkins Marketplace イメージをインストールする。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. **[基本]** タブで、次の値を指定します。

    - **名前**: 「`Jenkins`」と入力します。
    - **ユーザー**: Jenkins が実行される仮想マシンへのサインイン時に使用するユーザー名を入力します。
    - **認証の種類**: **[パスワード]** を選択します。
    - **パスワード**: Jenkins が実行される仮想マシンへのサインイン時に使用するパスワードを入力します。
    - **パスワードの確認**: Jenkins が実行される仮想マシンへのサインイン時に使用するパスワードを再入力します。
    - **Jenkins のリリースの種類**: **[LTS]** を選択します。
    - **サブスクリプション**: Jenkins をインストールする Azure サブスクリプションを選択します。
    - **リソース グループ**: **[新規作成]** を選択し、Jenkins のインストールを構成するリソースのコレクションの論理コンテナーとして機能するリソース グループの名前を入力します。
    - **場所**: **[米国東部]** を選択します。

    ![[基本] タブに Jenkins の認証とリソース グループの情報を入力する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. **[OK]** を選択して、**[設定]** タブに進みます。 

1. **[設定]** タブで、次の値を指定します。

    - **サイズ**: Jenkins 仮想マシンの適切なサイズ オプションを選択します。
    - **VM ディスクの種類**: Jenkins 仮想マシンで許可されるストレージ ディスクの種類を示すために、[HDD] \(ハード ディスク ドライブ) または [SSD] \(ソリッドステート ドライブ) のどちらかを指定します。
    - **パブリック IP アドレス**: IP アドレス名の既定値は、前のページで指定した Jenkins 名にサフィックス-IP が付いたものになります。 この既定値を変更するオプションを選択できます。
    - **ドメイン名ラベル**: Jenkins 仮想マシンの完全修飾 URL の値を指定します。

    ![[設定] タブで、Jenkins の仮想マシン設定を入力する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. **[OK]** を選択して、**[概要]** タブに進みます。

1. **[概要]** タブが表示されれば、入力した情報は検証されています。 「**検証に成功しました** 」というメッセージを確認し、**[OK]** をクリックします。 

    ![[概要] タブが表示され、選択したオプションが検証される。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. **[作成]** タブが表示されたら、**[作成]** を選択して Jenkins 仮想マシンを作成します。 サーバーの準備が完了すると、Azure ポータルに通知が表示されます。

    ![Jenkins の準備が完了したことを示す通知。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)