<properties title="Monitor a DocumentDB Account" pageTitle="Manage a DocumentDB account | Azure" description="Learn how to manage your DocumentDB account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/03/2014" ms.author="hawong"></tags>

# DocumentDB アカウントの管理方法

## 目次

-   [方法: DocumentDB アクセス キーを表示、コピー、および再生成する](#keys)
-   [方法: DocumentDB 整合性の設定を管理する](#consistency)
-   [方法: DocumentDB 容量の設定を管理する](#capacity)
-   [方法: DocumentDB アカウントを削除する](#delete)
-   [次のステップ](#next)

## <span id="keys"></span></a>方法: アクセス キーを表示、コピー、および再生成する

DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが
生成され、DocumentDB アカウントにアクセスする際の認証に使用できます
。DocumentDB により 2 つのアクセス キーが提供されるので
、DocumentDB アカウントを中断することなくキーを
再生成できます。

[Azure 管理プレビュー ポータル](https://portal.azure.com/)[](http://manage.windowsazure.com)で、
[DocumentDB アカウント] ブレードの **[キー]** パーツにアクセスし、
[DocumentDB アカウント] にアクセスするときに使用するアクセス キーの表示、コピー、
再生成を行います。

![](./media/documentdb-manage-account/image002.jpg)

### アクセス キーを表示、およびコピーする

1.   [Azure 管理プレビュー ポータル](https://portal.azure.com/)で、[DocumentDB アカウント] にアクセスします。

2.   [概要] レンズで、**[キー]** をクリックします。

3.   [キー] ブレードで、コピー対象のキーの右側にある **[コピー]** ボタンを
クリックします。

4.   Ctrl+C を押してキーをコピーします。

  ![](./media/documentdb-manage-account/image004.jpg)

### アクセス キーを再生成する

接続のセキュリティを高めるために、DocumentDB アカウント
のアクセス キーは定期的に変更する必要があります。片方のアクセス キーで
DocumentDB アカウントに接続したまま、もう片方のアクセス キーを
再生成できるように、アクセス キーは 2 つ割り当てられます。

**警告**

アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが
影響を受けます。アクセス キーを使用して DocumentDB
 アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。

DocumentDB アカウントを使用するアプリケーションまたはクラウド サービスがある場合、
キーを切り替えない限り、キーを再生成すると、接続が
失われます。手順の 1 つを以下に示します。

1. DocumentDB アカウントのセカンダリ アクセス キーを参照するように
アプリケーション コードのアクセス キーを更新します。

2.   DocumentDB アカウントのプライマリ アクセス キーを再生成します。
[Azure 管理プレビュー ポータル](https://portal.azure.com/)で、
[DocumentDB アカウント] にアクセスします。

3.   [概要] レンズで、**[キー]** をクリックします。

4.   [キー] ブレードで **[プライマリの再生成]** コマンドをクリックしてから
 **[Ok]** をクリックして、新しいキーを生成することを確認します。

5.   (再生成してから約 5 分後に) 新しいキーが使用できることを
確認してから、アプリケーション コードのアクセス キーを
更新し、新しいプライマリ アクセス キーを参照するようにします。

6.   セカンダリ アクセス キーを再生成します。

*新しく生成されたキーを使用して DocumentDB アカウントに
アクセスできるようになるには数分かかる場合があることに注意してください。*

## <span id="consistency"></span></a>方法: DocumentDB 整合性の設定を管理する

DocumentDB では、確かな見通しに基づいて整合性、可用性、待機時間の
トレードオフを開発者が検討できるようにするため、ユーザーが構成可能な十分に定義された
4 つの整合性レベルがサポートされています。

·         **Strong** 整合性では、読み取り操作により、必ず最後に書き込まれた値が
戻されることが保証されます。

·         **Bounded Staleness** 整合性は、読み取る値が古くなりすぎていない
ことを保証します。特に、読み取る値が、最後に書き込まれたバージョンよりも
K バージョン以上古くないことを保証します。

·         **Session** 整合性は、モノトニックな読み取り (古いデータ、
次に新しいデータ、再び古いデータといった順に読み取ることは決してない)、
モノトニックな書き込み (順序どおりに書き込まれる)、
さらにはどのクライアントの観点からも最新の書き込みを読み取ることを保証します。

·         **Eventual** 整合性は、読み取り操作によって有効な書き込みの一部が
必ず読み取られ、最終的にはすべてが読み取られることを保証します。

*既定では、DocumentDB アカウントは Session
 レベルの整合性でプロビジョニングされることに注意してください。DocumentDB 整合性
設定について詳しくは、[整合性レベル](http://go.microsoft.com/fwlink/p/?LinkId=402365)のセクションを参照してください。*

### DocumentDB アカウントの既定の整合性を指定するには

1.   [Azure 管理プレビュー
ポータル](https://portal.azure.com/)で、[DocumentDB アカウント] にアクセスします。

2.   [構成] レンズで、**[整合性]** をクリックします。

3.   [既定の整合性] ブレードで、DocumentDB アカウント用の既定の
整合性レベルを選択します。

4.   **[保存]** をクリックします。

5.   Azure 管理プレビュー ポータルの [通知] ハブで、
操作の進行状況を監視できます。

 ![](./media/documentdb-manage-account/image005.png)

 ![](./media/documentdb-manage-account/image006.png)

*DocumentDB アカウントで、既定の整合性設定に加えた変更内容が
有効になるには数分かかる場合があることに注意してください。*

## <span id="capacity"></span></a>方法: DocumentDB 容量の設定を管理する

Microsoft Azure DocumentDB を使用すると、ご使用のアプリケーションのライフサイクルに
おける需要の変化に応じて規模を柔軟に拡張させることができます。
DocumentDB の拡張は、Azure 管理プレビュー ポータルで
ご使用の DocumentDB データベース アカウントの容量を増やすことによって行えます。

データベース アカウントを作成すると、データベース ストレージと
予約済みのスループットがプロビジョニングされます。アカウントに対してプロビジョニングされた
データベースとスループットはいつでも変更できます。
その場合には、Azure 管理プレビュー ポータルで容量ユニットを追加したり削除したりします。

### 容量ユニットを追加または削除するには

1.   [Azure 管理プレビュー
ポータル](https://portal.azure.com/)で、[DocumentDB アカウント] にアクセスします。

2.   [使用] レンズで、**[スケール]** をクリックします。

3.   [スケール] ブレードで、ご使用の DocumentDB アカウントの
容量ユニット数を指定します。

4.   **[保存]** をクリックします (スケール拡張操作が
完了するまでには数分かかる場合があることに注意してください。
Azure 管理プレビュー ポータルの [通知] ハブで進行状況を監視できます)。

*DocumentDB プレビュー版では、1 つの DocumentDB アカウントに
対して最大 5 個の容量ユニットまでサポートしています。*

![](./media/documentdb-manage-account/image007.png)

 

## <span id="delete"></span></a>方法: DocumentDB アカウントを削除する

使用しなくなった DocumentDB アカウントを削除するには、[DocumentDB アカウント] ブレードの
**[削除]** コマンドを使用します。

**警告**

*プレビュー リリース版では、削除された DocumentDB アカウントの
コンテンツを復元する方法はありません。DocumentDB アカウントを削除すると、
データベース、コレクション、ドキュメント、
および添付ファイルなど、対象アカウントのすべてのリソースが削除されます。*

![](./media/documentdb-manage-account/image009.png)

1.   [Azure 管理プレビュー
ポータル](https://portal.azure.com/)で、削除する [DocumentDB アカウント] に
アクセスします。

2.   [アカウント] ブレードで、**[削除]** コマンドをクリックします。

3.   [結果の確認] ブレードで、アカウントを削除することを確認するため
DocumentDB アカウント名を入力します。

4.   確認ブレードで **[削除]** ボタンをクリックします。

## <span id="next"></span></a>次のステップ

-   [DocumentDB アカウントを使用して開始
    する方法について取り上げます。](http://go.microsoft.com/fwlink/p/?LinkId=402364)
-   DocumentDB について詳しくは、
    [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409) の
     Azure DocumentDB 資料を参照してください。

 

  [方法: DocumentDB アクセス キーを表示、コピー、および再生成する]: #keys
  [方法: DocumentDB 整合性の設定を管理する]: #consistency
  [方法: DocumentDB 容量の設定を管理する]: #capacity
  [方法: DocumentDB アカウントを削除する]: #delete
  [次のステップ]: #next
  []: http://manage.windowsazure.com
  []: ./media/documentdb-manage-account/image002.jpg
  [1]: ./media/documentdb-manage-account/image004.jpg
  [2]: ./media/documentdb-manage-account/image005.png
  [3]: ./media/documentdb-manage-account/image006.png
  [4]: ./media/documentdb-manage-account/image007.png
  [5]: ./media/documentdb-manage-account/image009.png
  [azure.com]: http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409
