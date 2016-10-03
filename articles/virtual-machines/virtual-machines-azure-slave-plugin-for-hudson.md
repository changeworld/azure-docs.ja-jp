<properties
	pageTitle="Azure スレーブ プラグインを Hudson 継続的インテグレーションで使用する方法 | Microsoft Azure"
	description="Azure スレーブ プラグインを Hudson 継続的インテグレーションで使用する方法について説明します。"
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

# Azure スレーブ プラグインを Hudson 継続的インテグレーションで使用する方法

Hudson 用 Azure スレーブ プラグインを使用して、分散されたビルドを実行するときにスレーブ ノードを Azure にプロビジョニングできます。

## Azure スレーブ プラグインをインストールする

1. Hudson ダッシュボードで、**[Manage Hudson]** をクリックします。

1. **[Manage Hudson]** ページで **[Manage Plugins]** をクリックします。

1. **[Available]** タブをクリックします。

1. **[Search]** をクリックし、「**Azure**」と入力して、一覧を関連するプラグインに制限します。

	使用可能なプラグインの一覧をスクロールする場合、Azure スレーブ プラグインは **[Others]** タブの **[Cluster Management and Distributed Build]** セクションにあります。

1. **[Azure Slave Plugin]** チェックボックスをオンにします。

1. **[インストール]** をクリックします。

1. Hudson を再起動します。

これでプラグインがインストールされました。次に、Azure サブスクリプション プロファイルを使用してプラグインを構成し、スレーブ ノードの VM の作成に使用するテンプレートを作成します。

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

      		Id="<Subscription ID>"

      		Name="Pay-As-You-Go"
			ManagementCertificate="<Management certificate value>" />

  		</PublishProfile>

	</PublishData>

サブスクリプション プロファイルを用意したら、次の手順に従って Azure スレーブ プラグインを構成します。

1. Hudson ダッシュボードで、**[Manage Hudson]** をクリックします。

1. **[Configure System]** をクリックします。

1. ページを下にスクロールして **[Cloud]** セクションを探します。

1. **\[Add new cloud]、[Microsoft Azure]** の順にクリックします。

    ![新しいクラウドの追加][add new cloud]

    サブスクリプションの詳細を入力する必要のあるフィールドが表示されます。

    ![プロファイルの構成][configure profile]

1. サブスクリプション プロファイルからサブスクリプションID と管理証明書をコピーし、適切なフィールドに貼り付けます。

    サブスクリプション ID と管理証明書をコピーするときは、値を囲む引用符を含めないでください。

1. **[Verify configuration]** をクリックします。

1. 構成が正しいことが確認されたら、**[Save]** をクリックします。

## Azure スレーブ プラグイン用の仮想マシン テンプレートを設定する

仮想マシン テンプレートでは、プラグインが Azure でスレーブ ノードを作成する際に使用するパラメーターを定義します。次の手順では、Ubuntu VM 用のテンプレートを作成します。

1. Hudson ダッシュボードで、**[Manage Hudson]** をクリックします。

1. **[Configure System]** をクリックします。

1. ページを下にスクロールして **[Cloud]** セクションを探します。

1. **[Cloud]** セクションで、**[Add Azure Virtual Machine Template]** を探し、**[Add]** ボタンをクリックします。

    ![VM テンプレートの追加][add vm template]

1. **[Name]** フィールドにクラウド サービスの名前を指定します。指定した名前が既存のクラウド サービスを指す場合、VM はそのサービス内にプロビジョニングされます。それ以外の場合、Azure は新しいものを作成します。

1. **[Description]** フィールドに、作成するテンプレートを説明するテキストを入力します。この情報は説明用としてのみ使用され、VM のプロビジョニングでは使用されません。

1. **[Labels]** フィールドに、「**linux**」と入力します。このラベルは作成中のテンプレートを識別するために使用され、以降は Hudson ジョブを作成するときにテンプレートを参照するために使用されます。

1. VM が作成されるリージョンを選択します。

1. 適切な VM サイズを選択します。

1. VM が作成されるストレージ アカウントを指定します。使用するクラウド サービスと同じリージョン内にあることを確認します。新しいストレージを作成する場合は、このフィールドを空白のままにすることができます。

1. [Retention time] は、その時間を経過するとHudson がアイドル状態のスレーブを削除する分数を指定します。これは、既定値 60 のままにします。

1. **[Usage]** で、このスレーブノードが使用される適切な条件を選択します。ここでは、**[Utilize this node as much as possible]** を選択します。

    この時点で、フォームは次のようになります。

    ![テンプレートの構成][template config]

1. **[Image Family or Id]** には、VM にインストールされるシステム イメージを指定する必要があります。イメージ ファミリの一覧から選択するか、カスタム イメージを指定できます。

    イメージ ファミリの一覧から選択する場合は、イメージ ファミリ名の最初の文字を (大文字と小文字を区別して) 入力します。たとえば、「**U**」と入力すると、Ubuntu Server ファミリの一覧が表示されます。一覧から選択すると、Jenkins は VM をプロビジョニングするときにそのファミリの最新バージョンのシステム イメージを使用します。

    ![OS ファミリの一覧][OS family list]

    代わりに使用するカスタム イメージがある場合は、そのカスタム イメージの名前を入力します。カスタム イメージの名前は一覧には表示されないため、名前が正しく入力されていることを確認する必要があります。

    このチュートリアルでは、「**U**」と入力して Ubuntu イメージの一覧を表示し、**[Ubuntu Server 14.04 LTS]** を選択します。

1. **[Launch method]** で、**[SSH]** を選択します。

1. 次のスクリプトをコピーして **[Init script]** フィールドに貼り付けます。

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

    **Init スクリプト**は、VM が作成された後で実行されます。この例では、スクリプトは、Java、git、および ant をインストールします。

1. **[Username]** フィールドと **[Password]** フィールドに、VM に作成される管理者アカウント用の優先値を入力します。

1. **[Verify Template]** をクリックして、指定したパラメーターが有効であることを確認します。

1. **[Save]** をクリックします。

## Azure のスレーブ ノードで実行される Hudson ジョブを作成する

このセクションでは、Azure のスレーブ ノードで実行される Hudson タスクを作成します。

1. Hudson ダッシュボードで、**[New Job]** をクリックします。

1. 作成するジョブの名前を入力します。

1. ジョブの種類として、**[Build a free-style software job]** を選択します。

1. **[OK]** をクリックします。

1. ジョブを構成するページで、**[Restrict where this project can be run]** を選択します。

1. **[Node and label menu]** を選択し、**[linux]** を選択します (このラベルは、前のセクションで仮想マシンテンプレートを作成するときに指定したものです)。

1. **[Build]** セクションで、**[Add build step]** をクリックし、**[Execute shell]** を選択します。

1. 次のスクリプトを編集します。**{your github account name}**、**{your project name}**、**{your project directory}** を適切な値に置き換え、編集後のスクリプトを次に表示されるテキスト領域に貼り付けます。

		# Clone from git repo

		currentDir="$PWD"

		if [ -e {your project directory} ]; then

  			cd {your project directory}

  			git pull origin master

		else

  			git clone https://github.com/{your github account name}/{your project name}.git

		fi

		# change directory to project

		cd $currentDir/{your project directory}

		#Execute build task

		ant

1. **[Save]** をクリックします。

1. Hudson ダッシュボードで、作成したばかりのジョフ゛を探し、**[Schedule a build]** アイコンをクリックします。

Hudson は、前のセクションで作成したテンプレートを使用してスレーブ ノードを作成し、このタスク用のビルド手順に指定されたスクリプトを実行します。

## 次のステップ

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[サブスクリプション プロファイル]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

<!---HONumber=AcomDC_0921_2016-->