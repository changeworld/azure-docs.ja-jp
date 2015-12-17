<properties 
	pageTitle="DocumentDB Python SDK | Microsoft Azure" 
	description="リリース日、提供終了日、DocumentDB Python SDK の各バージョン間の変更など、Python SDK に関するあらゆる詳細を提供します。" 
	services="documentdb" 
	documentationCenter="python" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Python SDK

<table> <tr><td>**ダウンロード**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr> <tr><td>**投稿**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr> <tr><td>**ドキュメント**</td><td>[Python SDK Reference Documentation](http://azure.github.io/azure-documentdb-python/)</td></tr> <tr><td>**使用**</td><td>[Python SDK の使用](documentdb-python-application.md)</td></tr> <tr><td>**現在サポートされているプラットフォーム**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr> </table></br>

## リリース ノート

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Upsert を実装します。Upsert 機能をサポートするために新しい UpsertXXX メソッドが追加されました。
- ID ベースのルーティングを実装します。パブリック API の変更なし、すべて内部の変更。

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 地理空間インデックスをサポートします
- すべてのリソースの id プロパティを検証します。リソースの ID には ?、/、#、\\ 文字を使えず、終わりの文字をスペースにできません。
- ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- V2 インデックス作成ポリシーを実装する

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- プロキシ接続をサポートします

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK

## リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

> [AZURE.WARNING]バージョン **1.0.0** 以前のすべてのバージョンの Azure DocumentDB SDK for Python は **2016 年 2 月 29 日**で提供が終了しますす。

<br/>

| バージョン | リリース日 | 提供終了日 
| ---	  | ---	         | ---
| [1\.4.2](#1.4.2) | 2015 年 10 月 06 日 |---
| [1\.4.1](#1.4.1) | 2015 年 10 月 06 日 |---
| [1\.2.0](#1.2.0) | 2015 年 8 月 06 日 |---
| [1\.1.0](#1.1.0) | 2015 年 7 月 09 日 |---
| [1\.0.1](#1.0.1) | 2015 年 5 月 25 日 |---
| [1\.0.0](#1.0.0) | 2015 年 4 月 07 日 |---
| 0.9.4-prelease | 2015 年 1 月 14 日 | 2016 年 2 月 29 日
| 0.9.3-prelease | 2014 年 12 月 09 日 | 2016 年 2 月 29 日
| 0.9.2-prelease | 2014 年 11 月 25 日 | 2016 年 2 月 29 日
| 0.9.1-prelease | 2014 年 9 月 23 日 | 2016 年 2 月 29 日
| 0.9.0-prelease | 2014 年 8月 21 日 | 2016 年 2 月 29 日

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 関連項目

DocumentDB に関する詳細は、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。

<!---HONumber=AcomDC_1203_2015-->
