<properties 
	pageTitle="HDInsight での Script Action 開発 | Microsoft Azure" 
	description="Script Action を使用して Hadoop クラスターをカスタマイズする方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="jgao"/>

# HDInsight 用の Script Action スクリプトの開発 

Script Action は、Hadoop クラスターで実行される追加のソフトウェアのインストールや、クラスターにインストールされているアプリケーションの構成を変更するために使用することができます。Script Action は、HDInsight クラスターがデプロイされる際にクラスター ノードで実行されるスクリプトであり、クラスター内のノードが HDInsight 構成を完了すると実行されます。Script Action はシステム管理者アカウント権限で実行され、完全なアクセス権をクラスター ノードに提供します。各クラスターには、指定された順序で実行される Script Action の一覧が提供されます。




## Script Action を呼び出す

HDInsight は、HDInsight クラスターで追加のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

名前 | スクリプト
----- | -----
**Spark のインストール** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」をご覧ください。
**R のインストール** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1「[HDInsight Hadoop クラスターに R をインストールして使用する][hdinsight-r-scripts]」をご覧ください。
**Solr のインストール** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1「[HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」をご覧ください。
- **Giraph のインストール** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1「[HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」をご覧ください。

Script Action は、Azure ポータル、Azure PowerShell から、または HDInsight .NET SDK を使用してデプロイできます。詳細については、「[Script Action を使用した HDInsight のカスタマイズ][hdinsight-cluster-customize]」に関するページをご覧ください。

> [AZURE.NOTE]サンプル スクリプトは、HDInsight クラスター Version 3.1 以降でのみ機能します。HDInsight クラスター バージョンの詳細については、「[HDInsight クラスター バージョン](../hdinsight-component-versioning/)」をご覧ください。

## サンプル スクリプト

サイトの構成ファイルを構成するためのサンプル スクリプトを次に示します。

	param (
	    [parameter(Mandatory)][string] $ConfigFileName,
	    [parameter(Mandatory)][string] $Name,
	    [parameter(Mandatory)][string] $Value,
	    [parameter()][string] $Description
	)
	
	if (!$Description) {
	    $Description = ""
	}
	
	$hdiConfigFiles = @{
	    "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
	    "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
	    "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
	    "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
	    "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
	}
	
	if (!($hdiConfigFiles[$ConfigFileName])) {
	    Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
	    return
	}
	
	[xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]
	
	$existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}
	    
	if ($existingproperty) {
	    $existingproperty.Value = $Value
	    $existingproperty.Description = $Description
	} else {
	    $newproperty = @($configFile.configuration.property)[0].Clone()
	    $newproperty.Name = $Name
	    $newproperty.Value = $Value
	    $newproperty.Description = $Description
	    $configFile.configuration.AppendChild($newproperty)
	}
	
	$configFile.Save($hdiConfigFiles[$ConfigFileName])
	
	Write-HDILog "$configFileName has been configured."

スクリプト ファイルのコピーは、[https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1) から入手できます。Azure ポータルからスクリプトを呼び出すと、次のパラメーターを使用することができます。

	hive-site.xml hive.metastore.client.socket.timeout 90

これらのパラメーターは、hive-site.xml ファイルの hive.metastore.client.socket.timeout の値を 90 に設定します。既定値は 60 秒です。

## スクリプトの開発におけるベスト プラクティス

HDInsight クラスター向けのカスタム スクリプトを開発する際、留意すべきベスト プラクティスがあります。

- Hadoop のバージョンのチェック

	HDInsight Version 3.1 (Hadoop 2.4) 以降のみが、Script Action を使用したクラスターでのカスタム コンポーネントのインストールをサポートします。カスタム スクリプトでは、スクリプト内のその他のタスクの実行を続行する前に、バージョンをチェックするために、**Get-HDIHadoopVersion** ヘルパー メソッドを使用する必要があります。


- スクリプト リソースへの安定したリンクの提供

	ユーザーは、クラスターのカスタマイズで使用されるすべてのスクリプトとその他のアーティファクトがクラスターの有効期間全体で使用できること、実行中これらのファイルのバージョンが変更されないことを確認する必要があります。クラスター内のノードの再イメージ化が必要な場合、これらのリソースが必要になります。ベスト プラクティスとして、すべてをダウンロードして、ユーザーを制御するストレージ アカウントにアーカイブします。これは、既定のストレージ アカウントか、カスタマイズされたクラスターのデプロイ時に指定されている追加のストレージ アカウントのいずれかです。たとえば、ドキュメントに用意されている Spark と R のカスタマイズされたクラスターのサンプルでは、このストレージ アカウントにリソースのローカル コピーが作成されていますhttps://hdiconfigactions.blob.core.windows.net/。


- クラスターのカスタマイズ スクリプトはべき等にする

	HDInsight クラスターのノードはクラスターの有効期間中に再イメージ化されることを予測する必要があります。クラスターのカスタマイズ スクリプトは、クラスターが再イメージ化されるたびに実行されます。再イメージ化の際に、クラスターの最初の作成時に初めてスクリプトが実行された直後と同じカスタマイズ状態に戻ることを確認するという意味で、このスクリプトはべき等であるように設計する必要があります。たとえば、カスタム スクリプトが最初の実行時にアプリケーションを D:\AppLocation にインストールする場合、その後スクリプトを実行するたびに、再イメージ化の際、スクリプトはアプリケーションが D:\AppLocation に存在するかどうかを確認した後で、スクリプト内の他のステップを続行する必要があります。


- 最適な場所にカスタム コンポーネントをインストールする

	クラスター ノードが再イメージ化されると、C:\ リソース ドライブと D:\ システム ドライブは再フォーマットされ、それらのドライブにインストールされたデータとアプリケーションが失われる可能性があります。これは、クラスターの一部である Azure 仮想マシン (VM) ノードが停止して、新しいノードに置き換えられた場合にも起こる可能性があります。コンポーネントは D:\ ドライブやクラスタ上の C:\apps にインストールできます。C:\ ドライブのその他の場所はすべて予約されています。クラスターのカスタマイズのスクリプトに、アプリケーションやライブラリがインストールされる場所を指定します。


- クラスターのアーキテクチャの高可用性を確保

	HDInsight は、高可用性を確保するために、アクティブ / パッシブ アーキテクチャを備えています。1 つのヘッドノードはアクティブ モード (HDInsight サービスが実行されている) であり、その他のヘッドノードはスタンバイ モード (HDInsight サービスが実行されていない) です。HDInsight サービスが中断されると、ノードはアクティブ モードおよびパッシブ モードを切り替えます。高可用性を確保する目的で Script Action を使用して両方のヘッド ノードにサービスをインストールする場合、HDInsight のフェールオーバー メカニズムが、このようなユーザーがインストールしたサービスを自動的にフェールオーバーできなくなるため、注意してください。そのため、高可用性が期待される HDInsight ヘッド ノードにユーザーがインストールしたサービスは、アクティブ/パッシブ モードの場合に独自のフェールオーバーのメカニズムを持つか、アクティブ　 / アクティブ モードになる必要があります。

	HDInsight Script Action コマンドは、*ClusterRoleCollection* パラメーターでヘッドノードの役割が値として指定される場合、両方のヘッドノードで実行します (以下の [Script Action の実行方法](#runScriptAction)に記載)。そのため、カスタム スクリプトの設計時には、スクリプトがこの設定を認識するようにしてください。両方のヘッドノードで同じサービスがインストールし、開始され、互いに競合するという問題を回避する必要があります。また、再イメージ化の際にデータが失われるので、Script Action を通じてインストールされるソフトウェアはそうした事象に対して回復可能である必要があります。多数のノードに分散された高可用性のデータを操作するアプリケーションを設計する必要があります。同時に再イメージ化できる、クラスター内のノードは最大で 1/5 であることをに注意してください。


- Azure BLOB ストレージを使用するカスタム コンポーネントの構成

	クラスター ノードにインストールするカスタム コンポーネントは、Hadoop 分散ファイル システム (HDFS) ストレージを使用するように既定で構成されている場合があります。代わりに Azure BLOB ストレージを使用するには、構成を変更する必要があります。クラスターの再イメージ化の際に、HDFS ファイル システムはフォーマットされ、保管されているすべてのデータが失われます。代わりに Azure BLOB ストレージを使用すると、データは確実に保持されます。

## カスタム スクリプトのためのヘルパー メソッド 

Script Action は、カスタム スクリプトの書き込み中に使用できる次のヘルパー メソッドを提供しています。

ヘルパー メソッド | 説明
-------------- | -----------
**Save-HDIFile** | クラスターに割り当てられた Azure VM のノードに関連付けられているローカル ディスク上の場所に、指定した Uniform Resource Identifier (URI) からファイルをダウンロードする
**Expand-HDIZippedFile** | ZIP ファイルを解凍する
**Invoke-HDICmdScript** | cmd.exe からスクリプトを実行する
**Write-HDILog** | Script Action に使用されるカスタム スクリプトから出力を書き込む
**Get-Services** | スクリプトが実行されるコンピューターで実行されているサービスの一覧を取得する
**Get-Service** | 入力としてサービス名が指定されると、スクリプトが実行されるコンピューター上の特定のサービスの詳細な情報を取得する (サービス名、プロセス ID、状態など)
**Get-HDIServices** | スクリプトが実行されるコンピューターで実行されている HDInsight サービスの一覧を取得する
**Get-HDIService** | 入力として HDInsight サービス名が指定されると、スクリプトが実行されるコンピューター上の特定のサービスの詳細な情報を取得する (サービス名、プロセス ID、状態など)
**Get-ServicesRunning** | スクリプトが実行されるコンピューターで実行されているサービスの一覧を取得する
**Get-ServiceRunning** | スクリプトが実行されるコンピューター上で (名前別に) 特定のサービスが実行されているかどうかを確認する
**Get-HDIServicesRunning** | スクリプトが実行されるコンピューターで実行されている HDInsight サービスの一覧を取得する
**Get-HDIServiceRunning** | スクリプトが実行されるコンピューター上で (名前別に) 特定の HDInsight サービスが実行されているかどうかを確認する
**Get-HDIHadoopVersion** | スクリプトが実行されるコンピューターにインストールされている Hadoop のバージョンを取得する
**Test-IsHDIHeadNode** | スクリプトが実行されるコンピューターがヘッドノードであるかどうかを確認する
**Test-IsActiveHDIHeadNode** | スクリプトが実行されるコンピューターがアクティブなヘッドノードであるかどうかを確認する
**Test-IsHDIDataNode** | スクリプトが実行されるコンピューターがデータノードであるかどうかを確認する
**Edit-HDIConfigFile** | 構成ファイル hive-site.xml、core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml を編集する

## 一般的な使用パターン

このセクションでは、独自のカスタム スクリプトの書き込み中に発生する可能性がある一般的な使用状況パターンの実装についてのガイダンスを提供します。

### 環境変数を構成する

多くの場合、スクリプト アクションの開発では、環境変数を設定する必要があると感じることがあります。たとえば、最も可能性の高いシナリオは、外部サイトからバイナリをダウンロードし、クラスターにインストールして、'PATH' 環境変数にインストールされている場所を追加する場合です。次のスニペットでは、カスタム スクリプトで環境変数を設定する方法を示します。

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

このステートメントは、環境変数 **MDS_RUNNER_CUSTOM_CLUSTER** を値 'true' に設定します。またコンピューター全体にこの変数の範囲を設定します。環境変数はコンピューターやユーザーの適切な範囲で設定されていることが重要になることがあります。環境変数の設定の詳細については、[こちら][1]を参照してください。

### カスタム スクリプトを保管する場所へのアクセス

クラスターのカスタマイズに使用したスクリプトは、クラスターの既定のストレージ アカウントかその他のストレージ アカウントにおける読み取り専用のパブリック コンテナーのいずれかにする必要があります。スクリプトが他の場所に配置されているリソースにアクセスする場合、パブリックでアクセスできる (少なくともパブリック読み取り専用にする) ようにする必要があります。たとえば、SaveFile HDI コマンドを使用してファイルにアクセスし、保存する必要がある場合などです。

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

この例では、ストレージ アカウント「somestorageaccount」のコンテナー「somecontainer」を、パブリックからアクセスできるようにする必要があります。そうしないと、スクリプトは「Not Found」例外をスローして失敗します。

### 障害が発生したクラスターのデプロイメントにおける例外の実行

クラスターのカスタマイズが予想通りに成功しなかった場合に正確な通知を受け取るには、例外を実行してクラスターのプロビジョニングを失敗させることが重要になります。たとえば、ファイルが存在する場合はファイルを処理し、ファイルが存在しない場合はエラー状況を処理する必要があります。これにより、スクリプトは正常に終了し、クラスターの状態が正確に把握できるようになります。次のスニペットでは、これを実行する方法の例を示します。

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

このスニペットでは、ファイルが存在しない場合、スクリプトは、エラー メッセージを表示した後に実際には正常終了の状態になります。そのため、クラスターは、クラスター カスタマイズ プロセスが "正常に" 完了したと見なして実行状態になります。ファイルが存在しなかったために実際はクラスターのカスタマイズが期待したとおりに進まなかったという事実を正確に通知する必要がある場合は、例外をスローしてクラスター カスタマイズ ステップを失敗させるほうが適切です。その場合は、代わりに次のサンプル コード スニペットを使用してください。

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	throw
	}


## Script Action のデプロイ用チェックリスト
スクリプトのデプロイを準備する際に実行した手順を次に示します。

1. カスタム スクリプトが含まれているファイルを、デプロイ時にクラスター ノードからアクセス可能な場所に配置します。これには、クラスターのデプロイ時に指定された既定または追加のストレージ アカウント、またはその他のパブリックにアクセス可能なストレージ コンテナーを指定できます。
2. スクリプトがべき等に実行されるようにチェックを追加し、スクリプトが同じノードで複数回実行可能にします。
3. **Write-Output** Azure Powershell コマンドレットを使用して、STDOUT と STDERR に出力します。**Write-Host** を使用しないでください。
4. $env:TEMP などの一時ファイル フォルダーを使用して、スクリプトで使用するダウンロード済みファイルを維持し、スクリプトの実行後にそれらをクリーンアップします。
5. カスタム ソフトウェアは D:\ または C:\apps のみにインストールします。C: ドライブのその他の場所は予約されているため使用しないでください。C:\apps フォルダー以外の C: ドライブにファイルをインストールすると、ノードの再イメージ化の際にセットアップが失敗する可能性があります。
6. OS レベル設定や Hadoop サービス構成ファイルが変更された場合、スクリプトに設定された環境変数などの OS レベル設定を有効にするために、HDInsight サービスを再起動することをお勧めします。



## HDInsight Emulator を使用してカスタム スクリプトをテストする

カスタム スクリプトを HDInsight のスクリプト アクション コマンドで使用する前にテストする簡単でわかりやすい方法は、HDInsight Emulator で実行することです。HDInsight Emulator をローカルにインストールするか、Azure IaaS (サービスとしてのインフラストラクチャ) Windows Server 2012 R2 仮想マシンやローカル コンピューターにインストールして、スクリプトが正しく動作することを確認できます。Windows Server 2012 R2 VM は、HDInsight がそのノード向けに使用するものと同じ VM であることに注意してください。

このセクションでは、テストの目的でローカルで HDInsight Emulator を使用するための手順を説明しますが、VM を使用する手順と同様です。

**HDInsight Emulator のインストール**: Script Actions をローカルで実行するには、HDInsight Emulator をインストールしておく必要があります。インストールする方法の手順については、「[HDInsight Emulator の概要](../hdinsight-get-started-emulator/)」をご覧ください。

**Azure PowerShell の実行ポリシーの設定**: Azure PowerShell を開き、(管理者として) 次のコマンドを実行して実行ポリシーを *LocalMachine* と *Unrestricted* に設定します。
 
	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

スクリプトが署名されていないため、このポリシーを無制限にする必要があります。

ローカルのコピー先で実行する**スクリプト アクションをダウンロード**します。次のサンプル スクリプトは、次の場所からダウンロードできます。

* **Spark**https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**スクリプト アクションの実行**: 管理者モードで新しい Azure PowerShell ウィンドウを開き、保存されているローカルの場所から Spark や R のインストール スクリプトを実行します。

**使用例**: Spark や R のクラスターを使用している場合、必要なデータ ファイルが HDInsight Emulator に表示されないことがあります。その場合、HDFS のパスへのデータを含む関連する .txt ファイルをアップロードし、そのパスを使ってデータにアクセスする必要があります。次に例を示します。

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


場合によっては、カスタム スクリプトは、特定の Hadoop サービスが実行中かどうかの検出などを HDInsight のコンポーネントに依存することが実際にあるので注意してください。この場合、カスタム スクリプトを実際の HDInsight クラスターに配置してテストする必要があります。


## カスタム スクリプトをデバッグする

スクリプト エラー ログは、他の出力と共に、クラスターの作成時に指定した既定のストレージ アカウント内に格納されます。ログは、*u<\cluster-name-fragment><\time-stamp>setuplog* という名前のテーブルに格納されます。これらはクラスター内でスクリプトが実行されるすべてのノード (ヘッドノードとワーカー ノード) から取得されたレコードを持つ集計ログです。

カスタム スクリプトの STDOUT と STDERR の両方を表示するために、クラスター ノードにリモート接続することもできます。各ノード上のログは、そのノード専用で、**C:\HDInsightLogs\DeploymentAgent.log** に記録されます。これらのログ ファイルには、カスタム スクリプトからのすべての出力が記録されます。Spark Script Action のログ スニペットの例は次のようになります。

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
このログから、HEADNODE0 という名前の VM で Spark Script Action が実行され、実行中に例外がスローされなかったことがわかります。

実行エラーが発生すると、それを記述する出力もこのログ ファイルに含まれます。これらのログで提供される情報は、発生する可能性のあるスクリプトの問題をデバッグするときに役立ちます。


## 関連項目

- [Script Action を使って HDInsight クラスターをカスタマイズする][hdinsight-cluster-customize] 
- [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
- [HDInsight クラスターに R をインストールして使用する][hdinsight-r-scripts]
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
 

<!---HONumber=July15_HO4-->