<properties 
   pageTitle="HDinsight での Apache Phoenix および SQuirrel の使用 | Azure" 
   description="Apache Phoenix を HDInsight で使用する方法、およびワークステーションに SQuirrel をインストールして HDInsight の HBase クラスターに接続するように構成する方法について説明します。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/15/2015"
   ms.author="jgao"/>

# HDinsight での Apache Phoenix および SQuirrel の使用  

[Apache Phoenix](http://phoenix.apache.org/) を HDInsight で使用する方法、およびワークステーションに SQuirrel をインストールして HDInsight の HBase クラスターに接続するように構成する方法について説明します。Phoenix の詳細については、[Phoenix についての簡単な説明](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)を参照してください。

>[AZURE.NOTE]HDInsight での Phoenix のバージョンの情報については、「[What's new in the Hadoop cluster versions provided by HDInsight? (HDInsight で提供される Hadoop クラスター バージョンの新機能)][hdinsight-versions]」を参照してください。

## SQLLine の使用
[SQLLine](http://sqlline.sourceforge.net/) は、SQL を実行するためのコマンド ライン ユーティリティです。

### 前提条件
SQLLine を使用するには、以下のものが必要です。

- **HDInsight 環境の HBase クラスター**。HBase クラスターのプロビジョニングについては、「[Get started with Apache HBase in HDInsight (HDInsight での Apache HBase の使用)][hdinsight-hbase-get-started]」を参照してください。
- **リモート デスクトップ プロトコルを使用した HBase クラスターへの接続**。方法については、「[Azure の管理ポータルを使用した HDInsight での Hadoop クラスターの管理][hdinsight-manage-portal]」を参照してください。

**ホスト名を確認するには**

1. デスクトップから **Hadoop コマンド ライン**を開きます。
2. 次のコマンドを実行します。

		ipconfig

	**接続固有の DNS サフィックス**を記録します。例: *myhbasecluster.f5.internal.cloudapp.net* HBase クラスターに接続するときは、FQDN を使用して Zookeeper のいずれかに接続する必要があります。各 HDInsight クラスターには 3 つの Zookeeper があります。*zookeeper0*、*zookeeper1*、および *zookeeper2* です。FQDN は *zookeeper2.myhbasecluster.f5.internal.cloudapp.net* のようになります。

**SQLLine を使用するには**

1. デスクトップから **Hadoop コマンド ライン**を開きます。
2. 次のコマンドを実行します。

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

詳細については、[SQLLine のマニュアル](http://sqlline.sourceforge.net/#manual)を参照してください。


















## SQuirrel の使用

[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) はグラフィカルな Java プログラムであり、JDBC 準拠データベースの構造の表示、テーブル内のデータの参照、SQL コマンドの発行などに使用できます。

このセクションでは、ワークステーションに SQuirrel をインストールし、VPN 経由で HDInsight の HBase クラスターに接続するように構成する方法について説明します。

### 前提条件

手順を実行する前に、以下のものが必要です。

- DNS 仮想マシンで Azure Virtual Network にデプロイされた HBase クラスター。方法については、「[Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]」を参照してください。 

	>[AZURE.IMPORTANT]仮想ネットワークに DNS サーバーをインストールする必要があります。

- HBase クラスターの接続固有の DNS サフィックスを取得します。そのためには、RDP でクラスターに接続して、IPConfig を実行します。DNS サフィックスの例を次に示します。

		myhbase.b7.internal.cloudapp.net
- [Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) をダウンロードしてワークステーションにインストールします。パッケージから makecert を実行して証明書を作成する必要があります。  
- [Java ランタイム環境](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)をダウンロードしてワークステーションにインストールします。SQuirrel SQL クライアント バージョン 3.0 以降には、JRE バージョン 1.6 以降が必要です。  


### Azure Virtual Network へのポイント対サイト VPN 接続を構成します。

ポイント対サイト VPN 接続の構成には 3 つの手順があります。

1. [仮想ネットワークと動的ルーティング ゲートウェイを構成します](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [証明書を作成します](#Create-your-certificates)
3. [VPN クライアントを構成します](#Configure-your-VPN-client)

詳細については、「[Azure 仮想ネットワークへのポイント対サイト VPN 接続の構成](https://msdn.microsoft.com/library/azure/dn133792.aspx)」を参照してください。

#### 仮想ネットワークと動的ルーティング ゲートウェイの構成

Azure Virtual Network に HBase クラスターをプロビジョニングしてあることを確認します (このセクションの前提条件を参照)。次の手順では、ポイント対サイト接続を構成します。

**ポイント対サイト接続を構成するには**

1. [Azure ポータル][azure-portal]にサインインします。
2. 左側の **[ネットワーク]** をクリックします。
3. 作成してある仮想ネットワークをクリックします (「[Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]」を参照)。
4. 上部にある **[構成]** をクリックします。
5.  **[ポイント対サイト接続]** セクションで、**[ポイント対サイト接続の構成]** を選択します。 
6. **[開始 IP]** と **[CIDR]** を構成し、接続時に VPN クライアントが IP アドレスを受け取る IP アドレスの範囲を指定します。この範囲は、オンプレミスのネットワークに存在する範囲および接続先の Azure Virtual Network と重複することはできません。たとえば、仮想ネットワークに対して 10.0.0.0/20 を選択した場合、クライアント アドレス空間には 10.1.0.0/24 を選択できます。詳細については、「[[ポイント対サイト接続] ページ][vnet-point-to-site-connectivity]」を参照してください。
7. 仮想ネットワーク アドレス空間セクションで **[ゲートウェイ サブネットの追加]** をクリックします。
7. ページの下部にある **[保存]** をクリックします。
8. **[はい]** をクリックして変更を確定します。システムが変更を完了するまで待ってから、次の手順に進みます。


**動的ルーティング ゲートウェイを作成するには**

1. Azure ポータルで、ページの上部にある **[ダッシュボード]** をクリックします。
2. ページの下部にある **[ゲートウェイの作成]** をクリックします。
3. **[はい]** をクリックして確定します。ゲートウェイが作成されるまで待ちます。
4. 上部にある **[ダッシュボード]** をクリックします。仮想ネットワークの図が表示されます。

	![Azure Virtual Network のポイント対サイト仮想ダイアグラム][img-vnet-diagram]

	図ではクライアント接続が 0 であることが示されています。仮想ネットワークに接続すると、この値が 1 に更新されます。

#### 証明書の作成

X.509 証明書を作成する方法の 1 つは、[Microsoft Visual Studio Express 2013 for Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) に付属する証明書作成ツール (makecert.exe) を使用することです。


**自己署名ルート証明書を作成するには**

1. ワークステーションからコマンド プロンプト ウィンドウを開きます。
2. Visual Studio ツールのフォルダーに移動します。 
3. 次の例のコマンドを使用すると、ルート証明書が作成されてワークステーションの個人用証明書ストアにインストールされ、後で Azure ポータルにアップロードする対応する .cer ファイルが作成されます。 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	.cer ファイルを格納するディレクトリに移動します。HBaseVnetVPNRootCertificate は、証明書に使用する名前です。

	コマンド プロンプトを閉じないでください。これは、次の手順で必要になります。

	>[AZURE.NOTE]クライアント証明書の生成元となるルート証明書を作成したので、この証明書を秘密キーと共にエクスポートし、回復できる安全な場所に保存します。

**クライアント証明書を作成するには**

- 同じコマンド プロンプトから (ルート証明書を作成したのと同じコンピューターを使用する必要があります。クライアント証明書はルート証明書から生成する必要があります)、次のコマンドを実行します。

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate はルート証明書名です。  ルート証明書名と一致している必要があります。  

	ルート証明書とクライアント証明書は、どちらもコンピューターの個人用証明書ストアに保管されます。確認するには、certmgr.msc を使用してください。

	![Azure virtual network point-to-site vpn certificate][img-certificate]

	クライアント証明書は、仮想ネットワークに接続するコンピューターごとにインストールする必要があります。仮想ネットワークに接続するコンピューターごとに、一意のクライアント証明書を作成することをお勧めします。クライアント証明書をエクスポートするには、certmgr.msc を使用してください。 

**Azure ポータルにルート証明書をアップロードするには**

1. Azure ポータルの左側で **[ネットワーク]** をクリックします。
2. HBase クラスターのデプロイ先の仮想ネットワークをクリックします。
3. 上部の **[証明書]** をクリックします。
4. 下部の **[アップロード]** をクリックして、最後の前の手順で作成したルート証明書ファイルを指定します。証明書がインポートされるまで待ちます。
5. ページの上部にある **[ダッシュボード]** をクリックします。仮想図に状態が表示されます。


#### VPN クライアントの構成



**クライアント VPN パッケージをダウンロードしてインストールするには**

1. 仮想ネットワークの [ダッシュボード] ページの [概要] セクションで、ワークステーションの OS のバージョンに基づいて **[64 ビットのクライアント VPN パッケージのダウンロード]** または **[32 ビットのクライアント VPN パッケージのダウンロード]** をクリックします。
2. **[実行]** をクリックしてパッケージをインストールします。
3. セキュリティのプロンプトでは、**[詳細]** をクリックし、次に **[実行]** をクリックします。
4. **[はい]** を 2 回クリックします。

**VPN に接続するには**

1. ワークステーションのデスクトップで、タスク バーの [ネットワーク] アイコンをクリックします。仮想ネットワーク名と共に VPN 接続が表示されます。
2. VPN 接続の名前をクリックします。
3. **[接続]** をクリックします。

**VPN 接続とドメイン名の解決をテストするには**

- ワークステーションでコマンド プロンプトを開き、HBase クラスターの DNS サフィックスが myhbase.b7.internal.cloudapp.net である次のいずれかの名前に ping を実行します。

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

### ワークステーションへの SQuirrel のインストールと構成

**SQuirrel をインストールするには**

1. [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation) から SQuirrel SQL クライアントの jar ファイルをダウンロードします。
2. jar ファイルを開くか実行します。[Java ランタイム環境](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html)が必要です。
3. **[次へ]** を 2 回クリックします。
4. 書き込みアクセス許可のあるパスを指定し、**[次へ]** をクリックします。
	>[AZURE.NOTE]既定のインストール フォルダーは、C:\\Program Files\\squirrel-sql-3.6 です。このパスに書き込むには、インストーラーに管理者特権を付与する必要があります。管理者としてコマンド プロンプトを開き、Java の bin フォルダーに移動して実行します。
	>
	>     java.exe -jar [the path of the SQuirrel jar file] 
5. **[OK]** をクリックして、ターゲット ディレクトリの作成を確認します。
6. 既定の設定では、Base および Standard パッケージがインストールされます。**[次へ]** をクリックします。
7. **[次へ]** を 2 回クリックし、**[完了]** をクリックします。


**Phoenix ドライバーをインストールするには**

Phoenix ドライバーの jar ファイルは、HBase クラスターにあります。パスは、バージョンに基づく次のようなものです。

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
これを、ワークステーションの [SQuirrel インストール フォルダー]/lib パスにコピーする必要があります。最も簡単な方法は、RDP でクラスターに接続し、ファイルのコピー/貼り付け (Ctrl + C および Ctrl + V) を使用してワークステーションにコピーします。

**Phoenix ドライバーを SQuirrel に追加するには**

1. ワークステーションから SQuirrel SQL クライアントを開きます。
2. 左側の **[Driver]** タブをクリックします。
2. **[Drivers]** メニューから **[New Driver]** をクリックします。
3. 次の情報を入力します。

	- **Name**: Phoenix
	- **Example URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Class Name**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING][Example URL] はすべて小文字で指定します。

	![HDInsight HBase Phoenix SQuirrel ドライバー][img-squirrel-driver]
4. **[OK]** をクリックします。

**HBase クラスターの別名を作成するには**

1. SQuirrel で左側の **[Aliases]** タブをクリックします。
2. **[Aliases]** メニューの **[New Alias]** をクリックします。
3. 次の情報を入力します。

	- **Name**: HBase クラスターの名前または他の任意の名前。
	- **Driver**: Phoenix。これは、前の手順で作成したドライバー名と一致する必要があります。
	- **URL**: URL はドライバーの構成からコピーされます。すべて小文字を使用してください。
	- **User name**: HBase クラスターの HTTP ユーザー名
	- **Password**: HBase クラスターの HTTP ユーザー パスワード

	![HDInsight HBase Phoenix SQuirrel ドライバー][img-squirrel-alias]
4. **[Test]** をクリックします。 
5. **[接続]** をクリックします。接続が確立されると、SQuirrel の表示は次のようになります。

	![HBase Phoenix SQuirrel][img-squirrel]

**テストを実行するには**

1. **[Objects]** タブの右側の **[SQL]** タブをクリックします。
2. 次のコードをコピーして貼り付けます。

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. 実行ボタンをクリックします。

	![HBase Phoenix SQuirrel][img-squirrel-sql]
4. **[Objects]** タブに戻ります。
5. 別名を展開し、**[TABLE]** を展開します。新しいテーブルが下に一覧表示されます。
 
## 次のステップ
この記事では、HDInsight で Apache Phoenix を使用する方法を説明しました。詳細については、次を参照してください。

- 「[HDInsight HBase の概要][hdinsight-hbase-overview]」: HBase は、Hadoop 上に構築された Apache オープン ソースの NoSQL データベースです。大量の非構造化データおよび半構造化データに対するランダム アクセスと強力な一貫性を実現します。
- 「[Azure Virtual Network での HBase クラスターのプロビジョニング][hdinsight-hbase-provision-vnet]」: アプリケーションが HBase と直接通信できるように、仮想ネットワーク統合を使用して、HBase クラスターをアプリケーションと同じ仮想ネットワークにデプロイできます。
- 「[Configure HBase replication in HDInsight (HDInsight での HBase レプリケーションの構成)](hdinsight-hbase-geo-replication.md)」: 2 つの Azure データ センター間の HBase レプリケーションを構成する方法を説明します。 
- 「[HDInsight 環境の HBase で Twitter のセンチメントを分析する][hbase-twitter-sentiment]」: HDInsight の Hadoop クラスターで HBase を使用してリアルタイムでビッグ データの[センチメントを分析する](http://en.wikipedia.org/wiki/Sentiment_analysis)方法について説明します。

[azure-portal]: https://manage.windowsazure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png



<!--HONumber=52-->
