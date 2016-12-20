---
title: "Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法 | Microsoft Docs"
description: "Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法
Jenkins 用の Azure スレーブ プラグインを使用すると、分散されたビルドを実行するときにスレーブ ノードを Azure に簡単にプロビジョニングできます。このプラグインでは、以下の作成がサポートされます。

* SSH および Java Network Launch Protocol (JNLP) を使用する Azure クラウド上の Windows スレーブ
  
  * SSH から Windows イメージを起動するには、SSH を使用してイメージを事前構成しておく必要があります。
  * カスタムの Windows イメージを準備する方法については、「[Resource Manager デプロイ モデルで Windows 仮想マシンをキャプチャする方法][windows-image-capture]」をご覧ください。
* SSH を使用する Azure クラウド上の Linux スレーブ
  
  * カスタムの Linux イメージを準備する方法については、「[リソース マネージャーのテンプレートとして使用する Linux 仮想マシンをキャプチャする方法][linux-image-capture]」をご覧ください。

### <a name="prerequisites"></a>前提条件
この記事の手順を実行する前に、クライアント アプリケーションを登録および認証し、認証中に Azure Active Directory に送信されるクライアント ID とクライアント シークレットを取得する必要があります。 これらの前提条件の詳細については、次の記事を参照してください。

* [Azure Active Directory とアプリケーションの統合][integrate-apps-with-AAD]
* [クライアント アプリを登録する][register-client-app]

さらに、この記事の「[Azure のスレーブ ノードで実行される Jenkins ジョブを作成する](#create-jenkins-project)」セクションの手順を実行するために、GitHub でプロジェクトをセットアップしておく必要があります。

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Azure スレーブ プラグインをインストールする
1. Jenkins ダッシュボードで、 **[Manage Jenkins]**をクリックします。
   
    ![[Jenkins の管理]][jenkins-dashboard-manage]
2. **[Jenkins の管理]** ページで、**[プラグインの管理]** をクリックします。
   
    ![[プラグインの管理]][jenkins-manage-plugins]
3. **[利用可能]** タブをクリックし、フィルターとして「Azure」を入力して、**[Azure Slave Plugin]** を選択します。 
   
    ![[Azure Slave Plugin]][search-plugins]
   
    使用可能なプラグインの一覧をスクロールする場合、Azure スレーブ プラグインは **[クラスター管理/分散ビルド]** セクションで見つかります。
4. **[再起動せずにインストール]** または **[ダウンロードして再起動後にインストール]** をクリックします。
   
    ![プラグインのインストール][install-plugin]

これでプラグインがインストールされたため、次の手順では、Azure サブスクリプション プロファイルを使用してプラグインを構成し、スレーブ ノード用の仮想マシンの作成に使用するテンプレートを作成します。

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>サブスクリプション プロファイルを使用して Azure スレーブ プラグインを構成する
サブスクリプション プロファイル (発行設定) は、セキュリティで保護された資格情報と、開発環境で Azure を操作するために必要な追加情報を含む XML ファイルです。 Azure スレーブ プラグインを構成するには、以下が必要です。

* サブスクリプション ID
* サブスクリプション用の管理証明書

これらは、[サブスクリプション プロファイル]で確認できます。サブスクリプション プロファイルの例を以下に示します。

        <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
            <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
                <Subscription
                    ServiceManagementUrl="https://management.core.windows.net"
                    Id="<Subscription ID value>"
                    Name="Pay-As-You-Go"
                    ManagementCertificate="<Management certificate value>" />
            </PublishProfile>
        </PublishData>

サブスクリプション プロファイルを用意したら、次の手順に従って Azure スレーブ プラグインを構成します。

1. Jenkins ダッシュボードで、 **[Manage Jenkins]**をクリックします。
   
    ![[Jenkins の管理]][jenkins-dashboard-manage]
2. **[Configure System]**をクリックします。
   
    ![[システムの設定]][jenkins-configure-system]
3. ページを下にスクロールして **[Cloud]** セクションを探します。
4. **[追加]**、**[Microsoft Azure]** の順にクリックします。
   
    ![新しいクラウドの追加][cloud-section]
   
    サブスクリプションの詳細を入力する必要のあるフィールドが表示されます。
   
    ![サブスクリプションの構成][subscription-configuration]
5. サブスクリプション プロファイルからサブスクリプションID と管理証明書の値をコピーし、適切なフィールドに貼り付けます。
   
    サブスクリプション ID と管理証明書をコピーするときは、値を囲む引用符は含めないでください。
6. **[Verify Configuration (構成の確認)]** をクリックして、指定したパラメーターが有効であることを確認し、**[保存]** をクリックします。

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Azure スレーブ プラグイン用の仮想マシン テンプレートを設定する
このセクションでは、プラグインが Azure でスレーブ ノードの作成に使用するパラメーターを定義する、仮想マシン テンプレートを追加します。 次の手順では、Ubuntu 仮想マシン用のテンプレートを作成します。

1. Jenkins ダッシュボードで、 **[Manage Jenkins]**をクリックします。
   
    ![[Jenkins の管理]][jenkins-dashboard-manage]
2. **[Configure System]**をクリックします。
   
    ![[システムの設定]][jenkins-configure-system]
3. ページを下にスクロールして **[Cloud]** セクションを探します。
4. **[クラウド]** セクションで、前のセクションで追加したクラウドの **[Add Azure Virtual Machine Template (Azure 仮想マシン テンプレートの追加)]** を探し、**[追加]** をクリックします。
   
    ![[Add Azure Virtual Machine Template (Azure 仮想マシン テンプレートの追加)]][add-vm-template]
   
    フォームが表示され、作成するテンプレートの詳細をフォーム内のフィールドに入力します。
   
    ![空白の [General Configuration (全般構成)]][blank-general-configuration]
5. テンプレートの **[General Configuration (全般構成)]** の情報を入力します。
   
   1. **[名前]** ボックスに、新しいテンプレートの名前を入力します。この名前は自分用のみに使用し、仮想マシンのプロビジョニングでは使用されません。
   2. **[Description]** ボックスに、作成するテンプレートを説明するテキストを入力します。 これは記録のみを目的としており、仮想マシンのプロビジョニングでは使用されません。
   3. **[Labels]** ボックスは作成中のテンプレートを識別するために使用され、その後、Jenkins ジョブを作成するときにテンプレートを参照するために使用されます。 この手順では、このボックスに「**linux**」と入力します。
   4. **[Region (リージョン)]** ボックスの一覧で、仮想マシンを作成する Azure リージョンをクリックします。
   5. **[Virtual Machine Size (仮想マシンのサイズ)]** ボックスの一覧で、ドロップダウン メニューから適切なサイズを選択します。
   6. **[Storage Account Name]** ボックスに、仮想マシンを作成するストレージ アカウントを指定します。 既定値 "jenkinsarmst" を使用してストレージ アカウントが作成されるようにするには、このボックスを空白のままにします。
   7. **[Retention Time (保存期間)]** は、その時間を経過するとJenkins がアイドル状態のスレーブを削除する分数を指定します。これは、既定値 60 のままにします。
      
      * スレーブは、アイドル状態になったときに削除するのではなくシャットダウンすることもできます。 そのためには、 **[Shutdown Only (Do Not Delete) After Retention Time]** チェック ボックスをオンにします。
      * また、アイドル状態のスレーブが自動的に削除されないようにする場合は 0 を指定します。
   8. **[Usage (用途)]** ボックスの一覧で、次のいずれかのオプションを選択します。
      
      * **[Utilize this node as much as possible (このノードをできるだけ利用する)]** - Jenkins は、このスレーブが利用可能である限り、あらゆるジョブをこのスレーブで実行します。
      * **[Leave this node for tied jobs only (このノードを関連付けられたジョブ専用にする)]** - Jenkins は、このノードに特別に関連付けられた 1 つのプロジェクト (ジョブ) だけをこのノードでビルドします。これにより、スレーブを特定の種類のジョブ専用にすることができます。
      
      このチュートリアルでは、**[Utilize this node as much as possible (このノードをできるだけ利用する)]**をクリックします。
      
      この時点で、フォームは次のようになります。
      
      ![一般的なテンプレートの構成][general-template-config]
6. テンプレートの **[Image Configuration (イメージの構成)]** を入力します。
   
   1. イメージ ファミリについては、2 つのオプションがあります。
      
      * **[Custom User Image (カスタム ユーザー イメージ)]** - このオプションでは、スレーブ ノードを作成するのと同じストレージ アカウント内に存在する、カスタム イメージへの URL を指定する必要があります。
      * **[Image Reference (イメージ参照)]** - このオプションでは、イメージの*発行元*、*プラン*、*SKU* を指定する必要があります。この情報は [Azure Virtual Machines Marketplace][azure-images] で見つけることができます。
        
        このチュートリアルでは、**[Image Reference (イメージ参照)]** を選択し、以下の値を使用します。
      * **[Image Publisher (イメージ発行元)]**: Canonical
      * **[Image Offer (イメージ プラン)]**: UbuntuServer
      * **[Image Sku (イメージ SKU)]**: 14.04.4-LTS
   2. **[Launch Method (起動方法)]** の一覧には、**[SSH]** と **[JNLP]** の 2 つのオプションがあります。 このチュートリアルでは、**[SSH]** を選択します。 ただし、起動方法を選択する際には、いくつかの注意事項を考慮する必要があります。
      
      * Linux スレーブは SSH からのみ起動できます。
      * Windows スレーブには SSH と JNLP のどちらも使用できますが、Windows スレーブに SSH を使用する場合、イメージは SSH サーバーで準備されたカスタム イメージである必要があります。
        
        起動方法として JNLP を使用する場合は、次の項目が構成されていることを確認する必要があります。
      * Azure スレーブは Jenkins の URL にアクセスできる必要があるため、それに応じてファイアウォールを構成する必要があります。 Jenkins の URL を調べるには、**[Jenkins の管理]**、**[システムの設定]** の順にクリックし、**[Jenkins の位置]** セクションを探します。
   3. JNLP を使用して起動される Azure スレーブは Jenkins の TCP ポートにアクセスできる必要があるため、それに応じてファイアウォールを構成できるように固定の TCP ポートを使用することをお勧めします。 Jenkins の TCP ポートを指定するには、**[Jenkins の管理]**、**[グローバル セキュリティの設定]** の順にクリックし、**[セキュリティを有効化]** チェック ボックスをオンにして **[固定]** ポートを選択し、使用するポートを指定します。
   4. **[Init Script (初期化スクリプト)]** では、仮想マシンの作成後に実行される初期化スクリプトを入力します。これに関しては以下の考慮事項があります。
      
      * スクリプトによって、少なくとも Java がインストールされる必要があります。
      * JNLP を使用する場合は、PowerShell で初期化スクリプトを記述する必要があります。
      * 初期化スクリプトの実行に時間がかかることが予想される場合 (たとえば、大規模な一連のパッケージをインストールする場合など) は、初期化スクリプトによるインストールの代わりに、すべての必要なソフトウェアを事前インストールしたカスタム イメージを準備することをお勧めします。
        
        このチュートリアルでは、次のスクリプトをコピーして **[Init Script (初期化スクリプト)]** ボックスに貼り付けます。
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        この例では、この初期化スクリプトによって *Java*、*Git*、および *ant* がインストールされます。
   5. **[ユーザー名]** ボックスと **[パスワード]** ボックスに、仮想マシンに作成される管理者アカウント用の優先値を入力します。
      
      この時点で、フォームは次のようになります。
      
      ![Jenkins の [Image Configuration (イメージの構成)]][jenkins-image-configuration]
7. **[Verify Template (テンプレートの確認)]** をクリックして、指定したパラメーターが有効であることを確認し、**[保存]** をクリックします。
   
    ![Jenkins のテンプレートの保存][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Azure のスレーブ ノードで実行される Jenkins ジョブを作成する
このセクションでは、Azure のスレーブ ノードで実行される Jenkins タスクを作成します。 これらの手順を完了するには、GitHub でプロジェクトをセットアップしておく必要があります。

1. Jenkins ダッシュボードで、 **[New Item]**をクリックします。
   
    ![Jenkins ダッシュボードの [新規ジョブ作成]][jenkins-dashboard-new-item]
2. 作成するタスクの名前を入力し、プロジェクトのタイプとして **[フリースタイル・プロジェクトのビルド]** をクリックし、**[OK]** をクリックします。
   
    ![Jenkins の新規ジョブ作成][jenkins-create-new-item]
3. タスクの構成ページで、**[実行するノードを制限]** チェック ボックスをオンにして、**[ラベル式]** ボックスに「**linux**」と入力します。この値は、前のセクションで作成したスレーブ テンプレートのラベルと同じです。
   
    ![Jenkins の [新規ジョブ作成] の制限][jenkins-new-item-restrict]
4. **[ビルド]** セクションで、**[ビルド手順の追加]** をクリックし、**[シェルの実行]** を選択します。
   
    ![Jenkins の [新規ジョブ作成] の [ビルド]][jenkins-new-item-build]
5. 次のスクリプトを編集します。**(your GitHub account name)**、**(your project name)**、**(your project directory)** を適切な値に置き換え、編集後のスクリプトを次に表示されるテキスト領域に貼り付けます。
   
            # Clone from git repo
            currentDir="$PWD"
            if [ -e (your project directory) ]; then
                cd (your project directory)
                git pull origin master
            else
                git clone https://github.com/(your GitHub account name)/(your project name).git
            fi
            # change directory to project
            cd $currentDir/(your project directory)
            #Execute build task
            ant
   
    フォームは次の例のようになります。
   
    ![Jenkins の [新規ジョブ作成] のスクリプト][jenkins-new-item-script]
6. すべての構成手順を完了したら、**[保存]** をクリックします。
   
    ![Jenkins の [新規ジョブ作成] の [保存]][jenkins-new-item-save]
7. Jenkins ダッシュボードで、作成したばかりのプロジェクトの横にあるドロップダウン矢印をクリックし、**[Build now (今すぐビルド)]** をクリックします。
   
    ![Jenkins の [Build now (今すぐビルド)]][jenkins-build-now]

Jenkins は、前のセクションで作成したテンプレートを使用してスレーブ ノードを作成し、このタスク用のビルド手順で指定したスクリプトを実行します。

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>イメージ テンプレートを使用する際の考慮事項
次のセクションには、さまざまなテンプレートを構成する際に役立つ情報が含まれています。

### <a name="when-you-are-using-ubuntu-image-templates"></a>Ubuntu イメージ テンプレートを使用する場合
* 初期化スクリプトの実行に時間がかかることが予想される場合 (たとえば、大規模な一連のパッケージをインストールする場合など) は、初期化スクリプトによるインストールの代わりに、すべての必要なソフトウェアを事前にインストールしたカスタム Ubuntu イメージを準備することをお勧めします。
* スクリプトによって、少なくとも Java がインストールされる必要があります。 前の例で示したように、次のスクリプトを使用して Java、Git、および ant をインストールできます。
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Windows イメージ テンプレートと JNLP 起動方法を使用する場合
* Jenkins マスターでセキュリティが構成されていない場合
  
  * **[Init Script (初期化スクリプト)]** フィールドを空白のままにして、既定のスクリプトがスレーブで実行されるようにします。
* Jenkins マスターでセキュリティが構成されている場合 
  
  * 「[Windows Slaves Setup][windows-slaves-setup]」(Window スレーブのセットアップ) からスクリプトをコピーし、自分の Jenkins 資格情報で修正します。
  * 少なくとも、スクリプトの Jenkins ユーザー ID と API トークンを修正する必要があります。 ユーザーの API トークンを取得するには、次の手順を使用します。
    
    1. Jenkins ダッシュボードで、**[開発者]** をクリックします。
    2. 適切なユーザー アカウントをクリックします。
    3. 左側のメニューで **[設定]** をクリックします。
    4. **[API トークンの表示]** をクリックします。

<a name="see-also"></a>

## <a name="see-also"></a>関連項目
Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]を参照してください。

Jenkins 用 Azure スレーブ プラグインの詳細については、GitHub の [Azure スレーブ プラグイン] プロジェクトを参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure スレーブ プラグイン]: https://github.com/jenkinsci/azure-slave-plugin/
[サブスクリプション プロファイル]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


