<properties
	pageTitle="Azure 診断のトラブルシューティング"
	description="次で Azure 診断を使用する場合の問題のトラブルシューティング。Azure Cloud Services、Virtual Machines および "
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Azure 診断のトラブルシューティング
Azure 診断の使用に関連する情報をトラブルシューティングします。Azure 診断の詳細については、[Azure 診断の概要](azure-diagnostics.md#cloud-services)に関するページを参照してください。

## Azure 診断が起動しない
診断は、ゲスト エージェント プラグインと監視エージェントの 2 つのコンポーネントで構成されます。

クラウド サービスのロールでは、ゲスト エージェント プラグインのログ ファイルは次のファイルにあります。

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

Azure Virtual Machine では、ゲスト エージェント プラグインのログ ファイルは次のファイルにあります。

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

プラグインによって次のエラー コードが返されます。

終了コード|説明
---|---
0|成功。
-1|一般的なエラー。
-2|rcf ファイルを読み込めませんでした。<p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。
-3|診断構成ファイルをロードできませんでした。<p><p>解決方法: これは、構成ファイルがスキーマ検証を渡さないことが原因です。スキーマに準拠する構成ファイルを使用してください。
-4|監視エージェント診断の別のインスタンスが既にローカル リソース ディレクトリを使用しています。<p><p>解決方法: **LocalResourceDirectory** に別の値を指定します。
-6|ゲスト エージェント プラグインの起動ツールが無効なコマンド ラインで診断を起動しようとしました。<p><p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。
-10|診断がハンドルされていない例外で終了しました。
-11|ゲスト エージェントが、監視エージェントの起動と監視を処理するプロセスを作成できませんでした。<p><p>。解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。<p>
-101|診断プラグインを呼び出すときの引数が無効です。<p><p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。
-102|プラグイン プロセスの初期化でエラーが発生しました。<p><p>解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。
-103|プラグイン プロセスの初期化でエラーが発生しました。具体的にはロガー オブジェクトを作成できません。<p><p>解決方法: 新しいプロセスを起動するのに十分なシステム リソースが使用できることを確認します。
-104|ゲスト エージェントが指定した rcf ファイルを読み込むことができません。<p><p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。
-105|診断プラグインが診断構成ファイルを開くことができません。<p><p>これは、診断プラグインが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。
-106|診断構成ファイルを読み込めませんでした。<p><p>解決方法: これは、構成ファイルがスキーマ検証を渡さないことが原因です。そのため、スキーマに準拠する構成ファイルを使用してください。VM 上の *%SystemDrive%\\WindowsAzure\\Config* フォルダーには、診断拡張機能に渡される XML があります。適切な XML ファイルを開いて **Microsoft.Azure.Diagnostics** を検索し、**xmlCfg** フィールドを見つけます。データは base64 でエンコードされているため、診断で読み込まれた XML を表示するにはこれを[デコードする](http://www.bing.com/search?q=base64+decoder)必要があります。<p>
-107|監視エージェントへのリソース ディレクトリのパスが無効です。<p><p>これは、監視エージェントが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p>
-108 |診断構成ファイルを監視エージェント構成ファイルに変換できません。<p><p>。これは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生する内部エラーです。
-110|一般的な診断構成エラー。<p><p>これは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生する内部エラーです。
-111|監視エージェントを開始できません。<p><p>解決方法: 新十分なシステム リソースが使用できることを確認します。
-112|一般的なエラー


## 診断データが Azure Storage に記録されない
Azure 診断のすべてのデータは、Azure Storage に格納されます。

イベント データが見つからない最も一般的な原因は、不正に定義されたストレージ アカウント情報です。

解決方法: 診断構成ファイルを修正し、診断を再インストールします。診断拡張機能を再インストールした後に問題が解決しない場合は、監視エージェント エラーを検索してさらにデバッグする必要がある場合があります。イベント データは、ストレージ アカウントにアップロードされる前に LocalResourceDirectory に格納されます。

クラウド サービスのロールでは、LocalResourceDirectory は次のとおりです。

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Virtual Machines では、LocalResourceDirectory は次のとおりです。

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

LocalResourceDirectory フォルダーにファイルがない場合、監視エージェントは起動できません。これは通常、無効な構成ファイルが原因であり、CommandExecution.log でイベントが報告される必要があります。

監視エージェントが正常にイベント·データを収集している場合は、構成ファイルで定義されたイベントごとに .tsf ファイルが表示されます。監視エージェントは、MaEventTable.tsf ファイルのエラーを記録します。このファイルの内容を確認するために、tabel2csv アプリケーションを使用して、.tsf ファイルをコンマ区切り値 (.csv) ファイルに変換できます。

クラウド サービスのロールの場合:

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

クラウド サービスのロールの *%Systemdrive%* は、通常 D: です。

Virtual Machine の場合:

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

上記のコマンドで *maeventtable.csv* ログ ファイルが生成されます。これを開くと、エラー メッセージがないか確認できます。


## 診断データ テーブルが見つからない
Azure 診断データを保持する Azure Storage 内のテーブルの名前には、次のコードを使用します。

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

たとえば次のようになります。

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

次の 4 つのテーブルが生成されます。

イベント|テーブル名
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-->