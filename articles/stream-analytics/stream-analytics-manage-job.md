---
title: 'チュートリアル: Azure Portal を使用した Stream Analytics ジョブの作成および管理 | Microsoft Docs'
description: このチュートリアルでは、Azure Stream Analytics を使用して通話ストリームにおける不正な呼び出しを分析する方法について詳しく説明します。
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 39b39a1d00c91e0ff114a28c13da0d4b6920ec13
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186232"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>通話データを分析して結果を Power BI ダッシュボードで視覚化する Stream Analytics ジョブの作成
 
このチュートリアルでは、Azure Stream Analytics を使用して、クライアント アプリケーションが生成するサンプル通話を分析する方法を紹介します。 クライアント アプリケーションが生成する通話データには不正な呼び出しが含まれているため、そのような呼び出しをフィルター処理するよう Stream Analytics ジョブを定義します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * サンプル通話データを生成して Azure Event Hubs に送信する  
> * Stream Analytics のジョブの作成   
> * ジョブへの入力と出力を構成する  
> * 不正な呼び出しをフィルター処理するようクエリを定義する  
> * ジョブをテストして開始する  
> * Power BI で結果を視覚化する 

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。  
* [Azure Portal](https://portal.azure.com/) にログインします。  
* Microsoft ダウンロード センターから通話イベント ジェネレーター アプリ [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) をダウンロードします。または、[GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) からソース コードを入手することもできます。  

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します 

Stream Analytics でデータ ストリームにおける不正な呼び出しを分析できるようにするには、データを Azure に送信する必要があります。 このチュートリアルでは、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs) を使用して Azure にデータを送信します。 このチュートリアルでは、イベント ハブを作成し、イベント ジェネレーター アプリからそのイベント ハブに通話データを送信します。 イベント ハブを作成するには、次の手順を実行します。

1. Azure ポータルにログインします。  
2. **[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Event Hubs]** の順に選択します。  

   ![イベント ハブの検索](media/stream-analytics-manage-job/find-eh.png)
3. **[名前空間の作成]** ウィンドウに次の値を入力します。  

   |**設定**  |**推奨値** |**説明**  |
   |---------|---------|---------|
   |Name     | myEventHubNS        |  イベント ハブの名前空間を識別する一意の名前。       |
   |サブスクリプション     |   \<該当するサブスクリプション\>      |   イベント ハブを作成する Azure サブスクリプションを選択します。      |
   |リソース グループ     |   MyASADemoRG      |  **[新規作成]** を選択し、アカウントの新しいリソース グループ名を入力します。       |
   |Location     |   米国西部 2      |    イベント ハブの名前空間をデプロイできる場所です。     |

4. 残りの設定では既定のオプションを使用し、**[作成]** を選択します。  

   ![イベント ハブの名前空間の作成](media/stream-analytics-manage-job/create-ehns.png)

5. 名前空間のデプロイが完了したら、**[すべてのリソース]** に移動し、Azure リソースの一覧で "myEventHubNS" を探して、それを選択して開きます。  
6. 次に、**[+ イベント ハブ]** >  **を選択し、[名前]** に「MyEventHub」と入力します。 別の名前を使用してもかまいません。 残りの設定では既定のオプションを使用し、**[作成]** を選択して、デプロイが正常に完了するまで待ちます。

   ![イベント ハブの作成](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>イベント ハブへのアクセスを許可し、接続文字列を取得する

アプリケーションから Azure Event Hubs にデータを送信できるようにするには、適切なアクセスを許可するポリシーがイベント ハブに必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1. 前の手順で作成した**イベント ハブ** (MyEventHub) に移動し、イベント ハブ ウィンドウから **[共有アクセス ポリシー]** を選択して、**[+ 追加]** を選択します。  
2. ポリシー名を「**Mypolicy**」に設定し、**[管理]**、**[作成]** の順に選択します。  

   ![イベント ハブの共有アクセス ポリシーの作成](media/stream-analytics-manage-job/create-ehpolicy.png)

3. ポリシーのデプロイが完了したら、ポリシーを選択して開き、**[接続文字列 - 主キー]** を探して、接続文字列の横にある **[コピー]** を選択します。  
4. 接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、次のセクションで必要になります。  

   接続文字列は次のようになります。

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   接続文字列には、Endpoint、SharedAccessKeyName、SharedAccessKey、EntityPath という複数のキーと値のペアが含まれており、セミコロンで区切られています。  

5. 接続文字列から EntityPath のペアを削除します (その前にあるセミコロンも忘れずに削除してください)。

## <a name="start-the-event-generator-application"></a>イベント ジェネレーター アプリケーションを起動する

TelcoGenerator アプリを起動する前に、以前に作成した Azure Event Hubs にデータを送信するよう構成する必要があります。

1. [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) ファイルの内容を抽出します。  
2. 任意のテキスト エディターで `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` ファイルを開きます (複数の .config ファイルがあるので、正しいファイルを開くように注意してください)。  

3. 構成ファイル内の <appSettings> 要素を次の詳細で更新します。

   * EventHubName キーの値を、接続文字列の EntityPath の値に設定します。  
   * Microsoft.ServiceBus.ConnectionString キーの値を、EntityPath 値を除いた接続文字列 (前のセクションの手順 5 で取得した値) に設定します。

4. ファイルを保存します。  
5. 次に、コマンド ウィンドウを開き、TelcoGenerator アプリケーションを解凍したフォルダーに変更し、次のコマンドを入力します。

   ```
   telcodatagen.exe 1000 .2 2
   ```

   このコマンドは、次のパラメーターを受け取ります。
   * **1 時間あたりの通話データ レコードの数**。  
   * **不正の確率のパーセンテージ** - アプリが不正な呼び出しをシミュレートする頻度。 値 .2 は、呼び出しレコードの約 20% が不正に見えることを意味します。  
   * **継続時間** - アプリを実行する時間数。 コマンド ラインでプロセスを終了する (Ctrl + C) ことで、いつでもアプリを停止できます。

   数秒後に、アプリはイベント ハブに送信する呼び出しレコードを画面に表示し始めます。 通話データには、次のフィールドが含まれています。

   |**レコード**  |**定義**  |
   |---------|---------|
   |CallrecTime    |  通話開始時刻のタイムスタンプ。       |
   |SwitchNum     |  通話の接続に使われた電話交換機。 この例では、交換機は発信国を表す文字列です (US、China、UK、Germany、Australia)。       |
   |CallingNum     |  発信元の電話番号。       |
   |CallingIMSI     |  IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 発信元の一意識別子。       |
   |CalledNum     |   通話受信者の電話番号。      |
   |CalledIMSI     |  IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 通話受信者の一意識別子。       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成 

通話イベントのストリームを準備できたら、イベント ハブからデータを読み取る Stream Analytics ジョブを作成できます。

1. Stream Analytics ジョブを作成するには、Azure Portal に移動します。  

2. **[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Stream Analytics ジョブ]** の順に選択します。  

3. **[新しい Stream Analytics ジョブ]** ウィンドウで、次の値を入力します。  

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |ジョブ名     |  ASATutorial       |   イベント ハブの名前空間を識別する一意の名前。      |
   |サブスクリプション    |  \<該当するサブスクリプション\>   |   ジョブを作成する Azure サブスクリプションを選択します。       |
   |リソース グループ   |   MyASADemoRG      |   **[既存のものを使用]** を選択し、アカウントの新しいリソース グループ名を入力します。      |
   |Location   |    米国西部 2     |      ジョブをデプロイできる場所。 最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。      |
   |ホスティング環境    | クラウド        |     Stream Analytics ジョブは、クラウドまたはエッジにデプロイすることができます。 クラウドでは Azure Cloud にデプロイすることができ、エッジでは IoT エッジ デバイスにデプロイすることができます。    |
   |[ストリーミング ユニット]     |    1       |      ストリーミング ユニットとは、ジョブの実行に必要なコンピューティング リソースのことです。 既定では、この値は 1 に設定されています。 ストリーミング ユニットのスケーリングについては、[ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md)に関する記事を参照してください。      |

   ![ジョブを作成する](media/stream-analytics-manage-job/create-a-job.png)   

4. 残りの設定では既定のオプションを使用し、**[作成]** を選択して、デプロイが成功するまで待ちます。

## <a name="configure-job-input"></a>ジョブの入力を構成する

次の手順では、データを読み取るためにジョブの入力ソースを定義します。 このチュートリアルでは、前のセクションで作成したイベント ハブを入力として使用します。 ジョブへの入力を構成するには、次の手順を実行します。

1. Azure Portal から **[すべてのリソース]** ウィンドウを開き、ASATutorial Stream Analytics ジョブを見つけます。  

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、**[入力]** オプションを選択します。  

3. **[+ ストリーム入力の追加]** (参照入力は静的な参照データを指しますが、このチュートリアルでは使用しません)、**[イベント ハブ]** の順に選択し、そのウィンドウに次の値を入力します。  

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |入力のエイリアス     |  CallStream       |  入力を識別するためのわかりやすい名前を入力します。 入力のエイリアスに含めることのできる文字は、英数字、ハイフン、アンダースコアのみであり、長さは 3 文字以上 63 文字以下でなければなりません。       |
   |サブスクリプション    |   \<該当するサブスクリプション\>      |   イベント ハブを作成した Azure サブスクリプションを選択します。 イベント ハブは、Stream Analytics ジョブと同じサブスクリプションにも別のサブスクリプションにも含めることができます。       |
   |イベント ハブの名前空間    |  MyEventHubNS       |  前のセクションで作成した、イベント ハブの名前空間を選択します。 現在のサブスクリプションで利用可能なイベント ハブの名前空間がすべて、ドロップダウンに表示されます。       |
   |イベント ハブ名    |   MyEventHub      |  前のセクションで作成したイベント ハブを選択します。 現在のサブスクリプションで利用可能なイベント ハブがすべて、ドロップダウンに表示されます。       |
   |イベント ハブ ポリシー名   |  Mypolicy       |  前のセクションで作成した、イベント ハブの共有アクセス ポリシーを選択します。 現在のサブスクリプションで利用可能なイベント ハブ ポリシーがすべて、ドロップダウンに表示されます。       |

   ![入力の構成](media/stream-analytics-manage-job/configure-input.png) 

4. 残りの設定では既定のオプションを使用し、**[保存]** を選択して、デプロイが正常に完了するまで待ちます。

## <a name="configure-job-output"></a>ジョブの出力を構成する 

最後の手順では、変換後のデータを書き込むことができる場所として、ジョブの出力シンクを定義します。 このチュートリアルでは、結果を Power BI に出力し、データを視覚化します。 ジョブへの出力を構成するには、次の手順を実行します。

1. Azure Portal から **[すべてのリソース]** ウィンドウを開き、ASATutorial Stream Analytics ジョブを見つけます。  

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、**[出力]** オプションを選択します。  

3. **[+ 追加]** > **[Power BI]** の順に選択し、フォームに次の詳細を入力して (表に示されているように、出力エイリアス、データセットの名前、テーブル名を識別するためのわかりやすい名前を入力してください)、**[承認]** を選択します。  

   |**設定**  |**推奨値**  |
   |---------|---------|---------|
   |出力エイリアス  |  MyPBIoutput  |
   |データセットの名前  |   ASAdataset  | 
   |テーブル名 |  ASATable  | 

   ![出力の構成](media/stream-analytics-manage-job/configure-output.png)  

4. **[承認]** を選択すると、ポップアップ ウィンドウが開き、Power BI アカウントに対する認証のための資格情報を入力するよう求められます。 認可が成功したら、設定を**保存**します。 

## <a name="define-a-query-to-analyze-input-data"></a>入力データを分析するようクエリを定義する

着信データ ストリームを読み取るように Stream Analytics ジョブを設定したら、次の手順として、データをリアルタイムで分析する変換を作成します。 変換クエリの定義には、[Stream Analytics クエリ言語](https://msdn.microsoft.com/library/dn834998.aspx)を使用します。 このチュートリアルでは、通話データから不正な呼び出しを検出するクエリを定義します。 

この例では、不正な呼び出しとは同一ユーザーが 5 秒おきに別々の場所から発信する呼び出しであると考えます。 たとえば、合法的に同じユーザーが米国とオーストラリアで同時に呼び出しを行うことはできません。 Stream Analytics ジョブの変換クエリを定義するには、次の手順を実行します。

1. Azure Portal から **[すべてのリソース]** ウィンドウを開き、前に作成した **ASATutorial** Stream Analytics ジョブを開きます。  

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、**[クエリ]** オプションを選択します。 ポップアップ ウィンドウでは、そのジョブ用に構成されている入力と出力が一覧表示されており、入力ストリームを変換するためのクエリを作成できます。  

3. 次に、エディターの既存のクエリを次のデータに置き換えます。このクエリでは、5 秒間分の通話データに対して自己結合を実行します。

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   不正な呼び出しを確認するには、`CallRecTime` の値に基づいてストリーミング データを自己結合する必要があります。 その後、`CallingIMSI` の値 (発信番号) は同じなのに `SwitchNum` の値 (発信国) が異なる通話レコードを探すことができます。 ストリーミング データで JOIN 操作を使用する際は、一致する行と見なす最大時間差を結合で制限する必要があります。 ストリーミング データは無限であるため、リレーションシップの時間限界は、結合の ON 句内で [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) 関数を使用して指定します。 

   このクエリは、DATEDIFF 関数を除けば、通常の SQL 結合と似ています。 このクエリで使用されている DATEDIFF 関数は、Streaming Analytics 固有の関数であり、`ON...BETWEEN` 句内で使用する必要があります。  

4. クエリを**保存**します。  

   ![クエリの定義](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>クエリをテストする

クエリは、クエリ エディターからテストできます。クエリをテストするには、サンプル データが必要です。 このチュートリアルでは、イベント ハブに送られてくる通話ストリームからサンプル データを抽出します。 クエリをテストするには、次の手順を実行します。

1. TelcoGenerator アプリが実行されていて、通話レコードを生成していることを確認します。  

2. **[クエリ]** ウィンドウで、CallStream 入力の横にある点を選択し、**[入力からのサンプル データ]** を選択します。 これにより、入力ストリームから読み取るサンプル データの量を指定できるウィンドウが開きます。  

   ![サンプルの入力データ](media/stream-analytics-manage-job/sample-input-data.png)  

3. **[分]** を 3 に設定し、**[OK]** を選択します。 3 分間分のデータが入力ストリームからサンプリングされ、サンプル データの準備ができると通知されます。 通知バーからサンプリングの状態を確認できます。 

   サンプル データは一時的に保存され、クエリ ウィンドウを開いている間使用できます。 クエリ ウィンドウを閉じると、サンプル データは破棄されるので、サンプル データの新しいセットを作成する必要があります。 代わりの方法として、サンプル データが含まれている .json ファイルを [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/telco.json) から入手し、その .json ファイルをアップロードして CallStream 入力のサンプル データとして使用することもできます。  

4. **[テスト]** を選択してクエリをテストすると、次のスクリーンショットに示すように出力結果が表示されます。  

   ![テスト出力](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>ジョブを開始して出力を視覚化する

1. ジョブを開始するには、ジョブの **[概要]** ウィンドウに移動し、**[開始]** を選択します。  

2. ジョブ出力の開始時刻に **[現在]** を選択し、**[開始]** を選択します。 ジョブは数分で開始され、通知バーで状態を確認できます。  

3. ジョブが成功したら、[Powerbi.com](https://powerbi.com/) に移動して、職場または学校アカウントでサインインします。 Stream Analytics ジョブ クエリで結果が出力されると、データセットが既に作成されていることがわかります。 **[データセット]** タブに移動すると、"ASAdataset" という名前のデータセットを確認できます。  

4. ワークスペースから、**[+ 作成]** を選択します。 新しいダッシュボードを作成し、「Fraudulent Calls」という名前を付けます。 このダッシュボードに 2 つのタイルを追加します。1 つのタイルは、特定のインスタンスでの不正な呼び出しの回数を表示するために使用され、もう 1 つのタイルには、折れ線グラフが表示されます。  

5. ウィンドウの上部で、**[タイルの追加]** を選択し、**[カスタム ストリーミング データ]**、[次へ]、**[ASAdataset]** の順に選択します。[視覚化タイプ] では、**[カード]**、**fraudulentcalls** フィールドを選択します。 **[次へ]** を選択し、このタイルの名前を入力して、**[適用]** を選択します。  

   ![タイルの作成](media/stream-analytics-manage-job/create-tiles.png)

6. 次のオプションを使用して、もう一度手順 4 を実行します。
   * [視覚化タイプ] では、[折れ線グラフ] を選択します。  
   * 軸を追加し、**[windowend]** を選びます。  
   * 値を追加し、**[fraudulentcalls]** を選びます。  
   * **[表示する時間枠]** で、過去 10 分間を選びます。  

7. 2 つのタイルが追加されると、ダッシュボードは次のスクリーンショットのように表示されます。 イベント ハブの送信側アプリケーションと Streaming Analytics アプリケーションが実行されている場合、PowerBI ダッシュボードは新しいデータが到着するときに定期的に更新されることがわかります。  

   ![Power BI の結果](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Web アプリケーションに PowerBI ダッシュボードを埋め込む

チュートリアルのこの部分では、PowerBI チームが作成したサンプルの [ASP.NET](http://asp.net/) Web アプリケーションを使用して、ダッシュボードを埋め込みます。 ダッシュボードの埋め込みの詳細については、記事「[Power BI で埋め込み](https://docs.microsoft.com/power-bi/developer/embedding)」を参照してください。

このチュートリアルでは、ユーザーが所有するデータ アプリケーション用の手順に従います。 アプリケーションを設定するには、[PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub リポジトリに移動し、**User Owns Data** セクションの指示に従います (**integrate-dashboard-web-app** サブセクションのリダイレクト URL およびホーム ページ URL を使用します)。 ダッシュボードの例を使用しているため、[GitHub リポジトリ](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User Owns Data/integrate-dashboard-web-app)にある integrate-dashboard-web-app サンプル コードを使用します。
ブラウザーでアプリケーションが実行されるようになったら、次の手順に従って、先ほど作成したダッシュボードを Web ページに埋め込みます。

1. **[Power BI にサインイン]** を選択します。これにより、PowerBI アカウントのダッシュボードへのアクセスがアプリケーションに許可されます。  

2. **[ダッシュボードの取得]** ボタンを選択します。これにより、アカウントのダッシュボードがテーブルに表示されます。 前に作成したダッシュボードの名前 (powerbi-embedded-dashboard) を探し、対応する **EmbedUrl** をコピーします。  

3. 最後に、その **EmbedUrl** を対応するテキスト フィールドに貼り付け、**[Embed Dashboard]\(ダッシュボードの埋め込み\)** を選択します。 これで、Web アプリケーション内に埋め込まれた同じダッシュボードを表示できるようになりました。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、簡単な Stream Analytics ジョブを作成し、着信データを分析して、結果を Power BI ダッシュボードに表示しました。 Stream Analytics ジョブの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Stream Analytics ジョブ内から Azure Functions を実行する](stream-analytics-with-azure-functions.md)
