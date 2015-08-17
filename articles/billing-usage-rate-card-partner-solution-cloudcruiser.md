<properties
   pageTitle="Cloud Cruiser と Microsoft Azure Billing API の統合"
   description="Microsoft Azure 課金パートナーの Cloud Cruiser が Azure Billing API を製品に統合した独自の事例について説明します。この記事は、Microsoft Azure Pack の使用/試用に関心を持っている Azure と Cloud Cruiser のユーザーには特に役立ちます。"
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Cloud Cruiser と Microsoft Azure Billing API の統合 

この記事では、新しい Azure Billing API から収集した情報を Cloud Cruiser で使用して、ワークフローのコスト シミュレーションと分析を行う方法について説明します。

## Azure RateCard API
RateCard API は、Azure の料金情報を提供します。適切な資格情報を認証した後は、API を照会し、Azure で使用できるサービスに関するメタデータや、自分のプラン ID に関連付けられた料金を収集できます。

次に、A0 (Windows) インスタンスの料金を表示する API の応答例を示します。

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Azure RateCard API に対する Cloud Cruiser のインターフェイス
Cloud Cruiser は、RateCard API をさまざまな方法で利用できます。この記事では、IaaS ワークロード コストのシミュレーションと分析に使用する方法を説明します。

この使用事例を読むにあたって、Microsoft Azure Pack (WAP) でいくつかのインスタンスが実行されているワークロードを想定してください。目標は、この同じワークロードを Azure でシミュレートし、移行した場合のコストを見積もることです。このシミュレーションを作成するために、主に次の 2 つの作業を実行します。

1. **RateCard API から収集したサービス情報のインポートと処理** - この作業はブックでも実行されます。この場合、RateCard API からの抽出を変換し、新しい料金プランに発行します。この新しい料金プランは、Azure の料金を見積もるシミュレーションに使用されます。

2. **IaaS の WAP サービスと Azure サービスの正規化** - 既定では、WAP サービスは個々のリソース (CPU、メモリ サイズ、ディスク サイズなど) に基づいていますが、Azure サービスはインスタンス サイズ (A0、A1、A2 など) に基づいています。この 1 つ目の作業は、Cloud Cruiser の ETL エンジン (ブックと呼ばれます) で実行できます。これらのリソースは、Azure インスタンス サービスと同様に、インスタンス サイズに基づいてまとめることができます。

## RateCard API からのデータのインポート

Cloud Cruiser のブックには、RateCard API の情報の収集と処理を自動化する機能があります。ETL (抽出-変換-読み込み) ブックを使用して、データのコレクション、変換、および Cloud Cruiser データベースへの発行を構成できます。

各ブックには、1 つ以上のコレクションを含めることができます。そのため、複数のソースの情報を関連付けて、使用状況データの補完と拡大を行うことができます。次の 2 つのスクリーンショットでは、既存のブックに新しい*コレクション*を作成する方法と、RateCard API から情報を*コレクション*にインポートする方法を示しています。

![図 1 - 新しいコレクションの作成][1]

![図 2 - 新しいコレクションからのデータのインポート][2]

データをブックにインポートすると、複数のステップで変換プロセスを作成して、データを変更し、モデルを作成することができます。この例では、サービスとしてのインフラストラクチャ (IaaS) にのみ関心があるので、変換ステップを使用して不要な行や、IaaS 以外のサービスに関連するレコードを削除することができます。

次のスクリーン ショットは、RateCard API から収集されたデータの処理に使用する変換ステップを示しています。

![図 3 - RateCard API から収集したデータを処理する変換ステップ][3]

## 新しいサービスと料金プランの定義

Cloud Cruiser には、サービスを定義する方法が複数あります。選択肢の 1 つは、使用状況データからサービスをインポートすることです。一般的に、この方法は、プロバイダーがサービスを定義済みであるパブリック クラウドを使用する場合にのみ使用されます。

料金プランは、有効期間、ユーザー グループなどのオプションに基づいて、さまざまなサービスに適用できる料金または価格のセットです。Cloud Cruiser の料金プランを使用して、シミュレーションまたは "what-if" シナリオを作成し、サービスの変更がワークロードの総コストに与える影響を理解できます。

この例では、RateCard API からのサービス情報を使用して Cloud Cruiser に新しいサービスを定義します。同様に、サービスに関連付けられた料金を使用して、Cloud Cruiser の新しい料金プランを作成できます。

変換プロセスが終わった後は、新しいステップを作成し、新しいサービスと料金として RateCard API からデータを発行することができます。

![図 4 - 新しい料金として RateCard API のデータを発行する][4]

## Azure サービスと料金

サービスと料金を発行したら、Cloud Cruiser の *[Services]* タブでインポートされたサービスの一覧を確認できます。

![図 5 - 新しいサービスの確認][5]

*[Rate Plans]* タブでは、“AzureSimulation” という新しい料金プランと、RateCard API からインポートされた料金を確認できます。

![図 6 - 新しい料金プランと関連付けられている料金の確認][6]

## WAP と Azure サービスの正規化

WAP の既定では、コンピューティング、メモリ、およびネットワーク リソースの使用に基づいて使用状況情報を提供します。Cloud Cruiser では、これらのリソースの割り当てまたは測定使用量に直接基づいてサービスを定義できます。たとえば、各時間の CPU 使用量の基本料金を設定したり、インスタンスに割り当てられたメモリの GB 数に対して課金したりすることができます。

この例では、WAP と Azure のコストを比較するために、WAP のリソース使用状況を収集してまとめる必要があります。その結果のデータは、Azure サービスに対応付けることができます。この変換は、ブックで簡単に実装できます。

![図 7 - サービスを正規化する WAP データの変換][7]

ブックの最後のステップは、データを Cloud Cruiser データベースに発行する処理です。このステップで、使用状況データはサービスにまとめられ (サービスは Azure サービスに対応付けられます)、既定の料金に関連付けられて料金が作成されます。

ブックが完成すると、Scheduler に新しい作業を追加し、ブックを実行する頻度と時間を指定することで、データの処理を自動化できます。

![図 8 - ブックのスケジュール設定][8]

## ワークロード コスト シミュレーション分析のレポート作成

使用状況を収集し、Cloud Cruiser データベースに読み込むと、Cloud Cruiser Insights モジュール (高度な分析レポート ツール) を利用して、目的のワークロード コスト シミュレーションを作成できます。

このシナリオを説明するために、次のレポートを作成しました。

![コストの比較][9]

上のグラフは、サービスを別のコスト比較を示し、WAP (濃い青色) と Azure (明るい青色) の間の特定のサービスごとのワークロードの実行料金を比較します。

下のグラフは同じデータですが、部門別に分けられ、WAP と Azure でワークロードを実行している各部門の料金を示しています。また、節約バー (緑色) は 2 つの差です。

## 次のステップ

+ Cloud Cruiser のブックとレポートの作成手順については、Cloud Cruiser のオンライン [ドキュメント](http://docs.cloudcruiser.com/)を参照してください (有効なログインが必要です)。Cloud Cruiser の詳細については、[info@cloudcruiser.com](mailto:info@cloudcruiser.com) に問い合わせてください。
+ Azure Resource Usage API と RateCard API の概要については、「[Microsoft Azure のリソース消費を把握する](billing-usage-rate-card-overview.md)」を参照してください。 
+ 両方の API の詳細については、「[Azure Billing REST API リファレンス](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください (Azure リソース マネージャーに用意されている API セットに含まれています)。
+ サンプル コードをすぐに確認するには、[Github の Microsoft Azure Billing API コード サンプル](https://github.com/Azure/BillingCodeSamples)を参照してください。

## 詳細情報
+ Azure リソース マネージャーの詳細については、「[Azure リソース マネージャーの概要](resource-group-overview.md)」を参照してください。

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "図 1 - 新しいコレクションの作成"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "図 2 - 新しいコレクションからのデータのインポート"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "図 3 - RateCard API から収集したデータを処理する変換ステップ"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "図 4 - 新しい料金として RateCard API のデータを発行する"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "図 5 - 新しいサービスの確認"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "図 6 - 新しい料金プランと関連付けられている料金の確認"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "図 7 - サービスを正規化する WAP データの変換"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "図 8 - ブックのスケジュール設定"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "図 9 - ワークロードのコスト比較シナリオのサンプル レポート"

<!---HONumber=August15_HO6-->