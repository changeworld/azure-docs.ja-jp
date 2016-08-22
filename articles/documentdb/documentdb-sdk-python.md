<properties 
	pageTitle="DocumentDB Python API と SDK | Microsoft Azure" 
	description="リリース日、提供終了日、DocumentDB Python SDK の各バージョン間の変更など、Python API と SDK に関するあらゆる詳細を提供します。" 
	services="documentdb" 
	documentationCenter="python" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# DocumentDB API と SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.JS](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST ()](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## DocumentDB Python API と SDK

<table>
<tr><td>**SDK のダウンロード**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**API ドキュメント**</td><td>[Python API リファレンス ドキュメント](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**SDK のインストール手順**</td><td>[Python SDK のインストール手順](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**SDK への協力**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**はじめに**</td><td>[Python SDK の概要](documentdb-python-application.md)</td></tr>
<tr><td>**現在サポートされているプラットフォーム**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## リリース ノート

### <a name="1.9.0"/>[1\.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- スロットルされた要求のための再試行ポリシー サポートを追加しました (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。 既定では、DocumentDB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。DocumentDB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。

- DocumentDB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。

- document\_client クラスで公開されている RetryPolicy クラスとそれに対応するプロパティ (retry\_policy) が削除され、代わりに、既定の一部の再試行オプションをオーバーライドするために使用できる ConnectionPolicy クラスの RetryOptions プロパティを公開する RetryOptions クラスが導入されました。

### <a name="1.8.0"/>[1\.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - 複数リージョンのデータベース アカウントのサポートを追加しました。

### <a name="1.7.0"/>[1\.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="1.6.1"/>[1\.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- partitionkey パスでの特殊文字の使用を許可するためのサーバー側のパーティション分割に関連するバグの修正。

### <a name="1.6.0"/>[1\.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- ハッシュおよび範囲パーティション リゾルバーを追加して、複数のパーティションにわたってシャーディング アプリケーションを支援します。

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Upsert を実装します。Upsert 機能をサポートするために新しい UpsertXXX メソッドが追加されました。
- ID ベースのルーティングを実装します。パブリック API の変更なし、すべて内部の変更。

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 地理空間インデックスをサポートします
- すべてのリソースの id プロパティを検証します。リソースの ID には ?、/、#、\\ 文字を使えず、終わりの文字をスペースにできません。
- ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- V2 インデックス作成ポリシーを実装します。

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- プロキシ接続をサポートします。

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK。

## リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

> [AZURE.WARNING]
バージョン **1.0.0** 以前のすべてのバージョンの Azure DocumentDB SDK for Python は **2016 年 2 月 29 日**で提供が終了しますす。

<br/>

| バージョン | リリース日 | 提供終了日 
| ---	  | ---	         | ---
| [1\.9.0](#1.9.0) | 2016 年 7 月 7 日 |--- 
| [1\.8.0](#1.8.0) | 2016 年 6 月 14 日 |--- 
| [1\.7.0](#1.7.0) | 2016 年 4 月 26 日 |--- 
| [1\.6.1](#1.6.1) | 2016 年 4 月 08 日 |--- 
| [1\.6.0](#1.6.0) | 2016 年 3 月 29 日 |--- 
| [1\.5.0](#1.5.0) | 2016 年 1 月 03 日 |--- 
| [1\.4.2](#1.4.2) | 2015 年 10 月 06 日 |--- 
| [1\.4.1](#1.4.1) | 2015 年 10 月 06 日 |--- 
| [1\.2.0](#1.2.0) | 2015 年 8 月 06 日 |--- 
| [1\.1.0](#1.1.0) | 2015 年 7 月 09 日 |--- 
| [1\.0.1](#1.0.1) | 2015 年 5 月 25 日 |--- 
| [1\.0.0](#1.0.0) | 2015 年 4 月 07 日 |--- 
| 0.9.4-prelease | 2015 年 1 月 14 日 | 2016 年 2 月 29 日 
| 0.9.3-prelease | 2014 年 12 月 09 日 | 2016 年 2 月 29 日 
| 0.9.2-prelease | 2014 年 11 月 25 日 | 2016 年 2 月 29 日6 
| 0.9.1-prelease | 2014 年 9 月 23 日 | 2016 年 2 月 29 日 
| 0.9.0-prelease | 2014 年 8月 21 日 | 2016 年 2 月 29 日

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 関連項目

DocumentDB に関する詳細は、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。

<!---HONumber=AcomDC_0810_2016-->