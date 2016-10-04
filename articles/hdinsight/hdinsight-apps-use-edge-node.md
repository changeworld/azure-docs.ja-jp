<properties
	pageTitle="HDInsight での空のエッジ ノードの使用 | Microsoft Azure"
	description="クライアントとして使用できる空のエッジ ノードを HDInsight クラスターに追加する方法と HDInsight アプリケーションをテストおよびホストする方法について説明します。"
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>

# HDInsight での空のエッジ ノードの使用

Linux ベースの HDInsight クラスターに空のエッジ ノードを追加する方法について説明します。空のエッジ ノードは、ヘッド ノードの場合と同じクライアント ツールがインストールされ、構成された Linux 仮想マシンですが、Hadoop サービスは実行されていません。エッジ ノードは、クラスターへのアクセス、クライアント アプリケーションのテスト、およびクライアント アプリケーションのホストに使用できます。

空のエッジ ノードは、既存の HDInsight クラスターに追加することも、クラスターの作成時にその新しいクラスターに追加することもできます。空のエッジ ノードを追加するには、Azure Resource Manager テンプレートを使用します。次のサンプルでは、テンプレートを使用して空のエッジ ノードを追加する方法を示しています。

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
					"roles": ["edgenode"]
				}],
				"uninstallScriptActions": [],
				"httpsEndpoints": [],
				"applicationType": "CustomApplication"
			}
		}
	],

サンプルに示すように、必要に応じて、[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)を呼び出して、エッジ ノードへの [Apache Hue](hdinsight-hadoop-hue-linux.md) のインストールなど、追加の構成を行うことができます。

エッジ ノードを作成した後、SSH を使用してエッジ ノードに接続し、クライアント ツールを実行して HDInsight の Hadoop クラスターにアクセスすることができます。

## 既存のクラスターにエッジ ノードを追加する

このセクションでは、Resource Manager テンプレートを使用して既存の HDInsight クラスターにエッジ ノードを追加します。Resource Manager テンプレートは、[GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode) にあります。Resource Manager テンプレートは、https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh にあるスクリプト アクションのスクリプトを呼び出します。このスクリプトでは操作は実行されません。これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを説明するためのものです。

**既存のクラスターに空のエッジ ノードを追加するには**

1. HDInsight クラスターがない場合は作成します。「[Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)」を参照してください。
2. 次の画像をクリックして Azure にサインインし、Azure Portal で Azure Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 次のプロパティを構成します。

	- CLUSTERNAME: 既存の HDInsight クラスター名を入力します。
	- EDGENODESIZE: いずれかの VM サイズを選択します。
	- EDGENODEPREFIX: 既定値は **new** です。既定値を使用する場合、エッジ ノードの名前は **new-edgenode** です。プレフィックスは、Portal でカスタマイズできます。テンプレートからフル ネームをカスタマイズすることもできます。


4. **[OK]** をクリックして変更を保存します。
5. **[リソース グループ]** で、リソース グループを選択します。
6. **[法律条項を確認してください]**、**[購入]** の順にクリックします。
7. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。

## クラスター作成時にエッジ ノードを追加する

このセクションでは、Resource Manager テンプレートを使用して、エッジ ノードを含む HDInsight クラスターを作成します。Resource Manager テンプレートは、パブリックの [Azure Blob Storage コンテナー](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)にあります。Resource Manager テンプレートは、https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh にあるスクリプト アクションのスクリプトを呼び出します。このスクリプトでは操作は実行されません。これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを説明するためのものです。

**既存のクラスターに空のエッジ ノードを追加するには**

1. HDInsight クラスターがない場合は作成します。「[Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)」を参照してください。
2. 次の画像をクリックして Azure にサインインし、Azure Portal で Azure Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 次のプロパティを構成します。
		
	- CLUSTERNAME: 作成する新しいクラスターの名前を入力します。
	- CLUSTERLOGINUSERNAME: Hadoop HTTP ユーザー名を入力します。既定の名前は **admin** です。
	- CLUSTERLOGINPASSWORD: Hadoop HTTP ユーザー パスワードを入力します。
	- SSHUSERNAME: SSH ユーザー名を入力します。既定の名前は **sshuser** です。
	- SSHPASSWORD: SSH ユーザー パスワードを入力します。
	- LOCATION: リソース グループ名、クラスター、および既定のストレージ アカウントの場所を入力します。
	- CLUSTERTYPE: 既定値は **hadoop** です。
	- CLUSTERWORKERNODECOUNT: 既定値は 2 です。
	- EDGENODESIZE: いずれかの VM サイズを選択します。
	- EDGENODEPREFIX: 既定値は **new** です。既定値を使用する場合、エッジ ノードの名前は **new-edgenode** です。プレフィックスは、Portal でカスタマイズできます。テンプレートからフル ネームをカスタマイズすることもできます。

4. **[OK]** をクリックして変更を保存します。
5. **[リソース グループ]** で、新しいリソース グループ名を入力します。
6. **[法律条項を確認してください]**、**[購入]** の順にクリックします。
7. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。


## エッジ ノードにアクセスする

エッジ ノードの SSH エンドポイントは、<エッジ ノード名>.<クラスター名>-ssh.azurehdinsight.net:22 です。たとえば、new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22 のようになります。

エッジ ノードは、Azure Portal ではアプリケーションとして表示されます。Portal には、SSH を使用してエッジ ノードにアクセスするための情報が表示されます。

**エッジ ノードの SSH エンドポイントを確認するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. クラスター ブレードで、**[アプリケーション]** をクリックします。エッジ ノードが表示されます。既定の名前は **new-edgenode** です。
4. エッジ ノードをクリックします。SSH エンドポイントが表示されます。

**エッジ ノードで Hive を使用するには**

5. SSH を使用してエッジ ノードに接続します。「[Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)」または「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)」を参照してください。
6. SSH を使用してエッジ ノードに接続したら、次のコマンドを使用して Hive コンソールを開きます。

		hive
7. 次のコマンドを使用して、クラスター内の Hive テーブルを表示します。

		show tables;

## エッジ ノードを削除する

Azure Portal からエッジ ノードを削除できます。

**エッジ ノードにアクセスするには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. クラスター ブレードで、**[アプリケーション]** をクリックします。エッジ ノードの一覧が表示されます。
4. 削除するエッジ ノードを右クリックし、**[削除]** をクリックします。
5. **[はい]** をクリックして確定します。

## 次のステップ

この記事では、エッジ ノードを追加する方法とエッジ ノードにアクセスする方法について説明しました。詳細については、次の記事を参照してください。

- [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について確認します。
- [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
- [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
- [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
- [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
- [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。

<!---HONumber=AcomDC_0914_2016-->