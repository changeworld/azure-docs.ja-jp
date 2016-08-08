<properties
	pageTitle="Azure Batch プール内のコンピューティング ノードの自動スケール | Microsoft Azure"
	description="クラウド プールで自動スケールを有効にして、プール内のコンピューティング ノードの数を動的に調整します。"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/21/2016"
	ms.author="marsma"/>

# Azure Batch プール内のコンピューティング ノードの自動スケール

自動スケールを使用すると、Azure Batch サービスによって、定義したパラメーターに基づいてプール内の計算ノードが動的に追加または削除されます。アプリケーションによって使用される計算処理能力を自動的に調整 (ジョブのタスクの需要が大きくなったときにはノードを追加し、小さくなったときは除外) することで、時間とコストを削減できる可能性があります。

自動スケールをコンピューティング ノードのプールに対して有効にするには、定義した*自動スケールの数式* ([Batch .NET](batch-dotnet-get-started.md) ライブラリ内の [PoolOperations.EnableAutoScale][net_enableautoscale] メソッドの数式など) をプールに関連付けます。Batch サービスでは、この数式を使用して、ワークロードを実行するために必要なコンピューティング ノードの数を決定します。Batch は定期的に収集されるサービスのメトリック データのサンプルに従い、プール内のコンピューティング ノードの数が、関連付けられている数式に基づいて構成可能な間隔で調整されます。

自動スケールは、プールの作成時のほか、既存のプールに対して有効化することができます。また、"自動スケール" が有効なプールに対し、既存の数式を変更することもできます。Batch では、数式をプールに割り当てる前に数式の評価を行うことができるほか、自動スケールの実行状態を監視することができます。

## 自動スケールの数式

自動スケールの数式は、1 つまたは複数のステートメントを含む、ユーザーが定義する文字列値であり、この文字列値は、プールの [autoScaleFormula][rest_autoscaleformula] 要素 (Batch REST) または [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] プロパティ (Batch .NET) に割り当てられます。Batch サービスをプールに割り当てると、Batch サービスによって、次の処理期間 (およびそれ以降の処理期間) にプールで使用可能なコンピューティング ノードの数の決定に数式が使用されます。この数式はサイズが 8 KB 以下の文字列で、最大 100 個のステートメントをセミコロンで区切って指定できます。また、改行やコメントを使用することもできます。

自動スケールの数式は、Batch 自動スケール "言語" を使用することと考えることができます。 数式は自由形式のステートメントになっていて、サービス定義の変数 (Batch サービスによって定義された変数) とユーザー定義の変数 (ユーザーによって定義された変数) の両方を含めることができます。組み込みの型、演算子、関数を使用して、これらの値に対する各種の操作を実行できます。たとえば、ステートメントは次の形式を使用する場合があります。

`$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);`

一般に、数式には複数のステートメントが存在し、先行するステートメントで取得された値に対してさまざまな操作が実行されます。たとえば `variable1` の値を取得しておき、それを関数に渡して `variable2` に代入します。

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

数式の中でそれらのステートメントを使用することによって、プールのコンピューティング ノード数をどこまでスケールするか、つまり**専用ノード**の**目標**数を得ることが必要となります。この数は、現在のプール内のノード数より多くなることも、少なくなることも、同じになることもあります。プールの自動スケールの数式は、Batch によって一定間隔で評価されます ([自動スケールの間隔](#automatic-scaling-interval)については以降で説明します)。その後、プール内の目標ノード数が、評価時に自動スケールの数式によって割り出されたノード数と一致するように調整されます。

以下にごく単純な例を紹介します。この 2 つの行から成る自動スケールの数式は、コンピューティング ノードの最大数を 10 とし、アクティブなタスクの数に基づいてノードの数を調整する必要があることを示しています。

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

以降いくつかのセクションで、変数、演算子、操作、関数など、自動スケールの数式を構成するさまざまな要素について説明します。Batch に存在する各種コンピューティング リソースとタスクについて、そのメトリックを取得する方法についても取り上げます。これらのメトリックを使用すると、リソースの使用状況やタスクの状態に応じて、プールのノード数をインテリジェントに調整することができます。その後、数式を作成する方法や、Batch の REST API と .NET API を使用してプールの自動スケールを有効にする方法について説明した後、最後に数式の例をいくつか紹介します。

> [AZURE.IMPORTANT] 各 Azure Batch アカウントは、処理に使用できる最大コア (コンピューティング ノード) 数に制限されています。そのコア数が、Batch サービスで作成される最大ノード数になります。そのため、数式から求められるコンピューティング ノードの目標数には届かない場合もあります。アカウントのクォータを表示する方法および増やす方法については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## 変数

自動スケールの数式には、**サービス定義**の変数と**ユーザー定義**の変数の両方を使用できます。サービス定義の変数は Batch サービスに組み込まれており、読み取り/書き込み可能な変数と読み取り専用の変数があります。ユーザー定義の変数は、ユーザーが*独自*に定義できる変数です。上記の 2 行の数式の例で、`$TargetDedicated` はサービス定義の変数であり、`$averageActiveTaskCount` はユーザー定義の変数です。

次の表に、Batch サービスで定義されている読み取り/書き込み変数と読み取り専用変数の両方を示します。

これらのサービス定義の変数の値を**取得**および**設定**することで、プール内のコンピューティング ノードの数を管理できます。

<table>
  <tr>
    <th>読み取り/書き込み可能な<br/>サービス定義の変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>プールの<b>専用コンピューティング ノード</b>の<b>目標</b>数。これはスケールによって目指すプールのコンピューティング ノード数です。プールはこのノード数に到達しない可能性があるため、この数は "目標" 数となります。たとえば、最初に設定された目標ノード数にプールが達する前に、後続の自動スケール評価によって目標ノード数が再度変更される場合があります。また、目標ノード数に到達する前に、Batch アカウントのノード数やコア クォータに達することも考えられます。</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>コンピューティング ノードがプールから削除されるときに発生するアクション。次のいずれかの値になります。
      <br/>
      <ul>
        <li><p><b>requeue</b> – すぐにタスクを終了し、再スケジュールされるようにジョブ キューに配置します。</p></li>
        <li><p><b>terminate</b> – すぐにタスクを終了し、ジョブ キューから削除します。</p></li>
        <li><p><b>taskcompletion</b> – 現在実行中のタスクの完了を待機して、プールからノードを削除します。</p></li>
        <li><p><b>retaineddata</b> - プールからノードを削除する前に、クリーンアップされるノードのデータを保存する、すべてのローカル タスクを待機します。</p></li>
      </ul></td>
   </tr>
</table>

これらのシステム定義の変数の値を**取得**して、Batch サービスのメトリックに基づいて調整を行います。

<table>
  <tr>
    <th>読み取り専用<br/>サービス定義<br/>variables</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>平均 CPU 使用率。</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>使用された秒数。</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>使用された平均メガバイト数。</td>
  <tr>
    <td>$DiskBytes</td>
    <td>ローカル ディスクで使用された平均ギガバイト数。</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>読み取られたバイト数。</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>書き込まれたバイト数。</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>実行された読み取りディスク操作の数。</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>実行された書き込みディスク操作の数。</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>受信バイト数。</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>送信バイト数。</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>コンピューティング ノードの数。</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>アクティブ状態のタスクの数。</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>実行状態のタスクの数。</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>正常に完了したタスクの数。</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>失敗したタスクの数。</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>専用コンピューティング ノードの現在の数。</td>
  </tr>
</table>

> [AZURE.TIP] 上記の読み取り専用であるサービス定義の変数は、それぞれに関連付けられたデータにアクセスするさまざまなメソッドを指定する "*オブジェクト*" です。詳細については、「[サンプル データの取得](#getsampledata)」を参照してください。

## 型

次の**型**が数式でサポートされています。

- double
- doubleVec
- doubleVecList
- string
- timestamp -- timestamp は次のメンバーを含む複合構造になっています。

	- year
	- month (1 ～ 12)
	- day (1 ～ 31)
	- weekday (数値の形式で表記します。月曜は 1 など)
	- hour (24 時間の数字で表記します。午後 1 時は 13 など)
	- minute (00 ～ 59)
	- second (00 ～ 59)
- timeinterval

	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

## 操作

上に挙げた型に対しては、次の**演算**を実行できます。

| 操作 | サポートされている演算子 | 結果の種類 |
| ------------------------------------- | --------------------- | ------------- |
| double *<演算子>* double | +, -, *, / | double |
| double *<演算子>* timeinterval | * | timeinterval |
| doubleVec *<演算子>* double | +, -, *, / | doubleVec |
| doubleVec *<演算子>* doubleVec | +, -, *, / | doubleVec |
| timeinterval *<演算子>* double | *, / | timeinterval |
| timeinterval *<演算子>* timeinterval | +, - | timeinterval |
| timeinterval *<演算子>* timestamp | + | timestamp |
| timestamp *<演算子>* timeinterval | + | timestamp |
| timestamp *<演算子>* timestamp | - | timeinterval | | *<演算子>*double | -、! | double | | *<演算子>*timeinterval | - | timeinterval | | double *<演算子>* double | <、<=、==、>=、>、!= | double | | string *<演算子>* string | <、<=、==、>=、>、!= | double | | timestamp *<演算子>* timestamp | <、<=、==、>=、>、!= | double | | timeinterval *<演算子>* timeinterval | <、<=、==、>=、>、!= | double | | double *<演算子>* double | &&、|| | double |

3 項演算子で double 型の値をテストするときに (`double ? statement1 : statement2`)、0 以外の値は **true**、0 は **false** となります。

## 関数

次の事前定義された**関数**は、自動スケールの数式の定義に使用できます。

| 関数 | 戻り値の型 | 説明
| --------------------------------- | ------------- | --------- |
| avg(doubleVecList) | double | doubleVecList のすべての値の平均値を返します。
| len(doubleVecList) | double | doubleVecList から作成されたベクター長を返します。
| lg (double) | double | 2 を底とする double の対数を返します。
| lg(doubleVecList) | doubleVec | 2 を底とする doubleVecList の成分ごとの対数を返します。このパラメーターには明示的に vec(double) を渡す必要があります。そのようにしないと、double lg(double) として解釈されます。
| ln(double) | double | double の自然対数を返します。
| ln(doubleVecList) | doubleVec | 2 を底とする doubleVecList の成分ごとの対数を返します。このパラメーターには明示的に vec(double) を渡す必要があります。そのようにしないと、double lg(double) として解釈されます。
| log(double) | double | 10 を底とする double の対数を返します。
| log(doubleVecList) | doubleVec | 10 を底とする doubleVecList の成分ごとの対数を返します。この単一の double パラメーターには明示的に vec(double) を渡す必要があります。そのようにしないと、double log(double) として解釈されます。
| max(doubleVecList) | double | doubleVecList の最大値を返します。
| min(doubleVecList) | double | doubleVecList の最小値を返します。
| norm(doubleVecList) | double | doubleVecList から作成されたベクトルの 2 ノルムを返します。
| percentile(doubleVec v, double p) | double | ベクトル v の百分位要素を返します。
| rand() | double | 0\.0 ～ 1.0 のランダムな値を返します。
| range(doubleVecList) | double | doubleVecList の最小値と最大値の差を返します。
| std(doubleVecList) | double | doubleVecList の値のサンプルの標準偏差を返します。
| stop() | | 自動スケール式の評価を停止します。
| sum(doubleVecList) | double | doubleVecList のすべての成分の合計を返します。
| time(string dateTime="") | timestamp | パラメーターが渡されない場合は現在の時刻のタイムスタンプ、渡された場合は dateTime 文字列のタイムスタンプを返します。サポートされている dateTime 形式は、W3C-DTF と RFC 1123 です。
| val(doubleVec v, double i) | double | 開始インデックス 0 のベクター v の位置 i にある要素の値を返します。

上記の表に示した一部の関数は、リストを引数として受け入れることができます。コンマ区切りのリストは、*double* と *doubleVec* の任意の組み合わせです。次に例を示します。

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 値は、評価の前に 1 つの *doubleVec* に変換されます。たとえば、`v = [1,2,3]` であるとき、`avg(v)` の呼び出しは、`avg(1,2,3)` を呼び出すことに相当します。`avg(v, 7)` の呼び出しは、`avg(1,2,3,7)` を呼び出すことに相当します。

## <a name="getsampledata"></a>サンプル データの取得

自動スケールの数式は、Batch サービスから提供されるメトリック データ (サンプル) に基づいて作用します。プール サイズは、サービスから数式に与えられる値に基づいて拡大されたり縮小されたりします。上記のサービス定義の変数は、関連付けられたデータにアクセスするメソッドを提供するオブジェクトです。たとえば、次の式は、最後の 5 分間の CPU 使用率を取得する要求を示しています。

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>メソッド</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p><b>GetSample()</b> メソッドは、データ サンプルのベクターを返します。
	<p>サンプルは、30 秒相当のメトリック データです。つまり、30 秒ごとにサンプルが取得されます。ただし、この後も説明しますが、サンプルが収集されてから、それが数式に使用できるようになるまでには時間差があります。そのため、特定の期間に取得されたすべてのサンプルを数式の評価に使用できない可能性があります。
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - 最新の収集済みサンプルから取得するサンプル数を指定します。</p>
				  <p>GetSample(1) は、使用できる最新のサンプルを返します。ただし、$CPUPercent などのメトリックの場合、サンプルが収集された<em>時間</em>がわからないので、GetSample を使用できません。最新の場合もありますが、システム上の問題が原因でかなり古い可能性があります。このような場合は、次のように期間を使用することをお勧めします。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>-- サンプル データを収集する期間を指定します。指定した期間内に必要となるサンプルの割合をオプションで指定できます。</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> は、過去 10 分間のサンプルがすべて CPUPercent 履歴に存在する場合、20 個のサンプルを返します。ただし、過去 1 分間の履歴を使用できない場合は、18 個のサンプルのみが返されます。この場合、次のようになります。<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> は失敗します。これは、サンプルの 90% のみが使用可能なためです。<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> の場合は成功します。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – 開始時刻と終了時刻の両方を使用してデータを収集する期間を指定します。</p></li></ul>
		  <p>前述のように、サンプルが収集される時間と、数式に使用できるようになる時間には遅延があります。<em>GetSample</em> メソッドを使用する場合は、この点を考慮する必要があります。以下の <em>GetSamplePercent</em> を参照してください。</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>履歴のサンプル データ セットで受け取ったサンプルの期間を返します。</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>メトリック履歴のサンプルの合計数を返します。</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>使用可能な最も古いメトリックのデータ サンプルのタイムスタンプを返します。</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>特定の時間間隔で利用できるサンプルの割合を返します。次に例を示します。</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>返されたサンプルの割合が指定した samplePercent 未満の場合、GetSample メソッドは失敗します。そのため、まず GetSamplePercent メソッドを使用して確認します。その後、十分なサンプルが存在しない場合は、自動スケール評価を停止せずに代替の操作を実行します。</p></td>
  </tr>
</table>

### サンプル、サンプルの割合、*GetSample()* メソッド

タスクおよびリソースのメトリック データを取得し、そのデータに基づいてプールのサイズを調整することが、自動スケールの数式の主要な動作となります。そのため、自動スケールの数式とメトリック データ ("サンプル") とがどのように関係し合うのかをしっかりと把握しておくことが重要です。

**サンプル**

Batch サービスでは、タスクおよびリソースのメトリックの "*サンプル*" を定期的に取得し、自動スケールの数式でこのサンプルを使用できるようにします。これらのサンプルは 30 秒に 1 回、Batch サービスによって記録されます。しかし、サンプルが記録されてから、それが自動スケールの数式で使用できるよう (読み取り可能) になるまでには通常、ある程度の時間差が生じます。さらに、ネットワークやその他のインフラストラクチャの問題など、さまざまな要因により、特定の間隔でサンプルが記録されない場合があります。その結果としてサンプル不足になります。

**サンプルの割合**

`samplePercent` を `GetSample()` メソッドに渡したり、`GetSamplePercent()` メソッドを呼び出したりするときの "パーセント" とは、Batch サービスによって記録される "*最大*" の合計サンプル数と、自動スケールの数式で実際に "*使用できる*" サンプル数との比を示します。

例として、10 分間の時間枠で説明します。サンプルは 30 秒ごとに記録されるため、10 分間の期間内に Batch によって記録されるサンプルの最大合計数は 20 サンプルとなります (1 分あたり 2 サンプル)。一方、レポートの機構上必然的に伴う時間差や Azure インフラストラクチャ内の問題により、自動スケールの数式で読み取り可能なサンプル数が 15 にしか到達しない可能性もあります。つまり、この 10 分間の期間で、数式が実際に使用可能なサンプルの数は、記録されたサンプル数の **75%** に限られるということです。

**GetSample() とサンプルの範囲**

プールの拡大と縮小は、自動スケールの数式に基づいてノードを追加したり削除したりすることによって行われます。ノードには金銭的コストが伴うため、数式には、十分なデータに基づいたインテリジェントな分析方法を用いる必要があります。このため、数式に傾向分析を取り入れ、収集したサンプルの "*範囲*" に基づいてプールの拡大縮小を行うことをお勧めします。

そのためには、`GetSample(interval look-back start, interval look-back end)` を使用して、サンプルの**ベクター**を返します。

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

上記の行は Batch によって評価されると、値のベクターとしてサンプルの範囲を返します。次に例を示します。

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

サンプルのベクターを収集すると、`min()`、`max()`、および `avg()` のような関数を使用して、収集した範囲から有意な値を導き出すことができます。

セキュリティの強化を目的として、特定の期間に使用できるサンプルの割合が特定の値を下回る場合に、数式によって行われる評価を強制的に "*失敗*" させることができます。指定したサンプルの割合を使用できない場合は、数式の評価を強制的に失敗させることで、数式のそれ以上の評価を中止するように Batch に指示します。プール サイズの変更は行われません。評価を成功させるために必要なサンプルの割合を指定するには、その割合を 3 番目のパラメーターとして `GetSample()` に指定します。ここで、サンプルの 75% という要件は次のように指定します。

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

また、前述したようにサンプルの可用性には遅延があるため、時間範囲を指定する際には、常に、開始時間を 1 分より長く遡って指定することが重要です。サンプルがシステムを介して伝達されるまで約 1 分かかるため、範囲 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 内のサンプルは使用できないことが頻繁にあります。ここでも、`GetSample()` の割合パラメーターを使用することで、サンプルの割合に関する特定の要件を強制的に指定することができます。

> [AZURE.IMPORTANT] 自動スケールの数式で、**`GetSample(1)` に "*のみ*" 依存するのは避けるようにすることを**強く推奨します****。これは、`GetSample(1)` は基本的に Batch サービスに対して、"どれほど前に取得したのかに関係なく、最後に取得したサンプルを渡すように" 指示するものであるためです。 それは単一のサンプルであり、また以前のサンプルであるため、最近のタスクまたはリソースの状態を表す情報として十分でない可能性があります。`GetSample(1)` を使用する場合は、より大きなステートメントの一部であり、数式が依存する唯一のデータ ポイントになっていないことを確認してください。

## メトリック

数式を定義するときは、**リソース**と**タスク**の両方のメトリックを使用できます。プール内の専用ノードの目標数は、収集して評価したメトリック データに基づいて調整します。各メトリックの詳細については、前述の「[変数](#variables)」セクションを参照してください。

<table>
  <tr>
    <th>メトリック</th>
    <th>説明</th>
  </tr>
  <tr>
    <td><b>リソース</b></td>
    <td><p><b>リソース メトリック</b>は、コンピューティング ノードの CPU、帯域幅、およびメモリの使用状況とノード数に基づくメトリックです。</p>
		<p> 次のサービス定義の変数は、ノード数に基づいて調整を行う場合に有用です。</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>次のサービス定義の変数は、ノード リソースの使用状況に基づいて調整を行う場合に有用です。</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>タスク</b></td>
    <td><p><b>タスク メトリック</b>は、タスクの状態 (保留中、アクティブ、完了) に基づくメトリックです。次のサービス定義の変数は、タスク メトリックに基づいてプールのサイズを調整する場合に有用です。</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## 自動スケールの数式のビルド

自動スケールの数式は、これまでに挙げたさまざまな要素を使ってステートメントを記述し、それらを組み合わせて 1 つにまとめあげることによって作成します。たとえば、ここでは最初に次を行う数式の要件を定義して、数式を構築しています。

1. CPU 使用率が高い場合、プール内のコンピューティング ノードの目標数を増やす。
2. CPU 使用率が低い場合、プール内のコンピューティング ノードの目標数を減らす。
3. 常に最大ノード数を 400 までに制限する。

CPU 使用率が高いときにノードを "*増やす*" 場合、過去 10 分間の平均 CPU 使用率の最小値が 70% を超えると、ユーザー定義の変数 ($TotalNodes) を現在のノードの目標数の 110% の値に設定するステートメントを定義します。

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

次のステートメントでは、過去 60 分間の平均 CPU 使用率が 20% より "*低い*" 場合、同じ変数を現在のノードの目標数の 90% に設定します。これによって、CPU 使用率が低いときに目標数が減らされます。このステートメントは、上記のステートメントからユーザー定義の変数 *$TotalNodes* も参照することに注意してください。

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

ここでは、専用コンピューティング ノードの目標数を**最大** 400 に制限しています。

`$TargetDedicated = min(400, $TotalNodes)`

完全な数式を次に示します。

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE] 自動スケールの数式は、[Batch REST][rest_api] API 変数、型、操作、および関数で構成されます。これらは [Batch .NET][net_api] ライブラリを使った作業中でも数式の文字列で使用されます。

## 自動スケールを有効にしてプールを作成する

プールの作成時に自動スケールを有効にするには、次のいずれかの手法を使用します。

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – この Azure PowerShell コマンドレットは、AutoScaleFormula パラメーターを使用して自動スケールの数式を指定します。
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) – この .NET メソッドがプールを作成するために呼び出されたら、プールの [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) プロパティと [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) プロパティを設定して、自動スケールを有効にします。
- [アカウントにプールを追加する](https://msdn.microsoft.com/library/azure/dn820174.aspx) – enableAutoScale 要素と autoScaleFormula 要素がこの REST API 要求で使用され、プールの作成時に自動スケールを設定します。

> [AZURE.IMPORTANT] 前述した手法のいずれかを使用して自動スケール対応のプールを作成する場合は、プールの *targetDedicated* パラメーターを指定**しないでください**。また、自動スケール対応のプールのサイズを手動で変更する場合 ([BatchClient.PoolOperations.ResizePool][net_poolops_resizepool] など)、最初にプールで自動スケールを**無効**にしてから、そのプールのサイズを変更する必要があります。

次のコード スニペットでは、自動スケール対応のプール ([CloudPool][net_cloudpool]) を [Batch .NET][net_api] ライブラリを使用して作成しています。このプールでは、自動スケールの数式によって、目標ノード数を月曜日は 5 に、それ以外の日は 1 に設定しています。さらに、自動スケールの間隔を 30 分に設定します (後述の「[自動スケールの間隔](#automatic-scaling-interval)」を参照)。次に示す C# スニペットまたはこの記事で示すその他の C# スニペットにおいて、"myBatchClient" は適切に初期化された [BatchClient][net_batchclient] のインスタンスです。

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### 自動スケールの間隔

Batch サービスは既定では、自動スケールの数式に従って **15 分**ごとにプールのサイズを調整します。ただし、この間隔は、次のプール プロパティを使用して構成することができます。

- REST API--[autoScaleEvaluationInterval][rest_autoscaleinterval]
- .NET API--[CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

間隔の最小値は 5 分、最大値は 168 時間です。この範囲外の間隔が指定されると、Batch サービスは「正しくない要求 (400)」エラーを返します。

> [AZURE.NOTE] 現行の自動スケール機能は、1 分以内に起こった変化に対応するというよりは、ワークロードを実行する過程でプールのサイズを少しずつ調整することを意図しています。

## プールを作成した後に自動スケールを有効にする

*targetDedicated* パラメーターを使用して指定した数のコンピューティング ノードで既にプールを設定している場合は、自動でスケーリングするように、後で既存のプールを更新できます。次のいずれかの方法で行います。

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] – この .NET メソッドには、既存のプールの ID と、プールに適用する自動スケールの数式が必要です。
- [プールで自動スケールを有効にする][rest_enableautoscale] – この REST API 要求では、URI の既存のプールの ID と要求本文内の自動スケールの数式が必要です。

> [AZURE.NOTE] プールの作成時に値を *targetDedicated* パラメーターに指定した場合、自動スケールの数式の評価時に無視されます。

このコード スニペットは、[Batch .NET][net_api] ライブラリを使用した既存のプールでの自動スケールの有効化を示します。既存のプールでの数式の有効化と更新の両方に、同じメソッドを使用することに注意してください。そのため、自動スケールが既に有効になっている場合は、この手法は指定されたプールの数式を *更新* します。スニペットでは、"mypool" が既存のプール ([CloudPool][net_cloudpool]) の ID であることを前提としています。

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## 自動スケールの数式を評価する

アプリケーションで使用する前には必ず数式を評価することをお勧めします。既存のプールで数式の "テストを実行" することで、数式を評価します。次を使用して行います。

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) または [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) – これらの .NET メソッドでは、既存のプールの ID と、自動スケールの数式を含む文字列が必要です。呼び出しの結果は、[AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) クラスのインスタンスに含まれます。
- [自動スケールの数式を評価する](https://msdn.microsoft.com/library/azure/dn820183.aspx) – この REST API 要求では、プールの ID は URI で指定され、自動スケールの数式は要求本文の *autoScaleFormula* 要素で指定されます。操作の応答には、数式に関連する可能性があるすべてのエラー情報が含まれています。

> [AZURE.NOTE] 自動スケールの数式を評価するには、最初に、有効な数式を使用して、プールの自動スケールを有効にする必要があります。

[Batch .NET][net_api] ライブラリを使用するこのコード スニペットでは、プール ([CloudPool][net_cloudpool]) に適用する前に数式を評価します。

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
	// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
	string myFormula = @"
		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;
	";

	// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
	// the pool.
	AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

	if (eval.AutoScaleRun.Error == null)
	{
		// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
		// variable as evaluated by the autoscale formula.
		Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

		// Apply the formula to the pool since it evaluated successfully
		client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
	}
	else
	{
		// Evaluation failed, output the message associated with the error
		Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
	}
}
```

このスニペットで数式の評価に成功すると、次のような出力になります。

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## 自動スケールの実行に関する情報を取得する

数式が適切に実行されるように、自動スケールの実行の結果を定期的に確認してください。

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) – .NET ライブラリを使用している場合、プールのこのプロパティは [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) クラスのインスタンスを提供します。このクラスからは、直近に実行された自動スケールについて、以下のプロパティを取得できます。
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [プールに関する情報を取得する](https://msdn.microsoft.com/library/dn820165.aspx) – この REST API 要求は、最新の自動スケールの実行を含むプールに関する情報を返します。

## <a name="examples"></a>数式の例

数式がプールのコンピューティング リソースの自動スケールを行うために使用できるいくつかの方法を示した例を見ていきましょう。

### 例 1: 時間ベースの調整

曜日と時刻に基づいてプールのサイズを調整し、プール内のノードの数を増減するとします。

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

この数式は、最初に現在の時刻を取得します。平日 (1 ～ 5) および稼働時間 (午前 8 時～午後 6 時) 内の場合、目標のプール サイズは 20 のノードに設定されます。それ以外の場合、プール サイズの目標は 10 のノードに設定されます。

### 例 2: 時間ベースの調整

この例では、プールのサイズはキューのタスク数に基づいて調整されます。コメントと改行の両方が数式の文字列に使用できることに注意してください。

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 例 3: 並列タスクの説明

別の例では、タスクの数に基づいてプールのサイズを調整します。この数式もプールに設定されている [MaxTasksPerComputeNode][net_maxtasks] の値を考慮します。これは特に、プールに対して[並列タスクの実行](batch-parallel-node-tasks.md)が有効にされている状況で便利です。

```
// Determine whether 70 percent of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### 例 4: 初期のプール サイズの設定

次に自動スケールの数式を使用した C# コード スニペットの例を示します。この例では、最初の期間はプール サイズを特定のノード数に設定し、最初の期間が経過した後は、実行中のアクティブなタスク数に基づいてプール サイズを調整します。

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

このコード スニペットの数式は、次の処理を行います。

- 最初のプール サイズを 4 ノードに設定しています。
- プールのライフサイクルの最初の 10 分間は、プール サイズを調整しません。
- 10 分経過した後は、過去 60 分間の実行中でアクティブなタスク数の最大値を取得します。
  - 両方の値が 0 の場合は (過去 60 分間に実行中またはアクティブなタスクがないことを示します)、プール サイズは 0 に設定されます。
  - いずれかの値が 0 よりも大きい場合は、変更されません。

## 次のステップ

* 「[同時実行ノード タスクで Azure Batch コンピューティング リソースの使用率を最大にする](batch-parallel-node-tasks.md)」では、プール内のコンピューティング ノードで複数のタスクを同時に実行する方法の詳細が説明されています。自動スケール以外にも、この機能は一部のワークロードのジョブの実行時間短縮に役立つことがあり、費用の節約になります。

* 他にも、効率を上げるために、Batch アプリケーションによって、最適な方法で Batch サービスが照会されていることを確認してください。「[効率的な Azure Batch サービスのクエリ](batch-efficient-list-queries.md)」では、数千に上る可能性のあるコンピューティング ノードやタスクの状態を照会するときに、送信されるデータの量を制限する方法について説明します。

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/ja-JP/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0727_2016-->