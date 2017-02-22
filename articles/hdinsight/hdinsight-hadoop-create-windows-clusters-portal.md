---
title: "HDInsight での Windows ベースの Hadoop クラスターの作成 | Microsoft Docs"
description: "Azure Portal を使用した HDInsight での Windows ベースの Hadoop の作成方法を説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Azure Portal を使用した HDInsight での Windows ベースの Hadoop クラスターの作成

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure Portal を使用して HDInsight で Windows ベースの Hadoop クラスターを作成する方法について説明します。 

この記事の情報は、Windows ベースの HDInsight クラスターにのみ適用されます。 Linux ベースのクラスターの作成方法の詳細については、[Azure Portal を使用した HDInsight での Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順を開始する前に、次の項目を用意する必要があります。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>クラスターの作成
**HDInsight クラスターを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順にクリックします。
3. 次の値を入力または選択します。
   
   * **クラスター名**: クラスターの名前を指定します。
   * **サブスクリプション**: このクラスターに使用する Azure サブスクリプションを選択します。
   * **クラスター構成**:

     * **クラスターの種類**: このチュートリアルでは、**[hadoop]** を選択します。
     * **オペレーティング システム**: このチュートリアルでは、**[Windows]** を選択します。
     * **バージョン**: **[Hadoop 2.7.0 (HDI 3.3)]** を選択します。  これが、Windows ベースの Hadoop クラスターの最新のバージョンです。
     * **クラスター レベル**: このチュートリアルでは、**[Standard]** を選択します。

   * **資格情報**:

     * **クラスター ログイン ユーザー名**: 既定の名前は **admin** です。 
     * **クラスター ログイン パスワード**: パスワードを入力します。
     * **リモート デスクトップの有効化**: このチュートリアルでは、リモート デスクトップは必要ありません。

   * **データ ソース**:

     * **メソッドの選択**: **[すべてのサブスクリプションから]** を選択します。
     * **ストレージ アカウントを選択してください**: **[新規作成]** をクリックして、既定のストレージ アカウントの名前を入力します。
     * **既定のコンテナー**: 既定では、既定のコンテナーにはクラスターと同じ名前を使用します。
     * **場所**: 近くの場所を選択します。  この場所は、クラスターと既定のストレージ アカウントの両方で使用されます。
   * **クラスター サイズ**:

     * **worker ノードの数**: このチュートリアルで必要なのは、1 つのノードのみです。
   * **詳細な構成**: このチュートリアルではこの部分をスキップすることができます。
   * **リソース グループ**: **[新規作成]** を選択して、名前を入力します。

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。 **[スタート画面にピン留めする]** を選択すると、ポータルのスタート画面にクラスターのタイルが追加されます。 クラスターの作成には 15 ～ 20 分かかります。プロビジョニング プロセスを確認するには、スタート画面のタイルまたはページの左側の **[通知]** エントリを使用します。
5. 作成が完了したら、スタート画面でクラスター用のタイルをクリックして、クラスター ブレードを起動します。 クラスター ブレードには、名前、属しているリソース グループ、場所、オペレーティング システム、クラスター ダッシュボードの URL などのクラスターに関する基本情報が表示されます。

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md)」を参照してください。
* 「 [スクリプト アクションを使用して Windows ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md) - HDInsight クラスターの使用方法について説明しています。
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md) - プログラムを使用して HDInsight にジョブを送信する方法について説明しています。
* [Azure Portal を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


