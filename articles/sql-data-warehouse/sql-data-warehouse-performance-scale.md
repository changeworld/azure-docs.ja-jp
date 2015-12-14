<properties
   pageTitle="SQL Data Warehouse を使用した弾力的なパフォーマンスとスケール | Microsoft Azure"
   description="コンピューティング リソースをスケールアップおよびスケールダウンするために Data Warehouse ユニットを使用した SQL Data Warehouse の柔軟性を理解します。コード例が提供されています。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# SQL Data Warehouse を使用した弾力的なパフォーマンスとスケール
コンピューティング機能を弾力的に増減するために必要なことは、SQL Data Warehouse に割り当てられた Data Warehouse ユニット (DWU) の数を調整することのみです。Data Warehouse ユニットとは、SQL Data Warehouse によって生じた新しい概念であり、この調整を簡単かつ効率的に管理できます。このトピックでは Data Warehouse ユニットを紹介し、これを使用してコンピューティング機能を弾力的に調整する方法を説明します。この記事には、環境に適した DWU 値を設定する方法に関するいくつかの基本ガイダンスも含まれています。

## Data Warehouse ユニットの概要
Microsoft はバック グラウンドで多数のパフォーマンス ベンチマーク テストを実行し、顧客に競争力のあるサービスを提供するにはどのくらいの数のハードウェアが必要で、どのような構成が必要なのかを判断しています。コンピューティングのスケーリングの上下は 100 個の DWU というブロック単位で実行できますが、100 の倍数のすべての DWU が提供されるわけではありません。

## 必要な DWU の数
特定の顧客カテゴリに適している可能性のある規範的な DWU 開始点を提供するのではなく、この問題に実際的な手法で取り組んでみましょう。SQL Data Warehouse ではパフォーマンスは線形にスケールし、あるコンピューティング スケールから別のコンピューティング スケールへの変化 (100 個の DWU から 2000 個 の DWU への変化など) は数秒で実行されます。このため、スケールを柔軟にテストして、自分のシナリオに最も合うものを決定できます。

1. 開発中のデータ ウェアハウスの場合は、少ない数の DWU を選択することから始めます。
2. アプリケーションのパフォーマンスを監視し、選択した DWU の数に対するパフォーマンスの変化を観察します。
3. 線形スケールを想定して、要件に対して最適なパフォーマンス レベルに到達するために、パフォーマンスをどの程度上下する必要があるかを判別します。 
4. 選択する DWU の数を増減します。サービスはすぐに反応し、DWU 要件を満たすようにコンピューティング リソースが調整されます。
5. ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

アプリケーションのワークロードが変動する場合は、ワークロードが高い時点と低い時点に対応するようにパフォーマンス レベルを上下に移動させます。たとえば、月末にワークロードがピークに達することが多い場合は、ピークに達する日には DWU をさらに追加し、ピーク期間が終わったらスケールダウンするように計画します。
 
## コンピューティング リソースのスケーリングの増減
クラウド ストレージとは関係なく、SQL Data Warehouse の弾力性により、Data Warehouse ユニット (DWU) のスライディング スケールを使用してコンピューティング機能を拡大、縮小、または一時停止できます。これにより、ビジネスに最適なレベルにコンピューティング機能を調整できる柔軟性が得られます。

コンピューティング機能を高めるには、Azure クラシック ポータルのスケール スライダーを使用して、サービスに複数の DWU を追加できます。T-SQL、REST API、Powershell コマンドレットから DWU を追加することもできます。スケールを増減すると、実行中のアクティビティやキューに入れられているアクティビティはすべてキャンセルされますが、数秒以内で完了するため、増加または減少されたコンピューティング機能で再開できます。

[Azure クラシック ポータル][]で、SQL Data Warehouse ページの上部にある [スケール] アイコンをクリックし、スライダーを使用して Data Warehouse に適用される DWU の量を増減し、[保存] をクリックします。プログラムを使用してスケールを変更する場合は、次の T-SQL コードが SQL Data Warehouse に対して DWU の割り当てを調整する方法を示しています。

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
この T-SQL は、論理サーバーに対して実行し、SQL Data Warehouse インスタンス自体に対しては実行しないでください。

また、次のコードを使用し、Powershell を使用して同様に調整することもできます。

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## コンピューティング リソースの一時停止
SQL Data Warehouse 独自の機能として、オンデマンドでコンピューティングを一時停止し、再開できる機能があります。夜間、週末、特定の休日、またはその他の理由でチームが一定の期間 Data Warehouse インスタンスを使用しない場合、その期間中 Data Warehouse インスタンスを一時停止し、再開時に、以前に停止したところから開始できます。

この一時停止アクションにより、コンピューティング リソースはデータ センターで使用可能なリソースのプールに戻り、再開アクションにより、設定した DWU に必要なコンピューティング リソースが取得され、Data Warehouse インスタンスにそれらが割り当てられます。

コンピューティング機能の一時停止と再開は、[Azure クラシック ポータル][] で REST API または Powershell を介して行うことができます。一時停止すると実行中のアクティビティまたはキューに入れられたアクティビティはすべてキャンセルされ、戻ったときにコンピューティング リソースを数秒で再開できます。

次のコードは、PowerShell を使用して一時停止を実行する方法を示しています。

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

PowerShell を使用すれば、サービスの再開も非常に簡単に行うことができます。

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

PowerShell の使用の詳細については、「[Introduction to PowerShell cmdlets (PowerShell コマンドレットの概要)][]」記事を参照してください。

> [Azure.Note]ストレージはコンピューティングとは切り離されているため、一時停止によってストレージが影響を受けることはありません。

## 次のステップ
パフォーマンスの概要については、[パフォーマンスの概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[パフォーマンスの概要]: sql-data-warehouse-overview-performance.md
[Introduction to PowerShell cmdlets (PowerShell コマンドレットの概要)]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Azure クラシック ポータル]: http://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->