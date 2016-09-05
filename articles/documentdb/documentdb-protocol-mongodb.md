<properties 
	pageTitle="MongoDB 向けの DocumentDB プロトコル サポート | Microsoft Azure" 
	description="現在、パブリック プレビューでご利用いただける DocumentDB の MongoDB プロトコル対応について紹介します。" 
	keywords="mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# MongoDB 向けの DocumentDB プロトコル サポート

## Azure DocumentDB とは
Azure DocumentDB は、完全管理された NoSQL データベース サービスです。高速で確実なパフォーマンス、高可用性、自動スケール、開発の容易さを視野に入れて構築されています。その柔軟性に優れたデータ モデルと一貫して低いレイテンシ、多彩なクエリ機能は、Web、モバイル、ゲーム、IoT など、シームレスな拡張性が求められるアプリケーションに最適な選択肢となっています。詳細については、[DocumentDB の概要](documentdb-introduction.md)を参照してください。

## DocumentDB における MongoDB のプロトコル対応とは
MongoDB 向けに作成されたアプリのデータ ストアとして DocumentDB データベースを使用できるようになりました。多くの場合、接続文字列を変更するだけで、MongoDB 用の既存の[ドライバー](https://docs.mongodb.org/ecosystem/drivers/)を使用して簡単かつ透過的に DocumentDB とやり取りすることができます。まだプレビュー段階ですが、この機能を使用することで、既にある MongoDB のスキルとツールを無駄にすることなく、DocumentDB の完全に管理されたスケーラブルな NoSQL データベースを利用するアプリケーションを簡単に作成し、Azure クラウドで実行することができます。

DocumentDB が MongoDB のプロトコルに対応したことで、データの CRUD (Create、Read、Update、Delete) やデータベースの照会などに、MongoDB の主要な API 関数を利用できるようになりました。現在実装されている機能は、一般的なプラットフォーム、フレームワーク、ツールの要件に加え、Azure を評価する大規模 MongoDB ユーザーのクラウド プラットフォームにおけるニーズを反映した優先度に従って決められました。
  

## 次のステップ


- MongoDB のプロトコル対応の DocumentDB アカウントを[作成](documentdb-create-mongodb-account.md)する方法を確認します。
- MongoDB のプロトコル対応の DocumentDB アカウントに[接続](documentdb-connect-mongodb-account.md)する方法を確認します。
- MongoDB のプロトコル対応の DocumentDB アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md)する方法を確認します。
- MongoDB のプロトコル対応 DocumentDB の[サンプル](documentdb-mongodb-samples.md)を体験します。

 

<!---HONumber=AcomDC_0824_2016-->