<properties
   pageTitle="Azure SQL Data Warehouse でのテーブルとインデックスの管理 | Microsoft Azure"
   description="Azure SQL Data Warehouse でのテーブルとインデックスの管理に関する考慮事項、ベスト プラクティス、およびタスクの概要。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL Data Warehouse でのテーブルとインデックスの管理

SQL Data Warehouse でのテーブルとインデックスの管理に関する考慮事項、ベスト プラクティス、およびタスクの概要。



| カテゴリ | タスクまたは考慮事項 | 説明 |
| :-----------------------| :---------------------------------------------- | :----------- |
| 列ストア インデックス | データの読み込みまたは挿入後にインデックスを再構築する | 既定では、SQL Data Warehouse には、各テーブルがクラスター化列ストア インデックスとして格納されます。これにより特に大きなテーブルでパフォーマンスとデータ圧縮が向上します。列ストア インデックスへのデータの取り込み方法によっては、一部のデータが列ストア圧縮で格納されない場合があります。この場合は、列ストア インデックスに設計されているとおりのパフォーマンスを得られない可能性があります。<br/><br/>列ストア インデックスが正常な状態であることを確認するには、[列ストア インデックスの管理][]に関するページをご覧ください。 |
| ハッシュ分散テーブル | データがノード間で均等に分散されていることを確認する | ハッシュ分散テーブルは、ほとんどの場合、大きなテーブルでの結合と集計を最適化する方法として最も効果的です。SQL Data Warehouse は、指定した分散列に基づいてテーブルを分散します。列によっては分散キーに適していたり、適していなかったりします。行は均等に分散する必要があります。ある程度のばらつき、つまりデータ傾斜は問題ありませんが、データ傾斜が大きすぎると、SQL Data Warehouse で実現できる超並列処理 (MPP) の利点が失われます。<br/><br/>ハッシュ分散テーブルで過度なデータ傾斜が発生しないようにするには、[分散データ傾斜の管理][]に関するページをご覧ください。 |





## 次のステップ

管理に関するその他のヒントについては、[管理の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[列ストア インデックスの管理]: sql-data-warehouse-manage-columnstore-indexes.md
[分散データ傾斜の管理]: sql-data-warehouse-manage-distributed-data-skew.md
[管理の概要]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->