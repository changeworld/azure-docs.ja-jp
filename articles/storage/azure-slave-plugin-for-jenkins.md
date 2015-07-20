<properties
    pageTitle="Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法"
    description="Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法について説明します。"
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

#Azure スレーブ プラグインを Jenkins 継続的インテグレーションで使用する方法

Jenkins 用の Azure スレーブ プラグインを使用して、分散されたビルドを実行するときにスレーブ ノードを Azure にプロビジョニングできます。

## Azure スレーブ プラグインをインストールする
1. Jenkins ダッシュボードで、**[Manage Jenkins]** をクリックします。
2. **[Manage Jenkins]** ページで **[Manage Plugins]** をクリックします。
3. **[Available]** タブをクリックします。
4. 使用可能なプラグインの一覧の上の [フィルター] フィールドに「**Azure**」と入力して、一覧を関連するプラグインに制限します。

	使用可能なプラグインの一覧をスクロールする場合、Azure スレーブ プラグインは **[Cluster Management and Distributed Build]** セクションで見つかります。
	 
5. **[Azure Slave Plugin]** チェックボックスをオンにします。
6. **[Install without restart]** または **[Download now and install after restart]** をクリックします。

これでプラグインがインストールされたため、次の手順は Azure サブスクリプション プロファイルを使用してプラグインを構成し、スレーブ ノード用の VM の作成で使用されるテンプレートを作成することになります。


## Azure スレーブ プラグインをサブスクリプション プロファイルを使用して構成する

サブスクリプション プロファイル (発行設定) は、セキュリティで保護された資格情報と、開発環境で Azure を操作するために必要な追加情報を含む XML ファイルです。Azure スレーブ プラグインを構成するには、以下が必要です。

* サブスクリプション ID
* サブスクリプション用の管理証明書

これらは、サブスクリプション プロファイルで確認できます。サブスクリプション プロファイルのコピーがない場合は、[ここ](https://manage.windowsazure.com/publishsettings/Index?SchemaVersion=2.0)からダウンロードできます。サブスクリプション プロファイルの例を以下に示します。

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
2. **[Configure System]** をクリックします。
3. ページを下にスクロールして **[Cloud]** セクションを探します。
4. **[Add new cloud]、[Microsoft Azure]** の順にクリックします。

	![クラウド セクション](./media/azure-slave-plugin-for-jenkins/jenkins-cloud-section.png)

	サブスクリプションの詳細を入力する必要のあるフィールドが表示されます。

	![サブスクリプションの構成](./media/azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png)

5. サブスクリプション プロファイルからサブスクリプションID と管理証明書の値をコピーし、適切なフィールドに貼り付けます。

	サブスクリプション ID と管理証明書をコピーするときは、値を囲む引用符を含めないでください。
	
6. **[Verify configuration]** をクリックします。
7. 構成が正しいことが確認したら、**[Save]** をクリックします。

## Azure スレーブ プラグイン用の仮想マシン テンプレートを設定する

仮想マシン テンプレートは、プラグインが Azure でスレーブ ノードを作成するために使用するパラメーターを定義します。次の手順では、Ubuntu VM 用のテンプレートを作成します。

1. Jenkins ダッシュボードで、**[Manage Jenkins]** をクリックします。
2. **[Configure System]** をクリックします。
3. ページを下にスクロールして **[Cloud]** セクションを探します。

4. [Cloud] セクションで、[Add Azure Virtual Machine Template] を探し、[Add] ボタンをクリックします。

	![VM テンプレートの追加](./media/azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png)

	作成するテンプレートの詳細を入力するフィールドが表示されます。

	![空白の一般的な構成](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png)

5. **[Name]** フィールドに Azure クラウド サービスの名前を入力します。入力した名前が既存のクラウド サービスを指す場合、VM はそのサービス内にプロビジョニングされます。それ以外の場合、Azure は新しいものを作成します。

6. **[Description]** フィールドに、作成するテンプレートを説明するテキストを入力します。これは参考目的のみで使用され、VM のプロビジョニングでは使用されません。
7. **[Labels]** フィールドは作成中のテンプレートを識別するために使用され、以降は Jenkins ジョブを作成するときにテンプレートを参照するために使用されます。この手順では、このフィールドには「**linux**」と入力します。 
8. VM が作成されるリージョンを選択します。
9. 適切な VM サイズを選択します。
10. VM が作成されるストレージ アカウントを指定します。使用するクラウド サービスと同じリージョン内にあることを確認します。新しいストレージを作成する場合は、このフィールドを空白のままにすることができます。
11. [Retention time] は、その時間を経過するとJenkins がアイドル状態のスレーブを削除する分数を指定します。これは、既定値 60 のままにします。スレーブは、アイドル状態になったときに削除するのではなくシャットダウンすることもできます。これを行うには、**[Shutdown Only (Do Not Delete) After Retention Time]** チェックボックスをオンにします。
12. **[Usage]** で、このスレーブノードが使用される適切な条件を選択します。ここでは、**[Utilize this node as much as possible]** を選択します。

	この時点で、フォームは次のようになります。

	![一般的なテンプレート構成のチェックポイント](./media/azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png)

	次の手順は、スレーブが作成されるオペレーティング イメージの詳細を指定することです。
 
13. **[Image Family or Id]** には、VM にインストールされるシステム イメージを指定する必要があります。イメージ ファミリの一覧から選択するか、カスタム イメージを指定できます。

	イメージ ファミリの一覧から選択する場合は、イメージ ファミリ名の最初の文字を (大文字と小文字を区別して) 入力します。たとえば、「**U**」と入力すると、Ubuntu Server ファミリの一覧が表示されます。一覧から選択すると、Jenkins は VM をプロビジョニングするときにそのファミリの最新バージョンのシステム イメージを使用します。

	![OS イメージの一覧の例](./media/azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png)
	
	代わりに使用するカスタム イメージがある場合は、そのカスタム イメージの名前を入力します。カスタム イメージの名前は一覧には表示されないため、名前が正しく入力されていることを確認する必要があります。

	このチュートリアルでは、「**U**」と入力して Ubuntu イメージの一覧を表示し、**[Ubuntu Server 14.04 LTS]** を選択します。
 
14. **[Launch method]** で、**[SSH]** を選択します。
15. 次のスクリプトをコピーして **[Init script]** フィールドに貼り付けます。

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
	
16. **[Username]** フィールドと **[Password]** フィールドに、VM に作成される管理者アカウント用の優先値を入力します。
17. **[Verify Template]** をクリックして、指定したパラメーターが有効であることを確認します。
18. **[Save]** をクリックします。


## Azure のスレーブ ノードで実行される Jenkins ジョブを作成する

このセクションでは、Azure のスレーブ ノードで実行される Jenkins タスクを作成します。このためには、プロジェクトを github で起動させる必要があります。

1. Jenkins ダッシュボードで、**[New Item]** をクリックします。 
2. 作成するタスクの名前を入力します。
3. プロジェクトの種類として、**[Freestyle project]** を選択します。
4. **[OK]** をクリックします。
5. タスクを構成するページで、**[Restrict where this project can be run]** を選択します。
6. **[Label Expression]** フィールドに、「**linux**」と入力します。前のセクションで、**linux** という名前のテンプレートを作成しました。ここで指定するのはそのテンプレートです。
7. **[Build]** セクションで、**[Add build step]** をクリックし、**[Execute shell]** を選択します。
8. 次のスクリプトを編集します。**(your github account name)**、**(your project name)**、および **(your project directory)** を適切な値に置き換え、編集後のスクリプトを次に表示されるテキスト領域に貼り付けます。

		# Clone from git repo

		currentDir="$PWD"

		if [ -e (your project directory) ]; then

  			cd (your project directory)

  			git pull origin master

		else

  			git clone https://github.com/(your github account name)/(your project name).git

		fi
		
		# change directory to project

		cd $currentDir/(your project directory)



		#Execute build task

		ant
		
9. **[Save]** をクリックします。
10. Jenkins ダッシュ ボードで、作成したばかりのタスクの上にマウスを移動し、矢印をクリックしてタスク オプションを表示します。
11. **[Build now]** をクリックします。

Jenkins は、前のセクションで作成したテンプレートを使用してスレーブ ノードを作成し、このタスク用のビルド手順に指定されたスクリプトを実行します。






  

  

<!---HONumber=July15_HO2-->