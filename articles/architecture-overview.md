<properties 
	pageTitle="Microsoft Azure のアプリケーションのアーキテクチャ" 
	description="一般的な設計パターンを対象とするアーキテクチャの概要" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="robb"/>

#Microsoft Azure のアプリケーションのアーキテクチャ
Microsoft Azure を使用するアプリケーションを構築するためのリソースです。

##Microsoft のアーキテクチャの設計図

Microsoft では、Microsoft 製品と Microsoft Azure サービスを含む特定の種類のシステムを構築する方法を示す高レベルなアーキテクチャの一連の設計図を公開しています。各設計図には、ダウンロードして変更できる 2D Visio ベースのファイル、設計図を紹介するさらに色鮮やかな 3D PDF ファイル、3D バージョンを説明するビデオが含まれています。「[アーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」を参照してください。

2D 設計図ダイアグラムには、以降で説明するクラウドおよびエンタープライズ用シンボル セットが使用されています。

3D 設計図 PDF は Microsoft 以外のツールを使って作成されていますが、現在 Visio のテンプレートを開発中です。[ベータ版のテンプレートのトレーニング ビデオ](https://www.youtube.com/watch?v=MXDzcb5HNtM)を参照してください。Visio の 3D 設計図テンプレートのベータ版をご希望の方は、[CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) までメールでお問い合わせください。

![Microsoft Architecture Blueprint 3D diagram][BluePrint]

##クラウドおよびエンタープライズ用シンボル/アイコン セット

[クラウドおよびエンタープライズ用シンボル/アイコン セットをダウンロード](http://www.microsoft.com/download/details.aspx?id=41937)すると、Azure、Windows Server、SQL Server、その他の Microsoft 製品を記述する技術資料を作成する際に役に立ちます。これらのシンボルやアイコンは、アーキテクチャ ダイアグラム、トレーニング資料、プレゼンテーション、データシート、インフォグラフィックス、ホワイトペーパーに加え、サード パーティの書籍でも使用できます (Microsoft 製品の使用が想定された書籍の場合)。これらのシンボルは、Visio 形式と PNG 形式で提供されます。PowerPoint での PNG ファイルの使用方法が含まれています。

シンボル セットは四半期に 1 回発行され、新しいサービスがリリースされるたびに更新されます。最新リリースには、追加の Azure サービスが含まれる可能性があります。最新リリースのプレビューを希望する方は、[CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) までメールでお問い合わせください。

ダウンロード中にフィードバックを送付する手順も示していますので、ぜひご感想をお知らせください。シンボルを使用したことがある方は、短い 5 つの質問[アンケート](http://aka.ms/azuresymbolssurveyv2)に記入するか前述のアドレス宛てのメールにて、利便性や使用方法についてのご意見をお聞かせください。

CnE セットのようにアーキテクチャ ダイアグラム向けに最適化されてはいませんが、[Microsoft Office Visio ステンシル](http://www.microsoft.com/ja-jp/download/details.aspx?id=35772)で追加のシンボルを入手できます。

![Azure のシンボル/アイコン セット][azure_symbols]

##設計パターン
Microsoft Patterns and Practices は、MSDN と PDF ダウンロードの両方で利用可能な書籍「[クラウド設計パターン](http://msdn.microsoft.com/library/dn568099.aspx)」を発行しました。すべてのパターンを一覧表示する大型のポスターもあります。

![Patterns and Practices Cloud Patterns Poster][PnPPoster]

##アーキテクチャのインフォグラフィック
Microsoft は、ポスター/インフォグラフィックに関連するいくつかのアーキテクチャを公開しています。[実際のクラウド アプリケーションの作成](http://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/)と[クラウド サービスへの対応](http://azure.microsoft.com/documentation/infographics/cloud-services/)が含まれています。


![Azure Architecture Infographics][AzureInfographic]



[BluePrint]: ./media/architecture-overview/BluePrintThumb.png
[azure_symbols]: ./media/architecture-overview/AzureSymbols.png
[PnPPoster]: ./media/architecture-overview/PnPPatternPosterThumb.png
[AzureInfographic]: ./media/architecture-overview/AzureArchInfographicThumb.png

<!---HONumber=July15_HO1-->