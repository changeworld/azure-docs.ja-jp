<properties 
	pageTitle="Application Insights からのテレメトリの連続エクスポート" 
	description="診断および利用状況データを Microsoft Azure のストレージにエクスポートし、そこからダウンロードします。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="awills"/>
 
# Application Insights からのテレメトリのエクスポート

テレメトリに対してカスタマイズした分析を行う必要がありますか。 それとも、特定のプロパティを持つイベントに対して電子メール アラートを送信する必要がありますか。 そのようなケースには、連続エクスポートが最適です。Application Insights ポータルに表示されるイベントは、JSON 形式で Microsoft Azure のストレージにエクスポートできます。そこからデータをダウンロードしたり、データを処理するためのコードを自由に記述したりできます。


## <a name="setup"></a>連続エクスポートの設定

Application Insights ポータルのアプリケーションの概要ブレードで、\[連続エクスポート\] を開きます。

![下へスクロールし、\[連続エクスポート\] をクリックします](./media/app-insights-export-telemetry/01-export.png)

エクスポートを追加し、データを格納する [Azure ストレージ アカウント](storage-introduction.md)を選択します。

![\[追加\]、\[エクスポート先\]、\[ストレージ アカウント\] の順にクリックし、新しいストアを作成するかまたは既存のストアを使用するかを選択します。](./media/app-insights-export-telemetry/02-add.png)

エクスポートするイベントの種類を選択します。

![\[イベントの種類の選択\] をクリックします](./media/app-insights-export-telemetry/03-types.png)


エクスポートが作成されると、処理が開始されます \(エクスポートを作成した後に到着したデータのみが取得されます\)。


後でイベントの種類を変更する場合は、単にエクスポートを編集します。

![\[イベントの種類の選択\] をクリックします](./media/app-insights-export-telemetry/05-edit.png)

ストリームを停止するには、\[無効\] をクリックします。もう一度 \[有効\] をクリックすると、新しいデータでストリームが再開されます。エクスポートが無効な場合、ポータルに到着したデータは取得されません。

ストリームを完全に停止するには、エクスポートを削除します。エクスポートを削除しても、ストレージのデータは削除されません。
#### エクスポートを追加または変更できない

* エクスポートを追加または変更するには、所有者、共同作成者、または Application Insights 共同作成者のアクセス権が必要になります。[ロールの詳細についてはこちら][roles]。

## <a name="analyze"></a>取得されるイベント

エクスポートされるデータは、アプリケーションから受信した未加工のテレメトリです。ただし、次の例外があります。

* 現在、Web テストの結果は含まれていません。 
* クライアントの IP アドレスから計算された位置データが追加されます。  

計算メトリックは含まれません。たとえば、平均 CPU 使用率はエクスポートされませんが、平均の計算に使用された未加工のテレメトリはエクスポートされます。

## <a name="get"></a>入手方法

[サーバー エクスプローラー](http://msdn.microsoft.com/library/azure/ff683677.aspx)などのツールを使用して Blob ストアを開くと、Blob ファイルのセットを含むコンテナーが表示されます。各ファイルの URI は、"アプリケーション ID/テレメトリの種類/日付/時刻" です。

![適切なツールで Blob ストアを調べます](./media/app-insights-export-telemetry/04-data.png)

日付と時刻は UTC 形式で表され、テレメトリが生成された時間ではなく、ストアに格納された日時を示します。そのため、データをダウンロードするコードを記述する場合は、直線的にデータ内を移動できます。

プログラムでこのデータをダウンロードするには、[Blob ストア REST API](storage-dotnet-how-to-use-blobs.md#configure-access) または [Azure PowerShell コマンドレット](http://msdn.microsoft.com/library/azure/dn806401.aspx)を使用します。

または、パイプラインを設定して大規模にデータを管理できる [DataFactory](http://azure.microsoft.com/services/data-factory/) を検討してください。

ここでは、\(イベントを受け取った場合に\) 1 時間ごとに新しい Blob を作成します。したがって、必ず前の 1 時間分の処理が必要になりますが、現在の 1 時間が経過するのを待つ必要があります。

[サンプル コード][exportcode]


## <a name="format"></a>データの内容

* それぞれの Blob は、"\\n" で区切られた複数の行を含むテキスト ファイルです。
* それぞれの行は、書式設定されていない JSON ドキュメントです。内容を確認する場合は、Notepad++ のようなビューアーに JSON プラグインを組み合わせることができます。

![適切なツールでテレメトリを表示します](./media/app-insights-export-telemetry/06-json.png)

時間の長さはティック単位で表記されます。10,000 ティックが 1 ミリ秒です。たとえば、これらの値は、ブラウザーから要求を送信するのに 10 ミリ秒、要求を受信するのに 30 ミリ秒、ブラウザーでページを処理するのに 1.8 秒の時間がかかったことを示しています。

	"sendRequest": {"value": 10000.0},
	"receiveRequest": {"value": 30000.0},
	"clientProcess": {"value": 17970000.0}



## 処理方法

小規模な処理では、データを分解してスプレッドシートに読み込んだ後で他の処理を実行するコードを記述できます。次に例を示します。

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }


SQL データベースにデータを移動することもできます。[サンプル コード][exportcode]を参照してください。

大規模な処理の場合は、[HDInsight](http://azure.microsoft.com/services/hdinsight/) \(クラウドの Hadoop クラスター\) を検討してください。HDInsight は、ビッグ データを管理および分析するためのさまざまなテクノロジを提供します。

## <a name="delete"></a>古いデータの削除
必要に応じて古いデータを削除するなどしてストレージ容量を管理する責任があることに注意してください。

## ストレージ キーを再生成する場合

ストレージのキーを変更した場合、連続エクスポートは動作しなくなります。Azure アカウントに通知が表示されます。

\[連続エクスポート\] ブレードを開き、エクスポートを編集します。エクスポート先の編集画面では、同じストレージを選択したままにします。\[OK\] をクリックして確定します。

![連続エクスポートを編集し、3 つのエクスポート先を開いた後閉じます。](./media/app-insights-export-telemetry/07-resetstore.png)

連続エクスポートが再開されます。


## サンプル コード

[エクスポートされたデータを SQL データベースに移動する][exportcode]

## Q & A

* *グラフを 1 回だけダウンロードしたいのですが。*  
 
    この操作については別個に対応します。

* *エクスポートを設定したのにストアにデータがありません。*

    エクスポートを設定した時点以降に Application Insights がアプリからテレメトリを受信していますか。 取得されるのは新しいデータのみです。

* *エクスポートを設定しようとしたところ、アクセスが拒否されました。*

    アカウントが組織によって所有されている場合は、所有者または共同作成者グループのメンバーである必要があります。

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *自分のオンプレミスのストアに直接エクスポートできますか。*

    いいえ、できません。エクスポート エンジンによるデータのプッシュ配信先は、特定の対象に制限されています。

* *ストアに格納できるデータの量に制限はありますか。*

    いいえ。データのプッシュ配信は、エクスポートが削除されるまで続行されます。Blob ストレージの制限に達した場合は配信が停止されますが、その制限には非常に大きな値が設定されています。使用するストレージの量を管理するのはお客様です。

* *ストレージのキーを再生成した後、またはコンテナーの名前を変更した後、エクスポートが動作しません。*

    エクスポートを編集し、\[エクスポート先\] ブレードを開きます。以前と同じストレージが選択されていることを確認し、\[OK\] をクリックして確定します。エクスポートが再開されます。変更を加えたのが数日前のことであれば、データは失われません。

* *エクスポートを一時停止できますか。*

    はい。\[無効\] をクリックします。


<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[roles]: app-insights-resources-roles-access-control.md


<!--HONumber=54-->