---
title: "Microsoft Azure のアプリケーション アーキテクチャ | Microsoft Docs"
description: "一般的な設計パターンを対象とするアーキテクチャの概要"
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Microsoft Azure のアプリケーションのアーキテクチャ
この記事では、Microsoft Azure を使用するアプリケーションを構築するためのリソースを紹介します。 これには、ソフトウェア システムを視覚的に示すツールが含まれます。

## <a name="design-patterns-poster"></a>設計パターンのポスター
Microsoft patterns & practices は、「[Cloud Design Patterns (クラウド設計パターン)](http://msdn.microsoft.com/library/dn568099.aspx)」を発行しました。この書籍は、MSDN と PDF ダウンロードの両方で利用できます。 また、すべてのパターンを視覚的に示す大型のポスターも用意されています。

![patterns & practices のクラウド パターンのポスター](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>シンボルとアイコン セットの描画
[Visio とシンボルに関するトレーニング ビデオを視聴](http://aka.ms/CnESymbolsVideo)してから、[クラウドおよびエンタープライズ用シンボル セットをダウンロード](http://aka.ms/CnESymbols)すると、Azure、Windows Server、SQL Server、その他について説明する技術資料を作成する際に役に立ちます。 無料か有料かに関係なく、シンボルは以下で使用できます。  
- アーキテクチャ図 
- トレーニング資料 
- プレゼンテーション 
- データシート 
- インフォグラフィック 
- ホワイト ペーパー 
- サード パーティの書籍 (Microsoft 製品を使用するユーザーのトレーニング用書籍の場合)

シンボルはユーザー インターフェイスで使用するためのものでありませんが、許可を求めることができます。  Azure のシンボルは使用して構いません。 つまり、Azure Portal 内と同じように同じオブジェクトを表すために使用するのであれば問題ありません。  たとえば、Azure Service Bus のシンボルは、Azure Service Bus オブジェクトを表し、Azure Service Bus オブジェクトへのアクセスにのみ使用します。 サード パーティのシンボルは Microsoft が所有しているわけではありません。 サード パーティのアイコンの使用規則については、各社の Web サイトを参照してください。

CnE シンボルは、Visio 形式、SVG 形式、および PNG 形式で提供されます。 セットには、PowerPoint でシンボルを簡単に使用する方法に関する追加の手順が含まれています。 シンボル セットは四半期に&1; 回発行され、新しいサービスがリリースされるたびに更新されます。

CnE セットのようにアーキテクチャ ダイアグラム向けに最適化されてはいませんが、[Microsoft Office Visio ステンシル](http://www.microsoft.com/en-us/download/details.aspx?id=35772)で、Microsoft Office と関連テクノロジの追加のシンボルを入手できます。   

具体的な質問やフィードバックについては、[CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) まで電子メールでお送りください。 ご意見、ご感想をお寄せください。この機能を継続的に改善できるよう、役立てたいと考えております。

![クラウドおよびエンタープライズ用シンボル/アイコン セット](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Microsoft のアーキテクチャの認定コース
Microsoft では、2015 年 8 月にマイクロソフト認定資格試験 70-534 をサポートするアーキテクチャ コースを作成しました。 このコースは、 [EDX.ORG で無料で利用可能](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)です。  このコースでは、 [3D ブループリントの Visio テンプレート](#3d-blueprint-visio-template)を使用します。

![Microsoft のアーキテクチャの認定コース](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Microsoft のソリューション
Microsoft は、Microsoft 製品を使用して特定の種類のシステムを構築する方法を示す、一連の大まかな[ソリューション アーキテクチャ](http://aka.ms/azblueprints)を公開しています。

これまで、Microsoft ではサンプル アーキテクチャを示す、一連の等角投影 3D ブループリントを公開していました。 これらのブループリントは、ソリューション アーキテクチャに置き換えられました。 ブループリントのリンクは、ソリューション アーキテクチャにリダイレクトされます。 何らかの理由で以前のブループリント資料にアクセスする必要がある場合は、[CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) まで電子メールで要望をお送りください。   

ブループリントとソリューション アーキテクチャの両方の図において、[クラウドおよびエンタープライズ用シンボル セット](http://aka.ms/CnESymbols)の一部が使用されています。   

![[Microsoft アーキテクチャ ブループリント 3D] ダイアグラム](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>3D ブループリントの Visio テンプレート
現在は使用されていない 3D バージョンの [Microsoft アーキテクチャ ブループリント](http://aka.ms/azblueprints) は、当初は非 Microsoft ツールで作成されました。 2015 年 8 月 5 日に、Visio 2013 (以降) のテンプレートが、[EDX.ORG で配信される Microsoft アーキテクチャの認定コース](#microsoft-architecture-certification-course)の一部として出荷されました。

テンプレートは、このコース以外でも利用もできます。

* [トレーニング ビデオを視聴](http://aka.ms/3dBlueprintTemplateVideo) する   
* [Microsoft 3D ブループリント Visio テンプレート](http://aka.ms/3DBlueprintTemplate)
* 3D テンプレートと一緒に使用する[クラウドおよびエンタープライズ用シンボル セット](https://www.microsoft.com/en-us/download/details.aspx?id=41937)をダウンロードする。 

トレーニング資料で解決できない具体的な質問や、フィードバックの提供については、 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) まで電子メールでご連絡ください。 テンプレートの開発は現在継続されていません。 ただし、更新が行われている任意の PNG や[クラウドおよびエンタープライズ用シンボル セット](http://aka.ms/CnESymbols)を使用できるため、テンプレートは現在も有用かつ有意です。  

![Microsoft 3D ブループリント Visio テンプレート](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>アーキテクチャのインフォグラフィック
Microsoft は、アーキテクチャに関連するポスター/インフォグラフィックをいくつか公開しています。 「[実際のクラウド アプリケーションの構築](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/)」や、[Cloud Services を使用したスケーリング](https://azure.microsoft.com/documentation/infographics/cloud-services/)に関するインフォグラフィックなどがあります。

![Azure Architecture Infographics](./media/architecture-overview/AzureArchInfographicThumb.jpg)

