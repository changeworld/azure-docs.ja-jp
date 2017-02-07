---
title: "HDInsight クラスター作成時の Hive ライブラリの追加 | Microsoft Docs"
description: "クラスターの作成時に Hive ライブラリ (jar ファイル) を HDInsight クラスターに追加する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: a918737c762fe93cd8472f51dfdd945ceff6c877


---
# <a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>HDInsight クラスター作成時の Hive ライブラリの追加

HDInsight の Hive で頻繁に使用するライブラリがある場合、このドキュメントに含まれている情報を参考にし、スクリプト アクションを使用して、クラスターの作成時にライブラリを事前に読み込むことができます。 そうすると、Hive でライブラリをグローバルに利用できるようになります ( [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) を使用して読み込む必要がありません)。

## <a name="how-it-works"></a>動作のしくみ

クラスターの作成時に、必要に応じて、作成中のクラスター ノードに対してスクリプトを実行するスクリプト アクションを指定できます。 このドキュメントのスクリプトが受け取るパラメーターは&1; つで、事前に読み込むライブラリ (jar ファイルとして格納されている) が含まれている WASB の場所を指定します。

クラスターの作成時に、そのスクリプトによってファイルが列挙され、ヘッド ノードとワーカー ノードの `/usr/lib/customhivelibs/` ディレクトリにコピーされて、`core-site.xml` ファイルの `hive.aux.jars.path` プロパティに追加されます。 Linux ベースのクラスターでは、それらのファイルの場所に合わせて `hive-env.sh` ファイルも更新されます。

> [!NOTE]
> この記事のスクリプト アクションを使用すると、次のシナリオでライブラリを使用できます。
>
> * **Linux ベースの HDInsight** - **Hive コマンド ライン**、**WebHCat** (Templeton)、および **HiveServer2** を使用する場合。
> * **Windows ベースの HDInsight** - **Hive コマンド ライン**および **WebHCat** (Templeton) を使用する場合。
>
>

## <a name="the-script"></a>スクリプト
**スクリプトの場所**

**Linux ベースのクラスター**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

**Windows ベースのクラスター**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

**要件**

* このスクリプトは、**ヘッド ノード**と**ワーカー ノード**の両方に適用する必要があります。
* インストールする jar は、Azure Blob Storage の **単一のコンテナー**に格納する必要があります。
* jar ファイルのライブラリを含むストレージ アカウントを、作成時に HDInsight クラスターにリンクする**必要があります**。 これは、次の&2; つの方法のどちらかで行うことができます。

  * コンテナーをクラスターの既定のストレージ アカウントに配置する。
  * コンテナーをリンクされたストレージ コンテナーに配置する。 たとえば、ポータルでは、**[オプションの構成]**、**[リンクされたストレージ アカウント]** を使用して、ストレージを追加できます。
* コンテナーへの WASB パスは、スクリプト アクションのパラメーターとして指定する必要があります。 たとえば、jar が **mystorage** という名前のストレージ アカウントの **libs** というコンテナーに格納されている場合、パラメーターは **wasbs://libs@mystorage.blob.core.windows.net/** となります。

  > [!NOTE]
  > このドキュメントでは、既にストレージ アカウントと BLOB コンテナーを作成し、そこにファイルをアップロードしていることを前提としています。
  >
  > ストレージ アカウントを作成していない場合は、 [Azure ポータル](https://portal.azure.com)で作成できます。 その後、 [Azure Storage エクスプローラー](http://storageexplorer.com/) などのユーティリティを使用して、アカウントに新しいコンテナーを作成し、そこにファイルをアップロードできます。
  >
  >

## <a name="create-a-cluster-using-the-script"></a>スクリプトを使用してクラスターを作成する
> [!NOTE]
> 次の手順では、Linux ベースの新しい HDInsight クラスターを作成します。 Windows ベースの新しいクラスターを作成するには、クラスターの作成時にクラスターの OS として **Windows** を選択し、Bash スクリプトの代わりに Windows (PowerShell) スクリプトを使用します。
>
> Azure PowerShell または HDInsight .NET SDK を使用し、このスクリプトを使用してクラスターを作成することもできます。 これらの方法の詳細については、 [スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。
>
>

1. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」に記載されている手順を使用してクラスターのプロビジョニングを開始します。ただし、プロビジョニングを完了しないでください。
2. **[オプションの構成]** ブレードで **[スクリプト アクション]** を選択し、以下のように情報を指定します。

   * **[名前]**: スクリプト アクションの表示名を入力します。
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
   * **[ヘッド]**: このオプションをオンにします。
   * **[ワーカー]**: このオプションをオンにします。
   * **[ZOOKEEPER]**: 空白のままにします。
   * **[パラメーター]**: jar が格納されているコンテナーとストレージ アカウントの WASB アドレスを入力します。 たとえば、**wasbs://libs@mystorage.blob.core.windows.net/** です。
3. **[スクリプト アクション]** の下部で、**[選択]** を使用して構成を保存します。
4. **[オプションの構成]** ブレードで、**[リンクされたストレージ アカウント]** を選択し、**[ストレージ キーの追加]** リンクをクリックします。 jar を含むストレージ アカウントを選択し、**[選択]** ボタンを使用して、設定を保存し、**[オプションの構成]** ブレードに戻ります。
5. **[オプションの構成]** ブレードの下部にある **[選択]** を使用して、オプションの構成情報を保存します。
6. 「[Linux の HDInsight クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」の説明に従って、クラスターのプロビジョニングを続行します。

クラスターの作成が完了したら、このスクリプトで追加した jar を、 `ADD JAR` ステートメントを使用することなく、Hive から使用できます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、クラスターの作成時に、jar ファイルに格納されている Hive ライブラリを HDInsight クラスターに追加する方法について説明しました。 Hive の使用法の詳細については、「 [HDInsight での Hive の使用](hdinsight-use-hive.md)



<!--HONumber=Jan17_HO3-->


