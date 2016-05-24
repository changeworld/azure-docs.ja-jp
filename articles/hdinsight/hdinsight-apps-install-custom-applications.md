<properties
   	pageTitle="HDInsight への Hadoop アプリケーションのインストール | Microsoft Azure"
   	description="HDInsight アプリケーションに HDInsight アプリケーションをインストールする方法について説明します。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# カスタム HDInsight アプリケーションのインストール

HDInsight アプリケーションは、ユーザーが Linux ベースの HDInsight クラスターにインストールすることのできるアプリケーションです。マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。この記事では、まだ Azure ポータルに発行されていない HDInsight アプリケーションを HDInsight にインストールする方法について説明します。ここでインストールするアプリケーションは [Hue](http://gethue.com/) です。

その他の関連記事:

- [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。
- [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について説明します。

 
## 前提条件

既存の HDInsight クラスターに HDInsight アプリケーションをインストールする場合は、対象となる HDInsight クラスターが必要です。新たに作成する場合は、「[クラスターの作成](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。HDInsight クラスターを作成するときに HDInsight アプリケーションをインストールすることもできます。


## HDInsight アプリケーションのインストール

HDInsight アプリケーションは、クラスターを作成するときにインストールするか、または既存の HDInsight クラスターにインストールすることができます。ARM テンプレートの定義については、「[MSDN: Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。

このアプリケーション (Hue) をデプロイするために必要なファイルは次のとおりです。

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight のアプリケーションをインストールするための ARM テンプレート。ARM テンプレートの作成については、「[MSDN: Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): ARM テンプレートによって呼び出されるスクリプト アクション。エッジ ノードの構成に使用されます。 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install\_v0.sh から呼び出される hue のバイナリ ファイル。 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install\_v0.sh から呼び出される hue のバイナリ ファイル。 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): hui-install\_v0.sh から呼び出されるサンプル Web アプリケーション (Tomcat)。

**既存の HDInsight クラスターに Hue をインストールするには**

1. 次の画像をクリックして Azure にサインインし、Azure ポータルで ARM テンプレートを開きます。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/ja-JP/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    このボタンをクリックすると、Azure ポータルの ARM テンプレートが開きます。この ARM テンプレートは、[https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) にあります。この ARM テンプレートの作成方法については、「[MSDN: Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。
    
2. **[パラメーター]** ブレードで、次の項目を入力します。

    - **ClusterName**: アプリケーションのインストール先となるクラスターの名前を入力します。このクラスターは既に存在していることが必要です。
    
3. **[OK]** をクリックしてパラメーターを保存します。
4. **[カスタム デプロイ]** ブレードから「**リソース グループ**」と入力します。リソース グループとは、クラスターや依存するストレージ アカウントなどのリソースをひとまとめにしたコンテナーです。クラスターと同じリソース グループを使用する必要があります。
5. **[法律条項]** をクリックし、**[作成]** をクリックします。
6. **[ダッシュボードにピン留めする]** チェック ボックスがオンになっていることを確認して、**[作成]** をクリックします。インストールのステータスは、ポータル ダッシュボードにピン留めされたタイルまたはポータルの通知 (ポータル上部のベル アイコンをクリック) で確認できます。アプリケーションのインストールには 10 分程度かかります。

**クラスターの作成時に Hue をインストールするには**

1. 次の画像をクリックして Azure にサインインし、Azure ポータルで ARM テンプレートを開きます。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/ja-JP/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    このボタンをクリックすると、Azure ポータルの ARM テンプレートが開きます。この ARM テンプレートは [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) にあります。この ARM テンプレートの作成方法については、「[MSDN: Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。

2. 手順に従ってクラスターを作成し、Hue をインストールします。HDInsight クラスターの作成の詳細については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## インストールを検証する

Azure ポータルでアプリケーションのステータスを確認し、アプリケーションが正しくインストールされていることを確認できます。さらに、すべての HTTP エンドポイントが正しく設定されていること、また Web ページが存在する場合は正しく表示されることを確認することもできます。

**Hue ポータルを開くには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックします。表示されない場合は、**[参照]** をクリックし、**[HDInsight クラスター]** をクリックしてください。
3. アプリケーションをインストールしたクラスターをクリックします。
4. **[設定]** ブレードで **[全般]** カテゴリの **[アプリケーション]** をクリックします。**[インストール済みアプリ]** ブレードの一覧に **hue** が表示されます。
5. 一覧の **[hue]** をクリックしてプロパティを一覧表示します。  
6. Web ページ リンクをクリックして Web サイトを検証します。具体的には、ブラウザーで HTTP エンドポイントを開いて Hue の Web UI が正しく表示されることを確認し、[PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) などの [SSH クライアント](hdinsight-hadoop-linux-use-ssh-unix.md)を使用して SSH エンドポイントを開きます。
 
## インストールのトラブルシューティング

HDInsight アプリケーションのインストールの[トラブルシューティング](hdinsight-apps-install-applications.md#troubleshoot)に関するセクションを参照してください。


## 次のステップ

- [MSDN 記事を参照して HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx)。HDInsight アプリケーションをデプロイするための ARM テンプレートの作成方法を確認します。
- [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。
- [Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)。スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
- [ARM テンプレートを使用して Linux ベースの Hadoop クラスターを HDInsight に作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。ARM テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。

<!---HONumber=AcomDC_0518_2016-->