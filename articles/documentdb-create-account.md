<properties title="How to Create a DocumentDB Account" pageTitle="How to create a DocumentDB account | Azure" description="Find out how to create a DocumentDB account and choose account settings in the Azure Preview portal."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# DocumentDB アカウントの作成方法

DocumentDB を使用するには、利用可能なリージョンの DocumentDB アカウントを作成する必要があります。このトピックでは、Azure プレビュー ポータルで DocumentDB アカウントを作成する方法について説明します。

## 目次

-   [方法: DocumentDB アカウントを作成する][方法: DocumentDB アカウントを作成する]
-   [次のステップ][次のステップ]

## <span id="HowTo"></span></a>方法: DocumentDB アカウントを作成する

1.  [Azure 管理プレビュー ポータル][Azure 管理プレビュー ポータル]にサインインします。
2.  [新規]、[DocumentDB アカウント] の順にクリックします。<br/>
    ![][0]

    または、Azure ギャラリーを参照し、[データ、ストレージ、+ バックアップ] カテゴリを選択して、**[DocumentDB]** を選択します。次に、**[作成]** をクリックします。

    ![][1]

    **[新しい DocumentDB (プレビュー)]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。

    ![][2]

    [名前] に、DocumentDB アカウントの URI に使用する名前を入力します。この値は、DocumentDB アカウントのアドレス指定に使用される URI 内のホスト名になります。名前のエントリに含めることができるのは英小文字、数字、および '-' 文字のみで、文字数は 3 ～ 50 文字にする必要があります。

    *選択したエンドポイント名に documents.azure.com が追加され、これが DocumentDB アカウント エンドポイントになります。*

    DocumentDB プレビューで単一の標準料金レベルがサポートされているため、**[料金レベル]** レンズはロックされています。

    *詳細については、「[DocumentDB PREVIEW pricing details (DocumentDB プレビュー料金の詳細)][DocumentDB PREVIEW pricing details (DocumentDB プレビュー料金の詳細)]」を参照してください。*

    **[省略可能な構成]** レンズは、DocumentDB アカウントに割り当てられる初期の容量を指定するときに使用されます。DocumentDB では、容量単位を利用して、占有データベース ストレージとスループットが各容量単位に含まれる、DocumentDB アカウントを拡張できます。既定では、1 つの容量単位がプロビジョニングされます。DocumentDB アカウントで利用可能な容量単位の数は、[Azure 管理プレビュー ポータル][Azure 管理プレビュー ポータル]でいつでも調整できます。

    *DocumentDB アカウントの容量およびスループットの詳細については、「[DocumentDB の容量とパフォーマンスの管理][DocumentDB の容量とパフォーマンスの管理]」という記事を参照してください。*

    **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。

    *詳細については、「[リソース グループを使用した Azure リソースの管理][リソース グループを使用した Azure リソースの管理]」を参照してください。*

    **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。

    *アカウントのサブスクリプションが 1 つである場合は、それが自動的に選択されます。*

    **[場所]** を指定して、DocumentDB アカウントのホストの場所を指定します。

3.  新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントが作成されるまで数分かかる場合があります。状態を確認するには、スタートボードで進行状況を監視してください。<br/>
    ![][3]

    または、[通知] ハブを使用します。<br/>

    ![][4]

    ![][5]

4.  DocumentDB アカウントが作成されたら、既定の設定で使用するための準備が整います。

    *DocumentDB アカウントの既定の一貫性は Session に設定されることに注意してください。既定の一貫性の設定は、[Azure 管理プレビュー ポータル][Azure 管理プレビュー ポータル]を使用して調整できます。*<br/>


    ![][6]

5.  **[参照]** ブレードから、既存の DocumentDB アカウントにアクセスすることもできます。<br/><br/>



    ![][7]

## <span id="NextSteps"></span></a>次のステップ

-   DocumentDB の詳細については、[azure.com][azure.com] で Azure DocumentDB に関するドキュメントを参照してください

<!--Image references-->

  [方法: DocumentDB アカウントを作成する]: #Howto
  [次のステップ]: #NextSteps
  [Azure 管理プレビュー ポータル]: https://portal.azure.com/
  [0]: ./media/documentdb-create-account/ca1.png
  [1]: ./media/documentdb-create-account/ca2.png
  [2]: ./media/documentdb-create-account/ca3.png
  [DocumentDB の容量とパフォーマンスの管理]: ../documentdb-manage/
  [リソース グループを使用した Azure リソースの管理]: http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups/
  [3]: ./media/documentdb-create-account/ca4.png
  [4]: ./media/documentdb-create-account/ca5.png
  [5]: ./media/documentdb-create-account/ca6.png
  [6]: ./media/documentdb-create-account/ca7.png
  [7]: ./media/documentdb-create-account/ca8.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
