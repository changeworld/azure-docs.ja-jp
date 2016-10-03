<properties
pageTitle="HDInsight クラスター作成時の Hive ライブラリの追加 | Azure"
description="クラスターの作成時に Hive ライブラリ (jar ファイル) を HDInsight クラスターに追加する方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/20/2016"
ms.author="larryfr"/>

#HDInsight クラスター作成時の Hive ライブラリの追加

HDInsight の Hive で頻繁に使用するライブラリがある場合、このドキュメントに含まれている情報を参考にし、スクリプト アクションを使用して、クラスターの作成時にライブラリを事前に読み込むことができます。そうすると、Hive でライブラリをグローバルに利用できるようになります ([ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) を使用して読み込む必要がありません)。

##動作のしくみ

クラスターの作成時に、必要に応じて、作成中のクラスター ノードに対してスクリプトを実行するスクリプト アクションを指定できます。このドキュメントのスクリプトが受け取るパラメーターは 1 つで、事前に読み込むライブラリ (jar ファイルとして格納されている) が含まれている WASB の場所を指定します。

クラスターの作成時に、そのスクリプトによってファイルが列挙され、ヘッド ノードとワーカー ノードの `/usr/lib/customhivelibs/` ディレクトリにコピーされて、`core-site.xml` ファイルの `hive.aux.jars.path` プロパティに追加されます。Linux ベースのクラスターでは、それらのファイルの場所に合わせて `hive-env.sh` ファイルも更新されます。

> [AZURE.NOTE] この記事のスクリプト アクションを使用すると、次のシナリオでライブラリを使用できます。
>
> * __Linux ベースの HDInsight__ - __Hive コマンド ライン__、__WebHCat__ (Templeton)、および __HiveServer2__ を使用する場合。
> * __Windows ベースの HDInsight__ - __Hive コマンド ライン__および __WebHCat__ (Templeton) を使用する場合。

##スクリプト

__スクリプトの場所__

__Linux ベースのクラスター__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

__Windows ベースのクラスター__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__要件__

* このスクリプトは、__ヘッド ノード__と__ワーカー ノード__の両方に適用する必要があります。

* インストールする jar は、Azure Blob Storage の__単一のコンテナー__に格納する必要があります。

* jar ファイルのライブラリを含むストレージ アカウントを、作成時に HDInsight クラスターにリンクする__必要があります__。これは、次の 2 つの方法のどちらかで行うことができます。

    * コンテナーをクラスターの既定のストレージ アカウントに配置する。
    
    * コンテナーをリンクされたストレージ コンテナーに配置する。たとえば、ポータルでは、__[オプションの構成]__、__[リンクされたストレージ アカウント]__ を使用して、ストレージを追加できます。

* コンテナーへの WASB パスは、スクリプト アクションのパラメーターとして指定する必要があります。たとえば、jar が __mystorage__ という名前のストレージ アカウントの __libs__ というコンテナーに格納されている場合、パラメーターは wasbs://libs@mystorage.blob.core.windows.net/__ となります。

    > [AZURE.NOTE] このドキュメントでは、既にストレージ アカウントと BLOB コンテナーを作成し、そこにファイルをアップロードしていることを前提としています。
    >
    > ストレージ アカウントを作成していない場合は、[Azure ポータル](https://portal.azure.com)で作成できます。その後、[Azure Storage エクスプローラー](http://storageexplorer.com/)などのユーティリティを使用して、アカウントに新しいコンテナーを作成し、そこにファイルをアップロードできます。

##スクリプトを使用してクラスターを作成する

> [AZURE.NOTE] 次の手順では、Linux ベースの新しい HDInsight クラスターを作成します。Windows ベースの新しいクラスターを作成するには、クラスターの作成時にクラスターの OS として __Windows__ を選択し、Bash スクリプトの代わりに Windows (PowerShell) スクリプトを使用します。
> 
> Azure PowerShell または HDInsight .NET SDK を使用し、このスクリプトを使用してクラスターを作成することもできます。これらの方法の詳細については、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md#portal)」に記載されている手順を使用してクラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。

2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下のように情報を指定します。

    * __[名前]__: スクリプト アクションの表示名を入力します。
    * __[スクリプト URI]__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __[ヘッド]__: このオプションをオンにします。
    * __[ワーカー]__: このオプションをオンにします。
    * __[ZOOKEEPER]__: 空白のままにします。
    * __[パラメーター]__: jar が格納されているコンテナーとストレージ アカウントの WASB アドレスを入力します。たとえば、「wasbs://libs@mystorage.blob.core.windows.net/__」のように入力します。

3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。

4. **[オプションの構成]** ブレードで、__[リンクされたストレージ アカウント]__ を選択し、__[ストレージ キーの追加]__ リンクをクリックします。jar を含むストレージ アカウントを選択し、__[選択]__ ボタンを使用して、設定を保存し、__[オプションの構成]__ ブレードに戻ります。

5. **[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。

6. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md#portal)」の説明に従って、クラスターのプロビジョニングを続行します。

クラスターの作成が完了したら、このスクリプトで追加した jar を、`ADD JAR` ステートメントを使用することなく、Hive から使用できます。

##次のステップ

このドキュメントでは、クラスターの作成時に、jar ファイルに格納されている Hive ライブラリを HDInsight クラスターに追加する方法について説明しました。Hive の使用法の詳細については、「[HDInsight での Hive の使用](hdinsight-use-hive.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->