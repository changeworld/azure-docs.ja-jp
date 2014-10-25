<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev"></tags>

# DocumentDB アカウントを監視する

DocumentDB アカウントは、[Azure プレビュー ポータル][Azure プレビュー ポータル]で監視できます。各 DocumentDB アカウントに対して、パフォーマンス メトリック (要求やサーバー エラーなど) と使用状況メトリック (ストレージ消費など) が利用可能です。

## この記事の内容

-   [方法: DocumentDB アカウントのパフォーマンス メトリックを表示する][方法: DocumentDB アカウントのパフォーマンス メトリックを表示する]
-   [方法: DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする][方法: DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする]
-   [方法: サイド バイ サイドのパフォーマンス メトリック チャートを作成する][方法: サイド バイ サイドのパフォーマンス メトリック チャートを作成する]
-   [方法: DocumentDB アカウントの使用状況メトリックを表示する][方法: DocumentDB アカウントの使用状況メトリックを表示する]
-   [方法: DocumentDB アカウントのパフォーマンス メトリック アラートを設定する][方法: DocumentDB アカウントのパフォーマンス メトリック アラートを設定する]
-   [次のステップ][次のステップ]

## <span id="metrics"></span></a>方法: DocumentDB アカウントのパフォーマンス メトリックを表示する

1.  [Azure プレビュー ポータル][Azure プレビュー ポータル]で、**[参照]** をクリックし、**[DocumentDB アカウント]** をクリックして、パフォーマンス メトリックを表示する DocumentDB アカウントの名前をクリックします。
2.  **[監視]** レンズ内で、既定により以下を表示できます。

    -   当日の要求数合計
    -   当日の 1 秒あたりの要求数平均

    ![][]

3.  **[要求数合計または 1 秒あたりの要求数平均]** パーツをクリックすると、詳細な **[メトリック]** ブレードが表示されます。
4.  [メトリック] ブレードには、選択したメトリックの詳細が表示されます。ブレードの上部にはグラフがあり、下部にある表では、平均、最小値、最大値など、選択したメトリックの集約値が表示されます。[メトリック] ブレードには、現在のメトリック ブレードに表示されるメトリックに対してフィルター処理された定義済みアラートの一覧も表示されます (これにより、さまざまなアラートがある場合、ここで示された関連するアラートのみが表示されます)。

    ![][1]

## <span id="custom"></span></a>方法: DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする

1.  特定のパーツに表示されるメトリックをカスタマイズするには、メトリック チャートを右クリックし、**[グラフの​​編集]** をクリックします。
    ![][2]

2.  **[グラフの編集]** ブレードには、パーツに表示されるメトリックおよび期間を変更するオプションがあります。
    ![][3]

3.  パーツに表示されるメトリックを変更するには、利用可能なパフォーマンス メトリックをオンまたはオフにして、ブレードの下部にある **[保存]** をクリックするだけです。
4.  期間を変更するには、異なる範囲を選択し (たとえば、**[過去 1 時間]**)、ブレードの下部にある **[保存]** をクリックします。  
    ![][4]

5.  カスタムの期間では、過去 2 週間の任意の期間を選択できます。
6.  **[保存]** をクリックすると、[DocumentDB アカウント] ブレードを閉じるまでは変更内容が維持されます。後でもう一度このブレードを表示すると、元のメトリックと期間が表示されます。

## <span id="create"></span></a>方法: サイド バイ サイドのグラフを作成する

Azure プレビュー ポータルでは、サイド バイ サイドのメトリック チャートを作成できます。

1.  最初に、出発点として使用するチャートを右クリックし、**[カスタマイズ]** をクリックします。
    ![][5]

2.  メニューの **[複製]** をクリックしてパーツをコピーします。

    ![][6]

3.  最後に、画面の上部にあるツール バーの **[完了]** をクリックします。これで、このパーツを他のメトリック パーツとして扱って、パーツに表示されるメトリックと期間をカスタマイズすることができます。これにより、2 種類の異なるメトリック チャートを横に並べて同時に見ることができます。  
    ![][7]

> チャートの期間と選択したメトリックは、Azure プレビュー ポータルを閉じたときにパーツの既定値にリセットされる点に注意してください。

## <span id="view"></span></a>方法: DocumentDB アカウントの使用状況メトリックを表示する

1.  [Azure プレビュー ポータル][Azure プレビュー ポータル]で、**[参照]** をクリックし、**[DocumentDB アカウント]** をクリックして、使用率メトリックを表示する DocumentDB アカウントの名前をクリックします。
2.  **[使用状況]** レンズ内で、既定で以下を表示できます。
	*	アカウント内で消費されるストレージ
	*	アカウントの利用可能なストレージの最大値
	*	添付ファイルの使用状況
	*	ユーザーとアクセス許可の使用状況
	*	容量単位の割り当て
	*	アカウント内の添付ファイルの使用状況  
        ![][8]

## <span id="setup"></span></a>方法: DocumentDB アカウントのパフォーマンス メトリック アラートを設定する

1.  [Azure プレビュー ポータル][Azure プレビュー ポータル]で、**[参照]** をクリックし、**[DocumentDB アカウント]** をクリックして、パフォーマンス メトリック アラートを表示する DocumentDB アカウントの名前をクリックします。
2.  **[操作]** レンズ内で、**[アラート ルール]** パーツをクリックします。  
    ![][9]

3.  [アラート ルール] ブレードで、**[アラートの追加]** をクリックします。  
    ![][10]

4.  **[アラート ルールの追加]** ブレードで、以下を指定します。

    -   設定するアラート ルールの名前。
    -   新しいアラート ルールの説明。
    -   アラート ルールのメトリック。
    -   いつアラートをアクティブにするかを決定する条件、しきい値、および期間。たとえば、過去 15 分間にサーバー エラー回数が 5 を超える場合を指定できます。
    -   サービス管理者および共同管理者は、アラートが生成されると電子メールを受け取ります。
    -   アラート通知用の追加の電子メール アドレス。  
        ![][11]

## <span id="next"></span></a>次のステップ

-   DocumentDB の詳細については、[azure.com][azure.com] で Azure DocumentDB に関するドキュメントを参照してください

<!--Anchors-->

  [Azure プレビュー ポータル]: https://portal.azure.com/
  [方法: DocumentDB アカウントのパフォーマンス メトリックを表示する]: #metrics
  [方法: DocumentDB アカウントのパフォーマンス メトリック ビューをカスタマイズする]: #custom
  [方法: サイド バイ サイドのパフォーマンス メトリック チャートを作成する]: #create
  [方法: DocumentDB アカウントの使用状況メトリックを表示する]: #view
  [方法: DocumentDB アカウントのパフォーマンス メトリック アラートを設定する]: #setup
  [次のステップ]: #next
  []: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
