
<properties
	pageTitle="Azure Batch プール内の計算ノードの自動スケーリング"
	description="自動スケーリングは、プールでこれを有効にし、アプリケーションの処理に必要な計算ノード数の計算に使用される数式をそのプールに関連付けることで実行されます。"
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="07/21/2015"
	ms.author="davidmu"/>

# Azure Batch プール内の計算ノードの自動スケーリング

Azure Batch プール内の計算ノードの自動スケーリングは、アプリケーションによって使用される処理能力を動的に調整します。この調整のしやすさによって、時間と費用を節約できます。計算ノードとプールの詳細については、「[Azure Batch の技術概要](batch-technical-overview.md)」をご覧ください。

自動スケーリングがプールで有効になっていて、プールに数式が関連付けられている場合に自動スケーリングが行われます。数式は、アプリケーションの処理に必要な計算ノードの数を決定するために使用されます。自動スケーリングは、プールの作成時に設定したり、既存のプールに後で設定することもできます。自動スケーリングが有効になっていたプールで、数式を更新することもできます。

自動スケーリングを有効にすると、使用可能な計算ノードの数が数式に基づいて 15 分ごとに調整されます。数式は、収集されたサンプルを 5 秒ごとに処理しますが、サンプルが収集されてから数式で使用されるまでに 75 秒の遅延があります。以下で説明する GetSample メソッドを使用する場合は、これらの時間要因を考慮する必要があります。

プールに割り当てる前には必ず数式を評価し、自動スケーリングが実行している状態を監視することが重要です。

> [AZURE.NOTE]各 Azure Batch アカウントは、処理のために使用できる最大計算ノード数に制限されています。システムはその制限までノードを作成し、数式で指定された目標数に到達しない場合があります。

## 数式を定義する

定義した数式は、次の処理間隔にプール内で使用可能な計算ノード数を決定するために使用されます。数式はサイズが 8 KB 以下の文字列で、最大 100 のステートメントをセミコロンで区切って含めることができます。

数式内のステートメントの式は自由形式です。システム定義の変数、ユーザー定義の変数、定数値、これらの変数や定数でサポートされる操作を含めることができます。

	VAR = Function(System defined variables, user-defined variables);

変数を結合して複雑な数式を作成できます。

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### 変数

システム定義の変数とユーザー定義の変数を数式で使用できます。これらのシステム定義の変数の値を設定することで、プール内の計算ノードを管理できます。

<table>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>プールの専用計算ノードの目標数。タスクの実際の使用状況に基づいて値を変更できます。</td>
  </tr>
  <tr>
    <td>$TVMDeallocationOption</td>
    <td>計算ノードがプールから削除されるときに発生するアクション。次のいずれかの値になります。
      <br/>
      <ul>
        <li><p><b>requeue</b> – すぐにタスクを終了し、再スケジュールされるようにジョブ キューに配置します。</p></li>
        <li><p><b>terminate</b> – すぐにタスクを終了し、ジョブ キューから削除します。</p></li>
        <li><p><b>taskcompletion</b> – 現在実行中のタスクの完了を待機して、プールからノードを削除します。</p></li>
        <li><p><b>retaineddata</b> - プールからノードを削除する前に、クリーンアップされるノードのデータを保存する、すべてのローカル タスクを待機します。</p></li>
      </ul></td>
   </tr>
</table>

これらのシステム定義の変数の値のみを読み取り、サンプルの計算ノードのメトリックに基づいて調整を行うことができます。

<table>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>平均 CPU 使用率</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>使用された秒数</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>使用された平均メガバイト数</td>
  <tr>
    <td>$DiskBytes</td>
    <td>ローカル ディスクで使用された平均ギガバイト数</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>読み取られたバイト数</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>書き込まれたバイト数</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>実行された読み取りディスク操作の数</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>実行された書き込みディスク操作の数</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>受信バイト数</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>送信バイト数</td>
  </tr>
  <tr>
    <td>$SampleTVMCount</td>
    <td>計算ノードの数</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>アクティブ状態のタスクの数</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>実行状態のタスクの数</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>正常に完了したタスクの数</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>失敗したタスクの数</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>専用計算ノードの現在の数</td>
  </tr>
</table>

### 型

次の型が数式でサポートされています。

- double
- doubleVec
- string
- timestamp
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

### 操作

次の演算を上記の型で使用できます。

<table>
  <tr>
    <th>操作</th>
    <th>使用可能な演算子</th>
  </tr>
  <tr>
    <td>double &lt;演算子> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;演算子> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;演算子> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;演算子> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;演算子> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;演算子> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;演算子> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;演算子> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;演算子> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;演算子>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;演算子>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;演算子> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;演算子> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;演算子> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;演算子> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;演算子> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>double 型のみテストする (ゼロ以外は true、ゼロは false)</td>
    <td>? :</td>
  </tr>
</table>

### 関数

次の事前定義された関数は、自動スケーリングの数式の定義に使用できます。

<table>
  <tr>
    <th>関数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>DoubleVecList のすべての値の平均値。</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>DoubleVecList から作成されたベクター長。</td>
  <tr>
    <td>double lg(double)</td>
    <td>対数の底 2。</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Componentwise の対数の底 2。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double lg(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>自然対数。</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Componentwise の対数の底 2。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double lg(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>対数の底 10。</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Componentwise の対数の底 10。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double log(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>DoubleVecList の最大値。</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>DoubleVecList の最小値。</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>DoubleVecList から作成された 2 つの標準ベクター。
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>ベクター v の百分位要素。</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>0.0 ～ 1.0 のランダムな値。</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>DoubleVecList の最小値と最大値の違い。</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>DoubleVecList の値のサンプルの標準偏差。</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>自動スケーリングの式評価を停止します。</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>doubleVecList のすべてのコンポーネントの合計。</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>パラメーターが渡されない場合は現在の時刻のタイムスタンプ、渡された場合は dateTime 文字列のタイムスタンプ。サポートされている dateTime 形式は、W3CDTF と RFC1123 です。</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>開始インデックス 0 のベクター v の場所 i にある要素の値。</td>
  </tr>
  <tr>
    <td>doubleVec vec(doubleVecList)</td>
    <td>doubleVecList から 1 つの doubleVec を明示的に作成します。</td>
  </tr>
</table>

表に示した一部の関数は、リストを引数として受け入れます。コンマ区切りのリストは、double と doubleVec の任意の組み合わせです。次に例を示します。

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

doubleVecList 値は、評価の前に 1 つの doubleVec に変換されます。たとえば、v = [1,2,3] の場合、avg(v) の呼び出しは avg(1,2,3) の呼び出しに相当し、avg (v, 7) の呼び出しは avg(1,2,3,7) の呼び出しに相当します。

### サンプル データ

システム定義の変数は、関連付けられたデータにアクセスするメソッドを提供するオブジェクトです。たとえば、次の式は、最後の 5 分間の CPU 使用率を取得する要求を示しています。

	$CPUPercent.GetSample(TimeInterval_Minute*5)

次のメソッドを使用して、サンプル データを取得できます。

<table>
  <tr>
    <th>メソッド</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>メトリック履歴のサンプルの合計数を返します。</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>データ サンプルのベクターを返します。次に例を示します。</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - 最新のサンプルから必要なサンプル数を指定します。</p>
				  <p>サンプルは、5 秒間のメトリック データです。GetSample(1) は、使用可能な最後のサンプルを返しますが、このサンプルはいつ収集されたかわからないので、$CPUPercent などのメトリックでは、これを使用しないでください。最新の場合もありますが、システム上の問題が原因でかなり古い可能性があります。次に示すように、期間を使用することをお勧めします。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b> – サンプル データを収集する期間を指定し、必要に応じて、必要な範囲内のサンプルの割合を指定します。</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10) は、最後の 10 分間のサンプルがすべて CPUPercent 履歴に存在する場合、200 のサンプルを返す必要があります。最後の 1 分間の履歴がまだ存在していない場合は、180 のサンプルが返されます。</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80) は成功し、$CPUPercent.GetSample(TimeInterval_Minute*10,95) は失敗します。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – 開始時刻と終了時刻の両方を使用してデータを収集する期間を指定します。</p></li></ul></td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>履歴のサンプル データ セットで受け取ったサンプルの期間を返します。</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>使用可能な最も古いメトリックのデータ サンプルのタイムスタンプを返します。</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>現在、履歴に一定期間あるサンプルの割合を返します。次に例を示します。</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b> - 返されたサンプルの割合が指定した samplePercent 未満の場合は、GetSample メソッドが失敗するため、GetSamplePercent メソッドを使用して最初に確認し、十分なサンプルが存在しない場合は、自動スケーリング評価を停止せずに代替の操作を実行します。</p></td>
  </tr>
</table>

### メトリック

次のメトリックを数式で定義できます。

<table>
  <tr>
    <th>メトリック</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>リソース</td>
    <td><p>CPU 使用率、帯域幅の使用率、メモリ使用量、計算ノードの数に基づきます。上記で説明した次のシステム変数は、プール内の計算ノードを管理するために数式で使用されます。</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$TVMDeallocationOption</li>
    </ul></p>
    <p>次のシステム変数は、ノードのメトリックに基づいて調整を行うために使用されます。</p>
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
    <p>この例は、過去 10 分間の平均 CPU 使用率の最小値が 70% を超える場合、プール内の計算ノード数を現在のノードの目標数の 110% に設定するために使用される数式を示しています。</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>この例は、過去 60 分間の平均 CPU 使用率が 20% 未満の場合、プール内の計算ノード数を現在のノードの目標数の 90% に設定するために使用される数式を示しています。</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>この例は、専用計算ノードの目標数を最大 400 に設定します。</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>タスク</td>
    <td><p>タスクの状態 (アクティブ、保留中、完了) に基づきます。</p>
    <p>次のシステム変数は、タスクのメトリックに基づいて調整を行うために使用されます。</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>この例は、サンプルの 70% が過去 15 分間に記録されているかどうかを検出する数式を示しています。記録されていない場合は、前回のサンプルを使用します。アクティブなタスクの数と一致するように計算ノードの数を増やそうとします (最大 3 つ)。プールの MaxTasksPerVM プロパティが 4 に設定されているため、ノード数をアクティブなタスクの数の 4 分の 1 に設定します。また、Deallocation オプションを "taskcompletion" に設定し、タスクが完了するまでコンピューターを保持します。</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $TVMDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## 自動スケーリングの数式を評価する

アプリケーションで使用する前には必ず数式を評価することをお勧めします。既存のプールでテストを実行することで、数式を評価します。次のいずれかのメソッドを使用してこれを行います。

- [IPoolManager.EvaluateAutoScale メソッド](https://msdn.microsoft.com/library/azure/dn931617.aspx)または [IPoolManager.EvaluateAutoScaleAsync メソッド](https://msdn.microsoft.com/library/azure/dn931545.aspx) – これらの .NET メソッドでは、既存のプール名と、自動スケーリングの数式を含む文字列が必要です。呼び出しの結果は、[AutoScaleEvaluation クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx)のインスタンスに含まれます。
- [自動スケーリングの数式を評価する](https://msdn.microsoft.com/library/azure/dn820183.aspx) – この REST 操作では、プール名は URI で指定され、自動スケーリングの数式は要求本文の autoScaleFormula 要素で指定されます。操作の応答には、数式に関連する可能性があるすべてのエラー情報が含まれています。

## 自動スケーリングを有効にしてプールを作成する

次のいずれかの方法でプールを作成します。

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – このコマンドレットは、AutoScaleFormula パラメーターを使用して自動スケーリングの数式を指定します。
- [IPoolManager.CreatePool メソッド](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) – この .NET メソッドがプールを作成するために呼び出された後、[ICloudPool.AutoScaleEnabled プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx)と [ICloudPool.AutoScaleFormula プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx)がプールで設定され、自動スケーリングが有効になります。
- [アカウントにプールを追加する](https://msdn.microsoft.com/library/azure/dn820174.aspx) – enableAutoScale 要素と autoScaleFormula 要素がこの REST API で使用され、プールの作成時に自動スケーリングを設定します。

> [AZURE.NOTE]上記で説明したリソースを使用してプールの作成時に自動スケーリングを設定する場合、targetDedicated パラメーターはプールで使用されません。

## プールを作成した後に自動スケーリングを有効にする

targetDedicated パラメーターを使用して指定した数の計算ノードで既にプールを設定している場合は、自動でスケーリングするように、後で既存のプールを更新できます。次のいずれかの方法で行います。

- [IPoolManager.EnableAutoScale メソッド](https://msdn.microsoft.com/library/azure/dn931709.aspx) – この .NET メソッドでは、既存のプール名と自動スケーリングの数式が必要です。
- [自動スケールの有効化または無効化](https://msdn.microsoft.com/library/azure/dn820173.aspx) – この REST API では、URI に既存のプール名と、要求本文に自動スケーリングの数式が必要です。

> [AZURE.NOTE]プールの作成時に targetDedicated パラメーターに指定された値は、自動スケーリングの数式の評価時に無視されます。

## 自動スケーリングの実行に関する情報を取得する

自動スケーリングの実行結果を定期的に確認する必要があります。次のいずれかの方法で行います。

- [ICloudPool.AutoScaleRun プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx) – .NET ライブラリを使用するときに、プールのこのプロパティが [AutoScaleRun クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx)のインスタンスを提供します。このクラスには、[AutoScaleRun.Error プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)、[AutoScaleRun.Results プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)、[AutoScaleRun.Timestamp プロパティ](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)が用意されています。
- [プールに関する情報を取得する](https://msdn.microsoft.com/library/dn820165.aspx) – この REST API は、最新の自動スケーリングの実行を含むプールに関する情報を返します。

## 次のステップ

1.	アプリケーションの効率を完全に評価できるように、計算ノードにアクセスする必要がある場合があります。リモート アクセスを利用するには、アクセスする計算ノードにユーザー アカウントを追加し、そのノードから RDP ファイルを取得する必要があります。次のいずれかの方法でユーザー アカウントを追加します。

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – このコマンドレットは、プール名、計算ノード名、アカウント名、パスワードをパラメーターとして受け取ります。
	- [IVM.CreateUser メソッド](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx)– この .NET メソッドは、計算ノードにアカウント名とパスワードを設定できる [IUser インターフェイス](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx)のインスタンスを作成します。
	- [ユーザー アカウントをノードに追加する](https://msdn.microsoft.com/library/dn820137.aspx) – プール名と計算ノード名は URI で指定され、アカウント名とパスワードはこの REST API の要求本文のノードに送信されます。

		RDP ファイルを取得する

	- [IVM.GetRDPFile メソッド](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx) – この .NET メソッドでは、作成する RDP ファイル名が必要です。
	- [ノードからリモート デスクトップ プロトコル ファイルを取得する](https://msdn.microsoft.com/library/dn820120.aspx) – この REST API では、プール名と計算ノード名が必要です。応答に RDP ファイルの内容が含まれています。
	- [Get AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – このコマンドレットは、指定した計算ノードから RDP ファイルを取得し、指定したファイルの場所やストリームに保存します。
2.	一部のアプリケーションは、処理するのに困難な大量のデータを生成します。これを解決する 1 つの方法は、[効率的なリスト クエリ](batch-efficient-list-queries.md)を使用することです。

<!---HONumber=August15_HO6-->