<properties
   pageTitle="Azure Data Catalog の新機能"
	description="Azure Data Catalog プレビューの新機能の概要です。"
	services="data-catalog"
	documentationCenter=""
	authors="steelanddata"
	manager="NA"
	editor=""
	tags=""/>
<tags
   ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="08/28/2015"
	ms.author="maroche"/>

# Azure Data Catalog の新機能

定期的に **Azure Data Catalog** の更新がリリースされます。すべてのリリースにユーザー向けの新機能が含まれているわけではなく、一部のリリースはバックエンド サービス機能向けです。ここでは、**Azure Data Catalog** サービスに追加されたユーザー向けの新機能について説明します。

## 2015 年 8 月 28 日の週のリリースの新機能

2015 年 8 月 28 日の週の時点で、**Azure Data Catalog** には次の機能が追加されています。

- SQL Server と Oracle のデータ ソースのデータプロファイルのサポート。SQL Server と Oracle のテーブルとビューを登録するときに、登録するオブジェクトのデータ プロファイル情報を含めることを選択できます。データ プロファイルには、オブジェクト レベルと列レベルの統計情報が含まれます。
- Hadoop HDFS データ ソースのサポート。HDFS のファイルとディレクトリを登録および検出できるようになりました。

## 2015 年 8 月 21 日の週のリリースの新機能

2015 年 8 月 21 日の週の時点で、**Azure Data Catalog** には次の機能が追加されています。

- 登録されているデータ ソースに対するアクセス要求情報の提供のサポート。任意の登録済みデータ資産について、ユーザーは電子メール リンクや URL などのアクセス要求方法を提供し、既存のツールおよびプロセスと簡単に統合できます。
- 登録されているデータ資産に対してどのようなユーザーがどのようなメタデータを提供したのかを簡単に検出できるようにするための、タグとエキスパートに関するヒント。
- 上部のナビゲーション バーに新しい [ユーザー] ボタンとメニューを追加しました。このメニューを使用すると、ユーザーは **Azure Data Catalog** へのログオンに使用したアカウントを確認でき、必要な場合はサインアウトできます。このメニューでは、**Azure Data Catalog** REST API を使用する開発者に役に立つカタログ名も表示されます。
- Standard Edition のみ: データ資産に所有者を追加するとき、**Azure Data Catalog** は所有者としてユーザー アカウントとセキュリティ グループの両方をサポートするようになりました。選択したデータ資産の所有者としてセキュリティ グループを追加する場合、グループの表示名またはグループの UPN 電子メール アドレス (ある場合) のいずれかを入力できます。
- Azure BLOB ストレージ データ ソースのサポート。ユーザーは、Azure Storage の BLOB およびディレクトリを登録および検出できるようになりました。

<!---HONumber=September15_HO1-->