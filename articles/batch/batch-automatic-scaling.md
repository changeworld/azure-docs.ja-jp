
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
	ms.date="12/04/2015"
	ms.author="marsma"/>

# Azure Batch プール内のコンピューティング ノードの自動スケール

Azure Batch プール内のコンピューティング ノードの自動スケールは、アプリケーションによって使用される処理能力を動的に調整します。この調整のしやすさによって、時間と費用を節約できます。コンピューティング ノードとプールの詳細については、「[Azure Batch の技術概要](batch-technical-overview.md)」を参照してください。

自動スケールがプールで有効になっていて、プールに数式が関連付けられている場合に自動スケールが行われます。数式は、アプリケーションの処理に必要なコンピューティング ノードの数を決定するために使用されます。定期的に収集されるサンプル上で動作する場合、プール内の利用可能なコンピューティング ノードの数は、関連付けられている数式に基づいて 15 分ごとに調整されます。

自動スケールは、プールの作成時に設定したり、後で既存のプールで有効化したりすることができます。自動スケールが以前に有効になっていたプールで、数式を更新することもできます。プールに割り当てる前には必ず数式を評価し、自動スケールが実行している状態を監視することが重要です。それぞれの方法について以下で説明します。

> [AZURE.NOTE]各 Azure Batch アカウントは、処理のために使用できる最大コンピューティング ノード数に制限されています。システムはその制限までのみノードを作成するため、数式で指定された目標数に到達しない場合があります。

## コンピューティング リソースを自動的にスケール

定義したスケーリングの数式は、次の処理間隔にプール内で使用可能なコンピューティング ノードの数を決定します。自動スケールの数式は、単に要求本文 (REST API) または [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) プロパティ (.NET API) 内のプールの [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) 要素に関連付けられた文字列の値です。この数式はサイズが 8 KB 以下の文字列で、最大 100 のステートメントをセミコロンで区切って含めることができます。また、改行やコメントを含めることもできます。

数式内のステートメントの式は自由形式です。システム定義の変数、ユーザー定義の変数、定数値、これらの変数や定数でサポートされる操作を含めることができます。

	VAR = Expression(system-defined variables, user-defined variables);

複雑な数式は、複数のステートメントや変数を使用して作成されます。

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]自動スケールの数式は、[Batch REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 変数、型、操作、および関数で構成されます。これらは [Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) ライブラリを使った作業中でも数式の文字列で使用されます。

### 変数

システム定義の変数とユーザー定義の変数の両方を数式で使用できます。

これらの**システム定義の変数**の値を*取得*および*設定*することで、プール内のコンピューティング ノードの数を管理します。

<table>
  <tr>
    <th>変数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>プールの専用コンピューティング ノードの目標数。タスクの実際の使用状況に基づいて値を変更できます。</td>
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

これらの**システム定義の変数**の値を*取得*して、サンプルのコンピューティング ノードのメトリックに基づいて調整します。次の変数は、読み取り専用です。

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
    <td>$SampleNodeCount</td>
    <td>コンピューティング ノードの数</td>
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
    <td>専用コンピューティング ノードの現在の数</td>
  </tr>
</table>

### 型

次の**型**が数式でサポートされています。

- double
- doubleVec
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

### 操作

次の**操作**を上記の型で使用できます。

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

次の事前定義された**関数**は、自動スケールの数式の定義に使用できます。

<table>
  <tr>
    <th>関数</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>DoubleVecList のすべての値の平均値。</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>DoubleVecList から作成されたベクター長。</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>対数の底 2。</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Componentwise の対数の底 2。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double lg(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>自然対数。</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Componentwise の対数の底 2。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double lg(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>対数の底 10。</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Componentwise の対数の底 10。vec(double) は 1 つの double パラメーターに明示的に渡される必要があります。それ以外の場合は、double log(double) バージョンと見なされます。</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>DoubleVecList の最大値。</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>DoubleVecList の最小値。</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>DoubleVecList から作成された 2 つの標準ベクター。
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>ベクター v の百分位要素。</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>0.0 ～ 1.0 のランダムな値。</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>DoubleVecList の最小値と最大値の違い。</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>DoubleVecList の値のサンプルの標準偏差。</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>自動スケールの式評価を停止します。</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>doubleVecList のすべてのコンポーネントの合計。</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>パラメーターが渡されない場合は現在の時刻のタイムスタンプ、渡された場合は dateTime 文字列のタイムスタンプ。サポートされている dateTime 形式は、W3CDTF と RFC1123 です。</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>開始インデックス 0 のベクター v の場所 i にある要素の値。</td>
  </tr>
</table>

上記の表に示した一部の関数は、リストを引数として受け入れることができます。コンマ区切りのリストは、*double* と *doubleVec* の任意の組み合わせです。次に例を示します。

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 値は、評価の前に 1 つの *doubleVec* に変換されます。たとえば、`v = [1,2,3]` の場合、`avg(v)` の呼び出しは `avg(1,2,3)` の呼び出しに相当し、`avg(v, 7)` の呼び出しは `avg(1,2,3,7)` の呼び出しに相当します。

### サンプル データの取得

上記のシステム定義の変数は、関連付けられたデータにアクセスするメソッドを提供するオブジェクトです。たとえば、次の式は、最後の 5 分間の CPU 使用率を取得する要求を示しています。

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

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
    <td><p>データ サンプルのベクターを返します。
	<p>サンプルは、30 秒相当のメトリック データです。つまり、サンプルは 30 秒ごとに取得されますが、後述のように、サンプルが収集される時間と数式に使用できるようになる時間には遅延があります。そのため、特定の期間に取得されたすべてのサンプルを数式の評価に使用できない可能性があります。
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - 最新の収集済みサンプルから取得するサンプル数を指定します。</p>
				  <p>GetSample(1) は、使用できる最新のサンプルを返します。ただし、$CPUPercent などのメトリックの場合、サンプルが収集された<em>時間</em>がわからないので (最近のサンプルか、システムの問題で古いサンプルかがわかりません)、GetSample を使用できません。このような場合は、次のように期間を使用することをお勧めします。</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b> – サンプル データを収集する期間を指定し、必要に応じて、必要な期間内に使用できるサンプルの割合を指定します。</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> は、過去 10 分間のサンプルがすべて CPUPercent 履歴に存在する場合、20 個のサンプルを返します。ただし、過去 1 分間の履歴を使用できない場合は、18 個のサンプルのみが返されます。この場合、<br/>
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
    <td>HistoryBeginTime()</td>
    <td>使用可能な最も古いメトリックのデータ サンプルのタイムスタンプを返します。</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>現在、履歴に一定期間あるサンプルの割合を返します。次に例を示します。</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>返されたサンプルの割合が指定した samplePercent 未満の場合は、GetSample メソッドが失敗するため、GetSamplePercent メソッドを使用して最初に確認し、十分なサンプルが存在しない場合は、自動スケール評価を停止せずに代替の操作を実行します。</p></td>
  </tr>
</table>

### メトリック

数式を定義する際、リソースとタスクの両方の**メトリック**を使用できます。また、これらのメトリックをプール内のコンピューティング ノードを管理するために使用することもできます。

<table>
  <tr>
    <th>メトリック</th>
    <th>説明</th>
  </tr>
  <tr>
    <td>リソース</td>
    <td><p>リソース メトリックは、CPU 使用量、帯域幅使用量、メモリ使用量、およびコンピューティング ノードの数に基づきます。次のシステム変数 (上記の「変数」で説明) は、プール内のコンピューティング ノードを管理するための数式で使用されます。</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>次のシステム定義の変数は、ノードのリソース メトリックに基づいて調整を行うために使用されます。</p>
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
    <td>タスク</td>
    <td><p>タスクの状態 (アクティブ、保留中、完了) に基づきます。</p>
    <p>次のシステム定義の変数は、タスク メトリックに基づいて調整を行うために使用されます。</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## 自動スケールの数式のビルド

自動スケールの数式の構築は、上記のコンポーネントを使用して、ステートメントと完全な数式を組み合わせて行われます。たとえば、ここでは最初に次を行う数式の要件を定義して、数式を構築しています。

1. CPU 使用率が高い場合、プール内のコンピューティング ノードの目標数を増やす
2. CPU 使用率が低い場合、プール内のコンピューティング ノードの目標数を減らす
3. 常に最大ノード数を 400 までに制限する

CPU 使用率が高いときにノードを*増やす*場合、過去 10 分間の平均 CPU 使用率の最小値が 70% を超えると、ユーザー定義の変数 ($TotalNodes) を現在のノードの目標数の 110% の値に設定するステートメントを定義します。

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

次のステートメントでは、CPU 使用率が低いときに目標数を減らして、過去 60 分間の平均 CPU 使用率が 20% より*低い*場合は、同じ変数を現在のノードの目標数の 90% に設定します。このステートメントは、上記のステートメントからユーザー定義の変数 *$TotalNodes* も参照することに注意してください。

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

ここでは、専用コンピューティング ノードの目標数を**最大** 400 に制限しています。

	$TargetDedicated = min(400, $TotalNodes)

完全な数式を次に示します。

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## 自動スケールを有効にしてプールを作成する

プールの作成時に自動スケールを有効にするには、次のいずれかの手法を使用します。

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – この Azure PowerShell コマンドレットは、AutoScaleFormula パラメーターを使用して自動スケールの数式を指定します。
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) – この .NET メソッドがプールを作成するために呼び出されたら、プールの [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) と [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) プロパティを設定して、自動スケールを有効にします。
- [アカウントにプールを追加する](https://msdn.microsoft.com/library/azure/dn820174.aspx) – enableAutoScale 要素と autoScaleFormula 要素がこの REST API 要求で使用され、プールの作成時に自動スケールを設定します。

> [AZURE.NOTE]上記の手法のいずれかを使用してプールの作成時に自動スケールを設定する場合、プールの *targetDedicated* パラメーターは作成時に指定されません (指定できません)。また、自動スケールが有効なプールのサイズを手動で変更する場合 ([BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx) など)、最初にプールで自動スケールを無効にしてから、そのプールのサイズを変更する必要があります。

次のコード スニペットは、[Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) ライブラリを使用した自動スケールが有効な [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) の作成を示しています。このライブラリの数式は、毎週月曜日にノードの目標数を 5 に設定し、他の曜日はすべて 1 に設定します。このスニペットで、"myBatchClient" は適切に初期化された [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) のインスタンスです。

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## プールを作成した後に自動スケールを有効にする

*targetDedicated* パラメーターを使用して指定した数のコンピューティング ノードで既にプールを設定している場合は、自動でスケーリングするように、後で既存のプールを更新できます。次のいずれかの方法で行います。

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx) – この .NET メソッドには、既存のプールの ID と、プールに適用する自動スケールの数式が必要です。
- [プールで自動スケールを有効にする](https://msdn.microsoft.com/library/azure/dn820173.aspx) – この REST API 要求では、URI の既存のプールの ID と要求本文内の自動スケールの数式が必要です。

> [AZURE.NOTE]プールの作成時に値を *targetDedicated* パラメーターに指定した場合、自動スケールの数式の評価時に無視されます。

このコード スニペットは、[Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) ライブラリを使用した既存のプールでの自動スケールの有効化を示します。既存のプールでの数式の有効化と更新の両方に、同じメソッドを使用することに注意してください。そのため、自動スケールが既に有効になっている場合は、この手法は指定されたプールの数式を *更新* します。このスニペットは、"myBatchClient" が適切に初期化された [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) のインスタンスであり、"mypool" が既存の [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) の ID であると想定しています。

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

> [AZURE.NOTE]自動スケールの数式を評価するには、最初に有効な数式を使用して、プールの自動スケールを有効にする必要があります。

[Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) ライブラリを使用するこのコード スニペットでは、[CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) に適用する前に数式を評価します。

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
				// variable as evaluated by the the autoscaling formula.
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

このスニペットで数式の評価に成功すると、次のような出力になります。

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## 自動スケールの実行に関する情報を取得する

数式が適切に実行されるように、自動スケールの実行の結果を定期的に確認する必要があります。次のいずれかの方法で行います。

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) – .NET ライブラリを使用している場合、プールのこのプロパティは [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) クラスのインスタンスを提供します。最新の自動スケールの実行では以下のプロパティを提供しています。
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [プールに関する情報を取得する](https://msdn.microsoft.com/library/dn820165.aspx) – この REST API 要求は、最新の自動スケールの実行を含むプールに関する情報を返します。

## 数式の例

数式がプールのコンピューティング リソースの自動スケールを行うために使用できるいくつかの方法を示した例を見ていきましょう。

### 例 1

プール内のノードの数の増減に応じて、週の曜日と時刻に基づくプールのサイズを調整するとします。

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

この数式は、最初に現在の時刻を取得します。平日 (1 ～ 5) および勤務時間 (午前 8 時～午後 6 時) 内の場合、目標のプール サイズは 20 のノードに設定されます。それ以外の場合、プール サイズの目標は 10 のノードに設定されます。

### 例 2

この例では、プールのサイズはキューのタスク数に基づいて調整されます。コメントと改行の両方が数式の文字列に使用できることに注意してください。

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
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

### 例 3

別の例では、タスクの数に基づいてプールのサイズを調整します。この数式もプールに設定されている [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) の値を考慮します。これは、特にコンピューティング ノードで並列タスクの実行が必要な場合に便利です。

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
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

### 例 4

次に自動スケールの数式例を示します。この例では、最初の期間はプール サイズを特定のノード数に設定し、最初の期間が経過した後は、実行中のアクティブなタスク数に基づいてプール サイズを調整します。

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

上記のコード スニペットの数式には、次の特徴があります。

- 最初のプール サイズを 4 ノードに設定しています
- プールのライフサイクルの最初の 10 分間は、プール サイズを調整しません
- 10 分間が経過した後は、過去 60 分間の実行中でアクティブなタスク数の最大値を取得します。
  - 両方の値が 0 の場合 (過去 60 分間に実行中またはアクティブなタスクがないことを示します)、プール サイズは 0 に設定されます
  - いずれかの値が 0 よりも大きい場合は、変更されません

## 次のステップ

1. アプリケーションの効率を完全に評価するには、コンピューティング ノードにアクセスする必要がある場合があります。リモート アクセスを利用するには、アクセスするノードにユーザー アカウントを追加し、そのノードの RDP ファイルを取得する必要があります。
    - 次のいずれかの方法でユーザー アカウントを追加します。
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – この PowerShell コマンドレットは、プール名、コンピューティング ノード名、アカウント名、パスワードをパラメーターとして受け取ります。
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx) – この .NET メソッドはアカウント名とパスワードをコンピューティング ノードに対して設定できる [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) クラスのインスタンスを作成します。その後、[ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) はそのノードのユーザーを作成するインスタンスで呼び出されます。
        * [ユーザー アカウントをノードに追加する](https://msdn.microsoft.com/library/dn820137.aspx) – プール名とコンピューティング ノード名は URI で指定され、アカウント名とパスワードはこの REST API 要求の要求本文の一部としてノードに送信されます。
    - RDP ファイルを取得する
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx) – この .NET メソッドには、プールの ID、ノード ID、および作成する RDP ファイルの名前が必要です。
        * [ノードからリモート デスクトップ プロトコル ファイルを取得する](https://msdn.microsoft.com/library/dn820120.aspx) – この REST API 要求では、プール名とコンピューティング ノード名が必要です。応答に RDP ファイルの内容が含まれています。
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – この PowerShell コマンドレットは、指定したコンピューティング ノードから RDP ファイルを取得し、指定したファイルの場所やストリームに保存します。
2.	一部のアプリケーションは、処理するのに困難な大量のデータを生成します。これを解決する 1 つの方法は、[効率的なリスト クエリ](batch-efficient-list-queries.md)を使用することです。

<!---HONumber=AcomDC_1210_2015-->