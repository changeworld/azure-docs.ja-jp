<properties 
	pageTitle="DocumentDB Java SDK | Microsoft Azure" 
	description="リリース日、提供終了日、DocumentDB Java SDK の各バージョン間の変更など、Java SDK に関するあらゆる詳細を提供します。" 
	services="documentdb" 
	documentationCenter="java" 
	authors="aliuy" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="andrl"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table>
<tr><td>**ダウンロード**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**投稿**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**ドキュメント**</td><td>[Java SDK リファレンス ドキュメント](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**概要**</td><td>[Java SDK の概要](documentdb-java-application.md)</td></tr>
<tr><td>**現在サポートされているランタイム**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## リリース ノート

### <a name="1.8.0"/>[1\.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - 複数リージョンのデータベース アカウントのサポートを追加しました。
  - 最大再試行回数と最大再試行待機時間をカスタマイズするオプションと共に、調整された要求での自動再試行のサポートを追加しました。RetryOptions と ConnectionPolicy.getRetryOptions() をご覧ください。 
  - IPartitionResolver に基づくカスタム パーティション分割コードを廃止しました。大量のストレージとスループットを必要とする場合、パーティション分割コレクションをお使いください。 

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- スロットルのための再試行ポリシー サポートを追加しました。  

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- ドキュメントの有効期限 (TTL) サポートを追加しました。 

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- [パーティション分割コレクション](documentdb-partition-data.md)と[ユーザー定義のパフォーマンス レベル](documentdb-performance-levels.md)を実装しました。 

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- 他の SDK と一貫性を維持するため、リトル エンディアンのハッシュ値を生成する HashPartitionResolver のバグを修正しました。

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- ハッシュおよび範囲パーティション リゾルバーを追加して、複数のパーティションにわたってシャーディング アプリケーションを支援します。

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Upsert を実装します。Upsert 機能をサポートするために新しい upsertXXX メソッドが追加されました。
- ID ベースのルーティングを実装します。パブリック API の変更なし、すべて内部の変更。

### <a name="1.3.0"/>1.3.0
- 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 地理空間インデックスをサポートします
- すべてのリソースの id プロパティを検証します。リソースの ID には ?、/、#、\\ 文字を使えず、終わりの文字をスペースにできません。
- ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- V2 インデックス作成ポリシーを実装する

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK で DocumentDB に要求した場合、サービスにより却下されます。

> [AZURE.WARNING]
バージョン **1.0.0** 以前のすべてのバージョンの Azure DocumentDB SDK for Java は **2016 年 2 月 29 日**で提供が終了します。

<br/>

| バージョン | リリース日 | 提供終了日 
| ---	  | ---	         | ---
| [1\.8.0](#1.8.0) | 2016 年 6 月 14 日|--- 
| [1\.7.1](#1.7.1) | 2016 年 4 月 30 日 |--- 
| [1\.7.0](#1.7.0) | 2016 年 4 月 27 日 |--- 
| [1\.6.0](#1.6.0) | 2016 年 3 月 29 日 |--- 
| [1\.5.1](#1.5.1) | 2015 年 12 月 31 日 |--- 
| [1\.5.0](#1.5.0) | 2015 年 12 月 04 日 |--- 
| [1\.4.0](#1.4.0) | 2015 年 10 月 05 日 |--- 
| [1\.3.0](#1.3.0) | 2015 年 10 月 05 日 |--- 
| [1\.2.0](#1.2.0) | 2015 年 8 月 05 日 |--- 
| [1\.1.0](#1.1.0) | 2015 年 7 月 09 日 |--- 
| [1\.0.1](#1.0.1) | 2015 年 5 月 12 日 |--- 
| [1\.0.0](#1.0.0) | 2015 年 4 月 07 日 |--- 
| 0.9.5-prelease | 2015 年 3 月 09 日 | 2016 年 2 月 29 日 
| 0.9.4-prelease | 2015 年 2 月 17 日 | 2016 年 2 月 29 日 
| 0.9.3-prelease | 2015 年 1 月 13 日 | 2016 年 2 月 29 日 
| 0.9.2-prelease | 2014 年 12 月 19 日 | 2016 年 2 月 29 日 
| 0.9.1-prelease | 2014 年 12 月 19 日 | 2016 年 2 月 29 日 
| 0.9.0-prelease | 2014 年 12 月 10 日 | 2016 年 2 月 29 日

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 関連項目

DocumentDB に関する詳細は、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。

<!---HONumber=AcomDC_0615_2016-->