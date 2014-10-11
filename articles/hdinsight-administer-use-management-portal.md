<properties linkid="manage-services-hdinsight-howto-administer-hdinsight" urlDisplayName="Administration" pageTitle="Manage Hadoop clusters in HDInsight using Azure Portal | Azure" metaKeywords="" description="Learn how to administer HDInsight Service. Create an HDInsight cluster, open the interactive JavaScript console, and open the Hadoop command console." metaCanonical="" services="hdinsight" documentationCenter="" title="Manage Hadoop clusters in HDInsight using the Azure Management Portal" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理

Azure の管理ポータルを使用すると、HDInsight で Hadoop クラスターをプロビジョニングし、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるように RDP を有効にすることができます管理ポータル以外にも、HDInsight を管理するツールが用意されています。

-   Azure PowerShell を使用して HDInsight を管理する方法の詳細については、「[PowerShell を使用した HDInsight の管理][]」を参照してください。

-   クロスプラットフォーム コマンド ライン ツールを使用した HDInsight の管理の詳細については、「[クロス プラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理][]」を参照してください。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

-   **Azure サブスクリプション**。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。

## この記事の内容

-   [HDInsight クラスターのプロビジョニング][]
-   [HDInsight クラスターのカスタマイズ][]
-   [HDInsight クラスターのユーザー名とパスワードの変更][]
-   [RDP を使用した HDInsight クラスターへの接続][]
-   [HTTP サービスのアクセス許可の付与/取り消し][]
-   [Hadoop コマンド コンソールを開く][]
-   [次のステップ][]

## <span id="create"></span></a> HDInsight クラスターのプロビジョニング

HDInsight クラスターを作成する方法はいくつかあります。この記事では、Azure 管理ポータルの [簡易作成] オプションを使用する方法だけを説明します。その他のオプションについては、「[HDInsight クラスターのプロビジョニング][1]」を参照してください。

HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。Azure BLOB ストレージと HDInsight クラスターのシームレスな統合の詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

Azure ストレージ アカウントは、HDInsight クラスターをプロビジョニングする予定のデータ センターと同じ場所に作成する必要があります。現在、HDInsight クラスターをプロビジョニングできるのは次の 5 つのデータ センターです。

-   東南アジア
-   北ヨーロッパ
-   西ヨーロッパ
-   米国東部
-   米国西部

Azure ストレージ アカウントの作成の詳細については、「[ストレージ アカウントの作成方法][]」を参照してください。

**HDInsight クラスターをプロビジョニングするには**

1.  [Azure 管理ポータル][]にサインインします。
2.  ページ下部の **[新規]** をクリックし、**[データ サービス]**、**[HDINSIGHT]**、**[簡易作成]** の順にクリックします。

3.  **クラスター名**、**クラスターのサイズ**、**クラスター管理用パスワード**、Azure の**ストレージ アカウント**を入力し、**[HDInsight クラスターの作成]** をクリックします。クラスターを作成して実行すると、状態が *"実行中"* になります。

    ![HDI.QuickCreate][]

    [簡易作成] オプションを使用してクラスターを作成すると、Hadoop ユーザー アカウントの既定のユーザー名は *admin* になります。アカウントに別のユーザー名を付けるには、[簡易作成] ではなく [カスタム作成] を使用します。または、プロビジョニングした後でアカウント名を変更します。

    [簡易作成] オプションを使用してクラスターを作成すると、指定したストレージ アカウントに、HDInsight クラスターと同じ名前の新しいコンテナーが自動的に作成されます。クラスターが既定で使用するコンテナーの名前を変更する場合は、[カスタム作成] オプションを使用する必要があります。

    > [WACOM.NOTE] HDInsight クラスター用の Azure Storage アカウントを選択した後で、ストレージ アカウントを変更する唯一の方法は、クラスターを削除して、希望するストレージ アカウントを使って新しいクラスターを作成することです。

4.  新しく作成したクラスターをクリックします。次のようなランディング ページが表示されます。

    ![HDI.ClusterLanding][]

## <span id="customize"></span></a> HDInsight クラスターのカスタマイズ

HDInsight は、広範囲の Hadoop コンポーネントで動作します。検証およびサポートされているコンポーネントの一覧については、「[Azure HDInsight でサポートされている Hadoop のバージョン][]」を参照してください。次のいずれかのオプションを使用して、HDInsight のカスタマイズを行うことができます。

-   クラスターのプロビジョニングの間は、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。そうすることで、これらの構成変更はクラスターの有効期間中は保持され、Azure プラットフォームが定期的に保守を実行するクラスター ノードのイメージ再適用の影響を受けません。クラスター カスタマイズ パラメーターの使用法の詳細については、「[HDInsight クラスターのプロビジョニング][1]」を参照してください。
-   Mahout や Cascading などの一部のネイティブな Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。これらの JAR ファイルは、Azure BLOB ストレージ (WASB) に分散させ、Hadoop ジョブ送信メカニズムを使用して HDInsight クラスターに送信できます。詳細については、「[プログラムによる Hadoop ジョブの送信][]」を参照してください。

    > [WACOM.NOTE] HDInsight クラスターへの jar ファイルのデプロイに関する問題、または HDInsight クラスターでの jar ファイルの呼び出しに関する問題がある場合は、[Microsoft サポート][]にお問い合わせください。

    > Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。サポートされているコンポーネントの一覧については、「[HDInsight によって提供されるクラスター バージョンの新機能][Azure HDInsight でサポートされている Hadoop のバージョン]」を参照してください。

リモート デスクトップ接続を使用したクラスターでのカスタム ソフトウェアのインストールはサポートされていません。クラスターを再作成する必要がある場合は、ヘッド ノードのドライブは失われるため、これらのドライブにファイルを保存しないでください。Azure BLOB ストレージにファイルを保存することをお勧めします。BLOB ストレージは永続的です。

## <span id="password"></span></a> HDInsight クラスターのユーザー名とパスワードの変更

HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。HDInsight クラスターのユーザー アカウントは、プロビジョニング処理中に作成されます。RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。「[リモート デスクトップを有効にする][]」を参照してください。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側のウィンドウで **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3.  ユーザー名とパスワードをリセットする HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  **[HADOOP サービス]** の横の **[オフ]** をクリックします。
6.  ページの下部にある **[保存]** をクリックし、無効になるのを待ちます。
7.  サービスが無効になった後で、**[HADOOP サービス]** の横にある **[オン]** をクリックします。
8.  **ユーザー名**と**新しいパスワード**を入力します。これらが、クラスターの新しいユーザー名とパスワードになります。
9.  **[保存]** をクリックします。

## <span id="rdp"></span></a> RDP を使用した HDInsight クラスターへの接続

クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自身にアクセスすることはできません。リモート デスクトップ アクセスは既定でオフに設定されているため、リモート デスクトップを使用してクラスターに直接アクセスするには、クラスターの作成後に追加の構成をする必要があります。

**リモート デスクトップを有効にするには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側のウィンドウで **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3.  接続する HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  ページの下部にある **[リモートを有効にする]** をクリックします。
6.  **リモート デスクトップの構成**ウィザードで、リモート デスクトップのユーザー名とパスワードを入力します。クラスターの作成時に使用したユーザー名 ([簡易作成] オプションの場合、既定で *admin*) とは異なるユーザー名を指定する必要があります。**[有効期限]** ボックスに有効期限の日付を入力します。有効期限としては、現在から 1 週間以内の将来の日付を指定する必要があります。有効期限の時刻は、既定で、指定した日付の真夜中と想定されます。チェック マーク アイコンをクリックします。

    ![HDI.CreateRDPUser][]

    有効期限は、今日から 7 日以内の将来の日付である必要があります。時刻は、選択した日付の真夜中です。

> [WACOM.NOTE] クラスターの RDP が有効になった後で、クラスターに接続する前にページを更新する必要があります。

**RDP を使用してクラスターに接続するには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側のウィンドウで **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3.  接続する HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  **[接続]** クリックし、指示に従います。

## <span id="httpservice"></span></a> HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

既定では、これらのサービスへのアクセス許可が付与されます。管理ポータルからアクセス許可を取り消す/付与することができます。

> [WACOM.NOTE] アクセス許可を付与する/取り消すことで、クラスター ユーザーのユーザー名とパスワードがリセットされます。

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側のウィンドウで **[HDINSIGHT]** をクリックします。展開した HDInsight クラスターが一覧表示されます。
3.  構成する HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  **[HADOOP サービス]** の横の **[オン]** または **[オフ]** をクリックします。
6.  **ユーザー名**と**新しいパスワード**を入力します。これらが、クラスターの新しいユーザー名とパスワードになります。
7.  **[保存]** をクリックします。

この処理は、Azure PowerShell コマンドレットを使用して行うこともできます。

-   Grant-AzureHDInsightHttpServicesAccess
-   Revoke-AzureHDInsightHttpServicesAccess

「[PowerShell を使用した HDInsight の管理][]」を参照してください。

## <span id="hadoopcmd"></span></a> Hadoop コマンド ラインを開く

リモート デスクトップを使用してクラスターに接続して Hadoop コマンド ラインを使用するには、まず、先のセクションで説明したように、クラスターに対するリモート デスクトップ アクセスを有効にする必要があります。

**Hadoop コマンド ラインを開くには**

1.  [Azure 管理ポータル][]にサインインします。
2.  左側のウィンドウで **[HDINSIGHT]** をクリックします。デプロイした Hadoop クラスターが一覧表示されます。
3.  接続する HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  ページの下部にある **[接続]** をクリックします。
6.  **[開く]** をクリックします。
7.  ユーザー名とパスワードを入力して **[OK]** をクリックします。クラスターの作成時に構成したユーザー名とパスワードを使用してください。
8.  **[はい]** をクリックします。
9.  デスクトップで、**[Hadoop コマンド ライン]** をダブルクリックします。

    ![HDI.HadoopCommandLine][]

    Hadoop コマンドの詳細については、[Hadoop コマンド リファレンス][]を参照してください。

先のスクリーンショットで、フォルダー名には Hadoop のバージョン番号が埋め込まれています。バージョン番号は、クラスターにインストールされている Hadoop コンポーネントのバージョンに基づいて変更できます。Hadoop 環境変数を使用して、これらのフォルダーを参照できます。次に例を示します。

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%   
    cd %hcatalog_home%

## <span id="nextsteps"></span></a>次のステップ

この記事では、Azure 管理ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。詳細については、次の記事を参照してください。

-   [PowerShell を使用した HDInsight の管理][]
-   [クロスプラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理][クロス プラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理]
-   [HDInsight クラスターのプロビジョニング][1]
-   [プログラムによる Hadoop ジョブの送信][]
-   [Azure HDInsight の概要][]
-   [Azure HDInsight でサポートされている Hadoop のバージョン][]

  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [クロス プラットフォーム コマンド ライン インターフェイスを使用した HDInsight の管理]: ../hdinsight-administer-use-command-line/
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [HDInsight クラスターのプロビジョニング]: #create
  [HDInsight クラスターのカスタマイズ]: #customize
  [HDInsight クラスターのユーザー名とパスワードの変更]: #password
  [RDP を使用した HDInsight クラスターへの接続]: #rdp
  [HTTP サービスのアクセス許可の付与/取り消し]: #httpservice
  [Hadoop コマンド コンソールを開く]: #hadoopcmd
  [次のステップ]: #nextsteps
  [1]: ../hdinsight-provision-clusters/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [ストレージ アカウントの作成方法]: ../storage-create-storage-account/
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [HDI.QuickCreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
  [HDI.ClusterLanding]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "クラスター ランディング ページ"
  [Azure HDInsight でサポートされている Hadoop のバージョン]: ../hdinsight-component-versioning/
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Microsoft サポート]: http://azure.microsoft.com/en-us/support/options/
  [リモート デスクトップを有効にする]: #enablerdp
  [HDI.CreateRDPUser]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
  [HDI.HadoopCommandLine]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop コマンド ライン"
  [Hadoop コマンド リファレンス]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html
  [Azure HDInsight の概要]: ../hdinsight-get-started/
