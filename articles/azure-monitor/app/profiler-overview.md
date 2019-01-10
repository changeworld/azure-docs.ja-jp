---
title: Application Insights Profiler を使用して Azure で運用アプリケーションのプロファイルを作成する | Microsoft Docs
description: フットプリントの小さいプロファイラーを使用して Web サーバー コードのホット パスを特定できます。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3e72c4af7139bac1cefb81d7b8fc1bbfcddabb6a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082062"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Application Insights を使用した Azure のプロファイル運用アプリケーション
## <a name="enable-profiler-for-your-application"></a>アプリケーションの Profiler を有効にする

Application Insights Profiler は、Azure の運用環境で稼働しているアプリケーションのパフォーマンス トレースを提供します。 エンド ユーザーに悪影響を及ぼすことなく、自動的に大量のデータをキャプチャします。 Profiler を使用すると、特定の Web 要求の処理時に最も時間が長くなる “ホットな“ コード パスを特定できます。 

プロファイラーは、以下の Azure サービスにデプロイされた .Net アプリケーションと連携します。 各サービスの種類のプロファイラーを有効にする具体的な手順については、以下のリンクを参照してください。

* [App Services](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric アプリケーション](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [仮想マシンと VM スケールセット](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Profiler を有効にして、トレースが表示されない場合は、[トラブルシューティング ガイド](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json)を参照してください。

オンプレミスでの Profiler の実行は公式にはサポートされていませんが、[試すことができる手順をいくつか紹介](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)しています。

## <a name="view-profiler-data"></a>プロファイラー データを表示する

プロファイラーがトレースをアップロードするには、アプリケーションが要求をアクティブに処理する必要があります。 実験を行っている場合は、[Application Insights パフォーマンス テスト](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test)を使用して Web アプリへの要求を生成できます。 Profiler を新しく有効にした場合は、短い読み込みテストを実行することができます。 読み込みテストの実行中に、[**[Profiler Settings]\(Profiler の設定\)**](profiler-settings.md #profiler-settings-page) ページの **[Profile Now]\(今すぐプロファイル\)** ボタンを押します。 プロファイラーが実行されると、1 時間に約 1 回、2 分間、ランダムにプロファイリングされます。 アプリケーションが要求の安定したストリームを処理している場合、Profiler から 1 時間ごとにトレースがアップロードされます。

アプリケーションでトラフィックを受け取り、プロファイラーにトレースをアップロードする時間がある場合は、表示するトレースが必要です。 このプロセスには 5 分から 10 分かかる可能性があります。 トレースを表示するには、**[パフォーマンス]** ウィンドウに移動し、**[Take Actions] \(アクションの実行)** を選択してプロファイラー トレースを表示してから、**[Profiler Traces] \(プロファイラー トレース)** ボタンを選択します。

![Application Insights の [パフォーマンス] ウィンドウの Profiler トレースのプレビュー][performance-blade]

要求の実行に費やされた時間のコード レベルの内訳を表示するためのサンプルを選択します。

![Application Insights トレース エクスプローラー][trace-explorer]

トレース エクスプローラーは、次の情報を表示します。

* **ホット パスの表示**:最大リーフ ノード、またはそれに近いノードが表示されます。 ほとんどの場合、このようなノードはパフォーマンスのボトルネックの近くにあります。
* **ラベル**:関数またはイベントの名前。 このツリーには、コードと発生したイベント (SQL イベントや HTTP イベントなど) が混在して表示されます。 最上位のイベントは要求時間全体に相当します。
* **経過時間**:操作の開始から終了までの期間。
* **時間**:関数またはイベントが他の関数と関連して実行されていた時間。

## <a name="how-to-read-performance-data"></a>パフォーマンス データの解釈方法

Microsoft サービス プロファイラーでは、アプリのパフォーマンス分析に、サンプリング メソッドとインストルメンテーションを組み合わせて使用します。 詳細な収集が進行中のとき、サービス プロファイラーは 1 ミリ秒ごとに各マシン CPU の命令ポインターをサンプリングします。 各サンプルでは、現在実行中のスレッドの呼び出し履歴全体がキャプチャされます。 これにより、そのスレッドが実行した内容に関する詳細情報が、高い抽象化レベルと低い抽象化レベルの両方で取得されます。 また、サービス プロファイラーでは、アクティビティの相関関係と因果関係を追跡するために、コンテキスト切り替えイベント、タスク並列ライブラリ (TPL) イベント、スレッドプール イベントなど、他のイベントも収集されます。

タイムライン ビューに表示される呼び出し履歴は、サンプリングとインストルメンテーションの結果です。 各サンプルはスレッドの完全な呼び出し履歴をキャプチャするため、それには Microsoft .NET Framework や、参照しているその他のフレームワークのコードが含まれています。

### <a id="jitnewobj"></a>オブジェクトの割り当て (clr!JIT\_New または clr!JIT\_Newarr1)

**clr!JIT\_New** と **clr!JIT\_Newarr1** は、マネージド ヒープからメモリを割り当てる .NET Framework のヘルパー関数です。 **clr!JIT\_New** は、オブジェクトが割り当てられるときに呼び出されます。 **clr!JIT\_Newarr1** は、オブジェクト配列が割り当てられるときに呼び出されます。 通常、これらの 2 つの関数は高速であり、比較的短時間で完了します。 タイムラインで **clr!JIT\_New** または **clr!JIT\_Newarr1** がかなり長時間かかっていることが確認された場合は、そのコードが多数のオブジェクトを割り当て、大量のメモリを消費している可能性があることを示しています。

### <a id="theprestub"></a>コードの読み込み (clr!ThePreStub)

**clr!ThePreStub** は、初めて実行するコードを準備する .NET Framework のヘルパー関数です。 これには通常、Just-In-Time (JIT) コンパイルが含まれます (ただし、これに限定されるわけではありません)。 C# の各メソッドでは、プロセスの有効期間中に **clr!ThePreStub** が最大 1 回呼び出されます。

要求に対して **clr!ThePreStub** がかなりの時間を要している場合は、その要求が、このメソッドを実行する最初の要求であることを示します。 .NET Framework ランタイムが最初のメソッドを読み込むには、かなりの時間がかかります。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用するか、アセンブリで Native Image Generator (ngen.exe) を実行することを検討してください。

### <a id="lockcontention"></a>ロックの競合 (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** または **clr!JITutil\_MonEnterWorker** は、現在のスレッドが、ロックが解放されるのを待っていることを示します。 このテキストは多くの場合、C# の **LOCK** ステートメントを実行するとき、**Monitor.Enter** メソッドを呼び出すとき、または **MethodImplOptions.Synchronized** 属性を持つメソッドを呼び出すときに表示されます。 ロックの競合は通常、スレッド _A_ がロックを取得し、スレッド _A_ がそれを解放する前にスレッド _B_ が同じロックを取得しようとしたときに発生します。

### <a id="ngencold"></a>コードの読み込み ([COLD])

**mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined** のように、メソッド名に **[COLD]** が含まれている場合は、.NET Framework ランタイムが、<a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">ガイド付き最適化のプロファイル</a>によって最適化されていないコードを初めて実行していることを示します。 これは、メソッドごとに、プロセスの有効期間中に最大 1 回表示されます。

要求に対してコードの読み込みがかなりの時間を要している場合は、要求がこのメソッドの最適化されていない部分を実行する最初の要求であることを示します。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用することを検討してください。

### <a id="httpclientsend"></a>HTTP 要求の送信

**HttpClient.Send** などのメソッドは、コードが HTTP 要求の完了を待機していることを示します。

### <a id="sqlcommand"></a>データベース操作

**SqlCommand.Execute** などのメソッドは、データベース操作が終了するのをコードが待機していることを示します。

### <a id="await"></a>待機 (AWAIT\_TIME)

**AWAIT\_TIME** は、別のタスクが終了するのをコードが待機していることを示します。 これは通常、C# の **AWAIT** ステートメントで発生します。 コードで C# の **AWAIT** が実行されると、スレッドはアンワインドして、スレッド プールの制御を戻します。**AWAIT** が終了するのを待機している間にスレッドがブロックされることはありません。 ただし、論理的に見れば、**AWAIT** を実行したスレッドは、操作の終了を待機している間 "ブロック" されます。 **AWAIT\_TIME** ステートメントは、タスクが終了するのを待機しているブロック時間を示します。

### <a id="block"></a>ブロック時間

**BLOCKED_TIME** は、別のリソースが使用可能になるのをコードが待機していることを示します。 たとえば、同期オブジェクトやスレッドが使用可能になるのを待っているか、要求が終了するのを待機している可能性があります。

### <a id="cpu"></a>CPU 時間

命令の実行中のため、CPU がビジー状態です。

### <a id="disk"></a>ディスク時間

アプリケーションがディスク操作を実行しています。

### <a id="network"></a>ネットワーク時間

アプリケーションがネットワーク操作を実行しています。

### <a id="when"></a>[When (実行期間)] 列

**[When]\(実行期間\)** 列は、ノードについて収集された包括的なサンプルが時間の経過と共にどのように変化するかを視覚化したものです。 要求の範囲全体は 32 個のタイム バケットに分割されます。 そのノードの包括的なサンプルは、その 32 個のバケットに蓄積されます。 各バケットはバーとして表されます。 バーの長さは、調整された値を表します。 **CPU_TIME** または **BLOCKED_TIME** とマークされているか、またはリソース (CPU、ディスク、スレッドなど) の消費との明らかな関係が存在するノードの場合、このバーは、そのバケットの期間中のいずれかのリソースの消費を表します。 これらのメトリックでは、複数のリソースを消費することによって 100 % を超える値になることがあります。 たとえば、ある間隔の間に平均して 2 つの CPU を使用した場合は、200 % になります。

## <a name="limitations"></a>制限事項

既定のデータ保有期間は 5 日です。 1 日あたりに取り込まれる最大データは 10 GB です。

Profiler サービスの使用に料金は発生しません。 Profiler サービスを使用するには、Web アプリが少なくとも Basic レベルの Web Apps でホストされている必要があります。

## <a name="overhead-and-sampling-algorithm"></a>オーバーヘッドとサンプリング アルゴリズム

Profiler は、トレースをキャプチャするために Profiler が有効になっているアプリケーションをホストする各仮想マシン上で 1 時間に 2 分間ランダムに実行されます。 Profiler が実行されているとき、サーバーには 5 ～ 15 % の CPU オーバーヘッドが追加されます。

## <a name="next-steps"></a>次の手順
Azure アプリケーションに対して Application Insights Profiler を有効にする
* [App Services](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric アプリケーション](profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [仮想マシンと VM スケールセット](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
