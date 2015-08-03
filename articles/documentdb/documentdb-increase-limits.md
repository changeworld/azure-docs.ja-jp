<properties 
	pageTitle="DocumentDB アカウント制限の引き上げ要求 | Azure" 
	description="使用できるコレクション数、ストアド プロシージャ数、クエリ句数など、DocumentDB 制限の調整を要求する方法について説明します。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="stbaro"/>

# DocumentDB アカウント制限の引き上げを要求する

[Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) には既定で、複数の制限とクォータの適用が設定されています。一部のクォータは、Azure サポートに問い合わせて調整できます。この記事では、アカウント制限の引き上げを要求する方法について説明します。

この記事を読むと、次の質問に回答できるようになります。

-	Azure サポートに問い合わせて調整できるのは、どの DocumentDB アカウント クォータか。
-	DocumentDB アカウント クォータの調整を要求するにはどうすればよいか。

##<a id="AdjustableQuotas"></a> 調整可能な DocumentDB アカウント クォータ

Azure サポートに問い合わせて調整できる DocumentDB クォータは、次の表のとおりです。

|エンティティ |クォータ (Standard プラン)|
|-------|--------|
|データベース アカウント |5
|コレクションあたりのストアド プロシージャ、トリガー、UDF の数 |それぞれ 25 個
|データベース アカウントあたりの最大コレクション |100
|データベースあたりの最大ドキュメント ストレージ (100 コレクション単位) |1 TB (テラバイト)
|クエリあたりの最大 UDF 数 |1
|クエリあたりの最大 JOIN 数 |2
|クエリあたりの AND 句の最大数 |5
|クエリあたりの OR 句の最大数 |5
|IN 式あたりの値の最大数 |100
|1 分あたりのコレクション作成の最大数 |5
|1 分あたりのスケール操作の最大数 |5

##<a id="RequestQuotaIncrease"></a> クォータ調整の要求
クォータ調整を要求するには、次の手順を実行します。

1. [Azure プレビュー ポータル](https://portal.azure.com)の **[参照]**、**[ヘルプとサポート]** を順にクリックします。

	![Screenshot of launching help and support](media/documentdb-increase-limits/helpsupport.png)

2. **[ヘルプとサポート]** ブレードで **[サポートを受ける]** をクリックします。

	![Screenshot of creating a support ticket](media/documentdb-increase-limits/getsupport.png)

3. **[新しいサポート リクエスト]** ブレードで **[要求の種類]** をクリックし、**[要求の種類]** ブレードで **[クォータ]** をクリックします。

	![Screenshot of support ticket request type](media/documentdb-increase-limits/supportrequest1.png)

4. **[サブスクリプション]** ブレードで、ご使用の DocumentDB アカウントをホストしているサブスクリプションを選択します。

	![Screenshot of support ticket subscription picker](media/documentdb-increase-limits/supportrequest2.png)

5. **[リソース]** ブレードで **[DocumentDB アカウント]** を選択します。

	![Screenshot of support ticket resource picker](media/documentdb-increase-limits/supportrequest3.png)

6. **[サポート プラン]** ブレードで **[クォータの無料サポート]** を選択します。

	![Screenshot of support ticket support plan picker](media/documentdb-increase-limits/supportrequest4.png)

7. **[問題]** ブレードで、問題のカテゴリとして **[クォータまたはコアの増加の要求 DocumentDB]** を選択します。

	![Screenshot of support ticket problem category picker](media/documentdb-increase-limits/supportrequest5.png)

8. **[説明]** ブレードに、要求の説明を入力します。必ず、調整が必要なアカウント (複数ある場合はすべて) と、要求しているクォータ調整の具体的な内容を含めてください。

	![Screenshot of support ticket description textbox](media/documentdb-increase-limits/supportrequest6.png)

9. **[作成]** をクリックします。

	![Screenshot of support ticket create button](media/documentdb-increase-limits/supportrequest7.png)

サポート チケットが作成されると、サポート要求番号が電子メールで届きます。サポート要求は、**[ヘルプとサポート]** ブレードの **[サポート要求]** をクリックして表示することもできます。

![Screenshot of support requests blade](media/documentdb-increase-limits/supportrequest8.png)
  

##<a name="NextSteps"></a>次のステップ
- DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。
 

<!---HONumber=July15_HO4-->