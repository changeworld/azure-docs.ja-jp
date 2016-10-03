<properties
	pageTitle="Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法 | Microsoft Azure"
	description="Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="robmcm"/>

# Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法

Jenkins 用の Azure スレーブ プラグインを使用して、分散されたビルドを実行するときにスレーブ ノードを Azure にプロビジョニングできます。

## Azure スレーブ プラグインをインストールする

1. Jenkins ダッシュボードで、**[Manage Jenkins]** をクリックします。

1. **[Manage Jenkins]** ページで **[Manage Plugins]** をクリックします。

1. **[Available]** タブをクリックします。

1. 使用可能なプラグインの一覧の上にあるフィルター フィールドに「**Azure**」と入力して、一覧を関連するプラグインに制限します。

    使用可能なプラグインの一覧をスクロールする場合、Azure スレーブ プラグインは **[Cluster Management and Distributed Build]** セクションで見つかります。

1. **[Azure Slave Plugin]** チェック ボックスをオンにします。

1. **[Install without restart]** または **[Download now and install after restart]** をクリックします。

これでプラグインがインストールされたため、次の手順では、Azure サブスクリプション プロファイルを使用してプラグインを構成し、スレーブ ノード用の仮想マシンの作成で使用するテンプレートを作成します。


## サブスクリプション プロファイルを使用して Azure スレーブ プラグインを構成する

サブスクリプション プロファイル (発行設定) は、セキュリティで保護された資格情報と、開発環境で Azure を操作するために必要な追加情報を含む XML ファイルです。Azure スレーブ プラグインを構成するには、以下が必要です。

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

1. Jenkins ダッシュボードで、**[Manage Jenkins]** をクリックします。

1. **[Configure System]** をクリックします。

1. ページを下にスクロールして **[Cloud]** セクションを探します。

1. **[Add new cloud]、[Microsoft Azure]** の順にクリックします。

    ![クラウド セクション][cloud section]

    サブスクリプションの詳細を入力する必要のあるフィールドが表示されます。

    ![サブスクリプションの構成][subscription configuration]

1. サブスクリプション プロファイルからサブスクリプションID と管理証明書の値をコピーし、適切なフィールドに貼り付けます。

    サブスクリプション ID と管理証明書をコピーするときは、値を囲む引用符は含めないでください。

1. **[Verify Configuration]** をクリックします。

1. 構成が正しいことが確認したら、**[Save]** をクリックします。

## Azure スレーブ プラグイン用の仮想マシン テンプレートを設定する

仮想マシン テンプレートでは、プラグインが Azure でスレーブ ノードを作成するために使用するパラメーターを定義します。次の手順では、Ubuntu 仮想マシン用のテンプレートを作成します。

1. Jenkins ダッシュボードで、**[Manage Jenkins]** をクリックします。

1. **[Configure System]** をクリックします。

1. ページを下にスクロールして **[Cloud]** セクションを探します。

1. **[Cloud]** セクションで、**[Add Azure Virtual Machine Template]** を探し、**[Add]** をクリックします。

    ![VM テンプレートの追加][add vm template]

    作成するテンプレートの詳細を入力するフィールドが表示されます。

    ![空白の一般的な構成][blank general configuration]

1. **[Name]** ボックスに、Azure クラウド サービスの名前を入力します。入力した名前が既存のクラウド サービスを指す場合、仮想マシンはそのサービス内にプロビジョニングされます。それ以外の場合、Azure は新しいものを作成します。

1. **[Description]** ボックスに、作成するテンプレートを説明するテキストを入力します。これは記録のみを目的としており、仮想マシンのプロビジョニングでは使用されません。

1. **[Labels]** ボックスは作成中のテンプレートを識別するために使用され、その後、Jenkins ジョブを作成するときにテンプレートを参照するために使用されます。この手順では、このボックスに「**linux**」と入力します。

1. **[Region]** ボックスの一覧で、仮想マシンを作成するリージョンをクリックします。

1. **[Virtual Machine Size]** ボックスの一覧で、適切なサイズをクリックします。

1. **[Storage Account Name]** ボックスに、仮想マシンを作成するストレージ アカウントを指定します。使用するクラウド サービスと同じリージョン内にあることを確認します。新しいストレージを作成する場合は、このボックスを空白のままにすることができます。

1. [Retention time] は、その時間を経過するとJenkins がアイドル状態のスレーブを削除する分数を指定します。これは、既定値 60 のままにします。スレーブは、アイドル状態になったときに削除するのではなくシャットダウンすることもできます。そのためには、**[Shutdown Only (Do Not Delete) After Retention Time]** チェック ボックスをオンにします。

1. **[Usage]** ボックスの一覧で、このスレーブ ノードが使用される適切な条件をクリックします。ここでは、**[Utilize this node as much as possible]** をクリックします。

    この時点で、フォームは次のようになります。

    ![一般的なテンプレート構成のチェックポイント][checkpoint general template config]

    次の手順では、スレーブが作成されるオペレーティング システム イメージの詳細を指定します。

1. **[Image Family or Id]** ボックスには、仮想マシンにインストールされるシステム イメージを指定する必要があります。イメージ ファミリの一覧から選択するか、カスタム イメージを指定できます。

    イメージ ファミリの一覧から選択する場合は、イメージ ファミリ名の最初の文字を (大文字と小文字を区別して) 入力します。たとえば、「**U**」と入力すると、Ubuntu Server ファミリの一覧が表示されます。一覧から選択すると、Jenkins は仮想マシンをプロビジョニングするときにそのファミリの最新バージョンのシステム イメージを使用します。

    ![OS イメージの一覧の例][OS Image list sample]

    代わりに使用するカスタム イメージがある場合は、そのカスタム イメージの名前を入力します。カスタム イメージの名前は一覧には表示されないため、名前が正しく入力されていることを確認する必要があります。

    このチュートリアルでは、「**U**」と入力して Ubuntu イメージの一覧を表示し、**[Ubuntu Server 14.04 LTS]** をクリックします。

1. **[Launch Method]** ボックスの一覧で、**[SSH]** をクリックします。

1. 次のスクリプトをコピーして **[Init Script]** ボックスに貼り付けます。

		# Install Java

		sudo apt-get -y update

		sudo apt-get install -y openjdk-7-jdk

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y openjdk-7-jdk

		# Install git

		sudo apt-get install -y git

		#Install ant

		sudo apt-get install -y ant

		sudo apt-get -y update --fix-missing

		sudo apt-get install -y ant

    init スクリプトは、仮想マシンが作成された後で実行されます。この例では、このスクリプトによって、Java、Git、および ant がインストールされます。

1. **[Username]** ボックスと **[Password]** ボックスに、仮想マシンに作成される管理者アカウント用の優先値を入力します。

1. **[Verify Template]** をクリックして、指定したパラメーターが有効であることを確認します。

1. [**Save**] をクリックします。


## Azure のスレーブ ノードで実行される Jenkins ジョブを作成する

このセクションでは、Azure のスレーブ ノードで実行される Jenkins タスクを作成します。このためには、独自のプロジェクトを GitHub で起動する必要があります。

1. Jenkins ダッシュボードで、**[New Item]** をクリックします。

1. 作成するタスクの名前を入力します。

1. プロジェクトの種類として、**[Freestyle project]** をクリックします。

1. **[OK]** をクリックします。

1. タスクを構成するページで、**[Restrict where this project can be run]** を選択します。

1. **[Label Expression]** ボックスに、「**linux**」と入力します。前のセクションで、**linux** という名前のテンプレートを作成しました。ここで指定するのはそのテンプレートです。

1. **[Build]** セクションで、**[Add build step]** をクリックし、**[Execute shell]** を選択します。

1. 次のスクリプトを編集します。**(your GitHub account name)**、**(your project name)**、**(your project directory)** を適切な値に置き換え、編集後のスクリプトを次に表示されるテキスト領域に貼り付けます。

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

1. [**Save**] をクリックします。

1. Jenkins ダッシュボードで、作成したばかりのタスクの上にマウスを移動し、ドロップダウン矢印をクリックしてタスク オプションを表示します。

1. **[Build now]** をクリックします。

Jenkins は、前のセクションで作成したテンプレートを使用してスレーブ ノードを作成し、このタスク用のビルド手順で指定されたスクリプトを実行します。

## 次のステップ

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[サブスクリプション プロファイル]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png

<!---HONumber=AcomDC_0921_2016-->