---
title: "シナリオ別の Azure コンテンツ配信の最適化"
description: "特定のシナリオに合わせてコンテンツの配信を最適化する方法"
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: fa4cf306eeb1a8372da5b2a86ac73fbba2832666
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>シナリオ別の Azure コンテンツ配信の最適化

世界中の多数のユーザーにコンテンツを配信する場合、コンテンツの配信を確実に最適化することが重要です。 Azure Content Delivery Network を使用すると、コンテンツの種類に応じて最適な配信を行うことができます。 コンテンツには、Web サイト、ライブ ストリーム、動画、ダウンロード用のサイズの大きなファイルなどがあります。 コンテンツ配信ネットワーク (CDN) エンドポイントを作成するとき、 **[Optimized for] \(最適化の種類)** オプションでシナリオを指定します。 この選択によって、CDN エンドポイントから提供されるコンテンツに適用される最適化が決まります。

最適化の選択肢は、ベスト プラクティス動作を使用して、コンテンツ配信のパフォーマンスと配信元のオフロードを向上するように設計されています。 シナリオの選択内容は、部分的なキャッシュ、オブジェクトのチャンキング、配信元のエラー再試行ポリシーの構成を変更することによってパフォーマンスに影響します。 

この記事では、さまざまな最適化機能と、これらをいつ使用すればよいかについての概要を示します。 機能と制限事項の詳細については、個々の最適化の種類に関する各ドキュメントをご覧ください。

> [!NOTE]
> **[Optimized for] \(最適化の種類)** メニュー オプションは選択したプロバイダーによって変わります。 CDN プロバイダーは、シナリオに応じて、さまざまな方法で拡張機能を適用します。 

## <a name="provider-options"></a>プロバイダー オプション

**Azure Content Delivery Network from Akamai** は、次の最適化をサポートしています。

* [一般的な Web 配信](#general-web-delivery) 

* [一般的なメディア ストリーミング](#general-media-streaming)

* [ビデオ オン デマンドのメディア ストリーミング](#video-on-demand-media-streaming)

* [大きなファイルのダウンロード](#large-file-download)

* [動的サイト アクセラレーション](#dynamic-site-acceleration) 

**Azure Content Delivery Network from Verizon** は、次の最適化をサポートしています。

* [一般的な Web 配信](#general-web-delivery) (メディア ストリーミングと大きなファイル コンテンツのダウンロードにも使用できます)

* [動的サイト アクセラレーション](#dynamic-site-acceleration) 

プロバイダー間のパフォーマンスのバラツキをテストして対象の配信に最適なプロバイダーを選択することを強くお勧めします。

## <a name="select-and-configure-optimization-types"></a>最適化の種類の選択と構成

新しいエンドポイントを作成するには、エンドポイントが配信するコンテンツの種類とシナリオに最も適した最適化の種類を選択します。 **General web delivery** (一般的な Web 配信) が既定値です。 既存の **Azure Content Delivery Network from Akamai** エンドポイントの場合、いつでも最適化オプションを更新できます。 この変更は、CDN からの配信を中断しません。 

1. **Azure Content Delivery Network from Akamai** のプロファイル内で、エンドポイントを選択します。

    ![エンドポイントの選択 ](./media/cdn-optimization-overview/01_Akamai.png)

2. **[設定]** で、**[最適化]** を選択します。 **[最適化の種類]** ドロップダウン リストで種類を選択します。

    ![最適化と種類の選択](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>特定のシナリオの最適化

次のいずれかのシナリオの CDN エンドポイントを最適化できます。 

### <a name="general-web-delivery"></a>一般的な Web 配信

一般的な Web 配信は、最も一般的な最適化オプションです。 これは、Web ページや Web アプリケーションなど、一般的な Web コンテンツの最適化用に設計されています。 この最適化は、ファイルやビデオのダウンロードにも使用できます。

一般的な Web サイトには、静的コンテンツと動的コンテンツが含まれています。 静的コンテンツには、イメージ、JavaScript ライブラリ、スタイル シートが含まれています。これらは、キャッシュしてさまざまなユーザーに配信できます。 動的コンテンツは、ユーザー プロファイルに合わせたニュース項目など、個々のユーザーにカスタマイズされています。 動的コンテンツは、ショッピング カートの内容など、ユーザーごとに一意であるため、キャッシュされせん。 一般的な Web 配信は、Web サイト全体を最適化できます。 

> [!NOTE]
> **Azure Content Delivery Network from Akamai** を使用する場合、平均ファイル サイズが 10 MB より小さい場合に、この最適化を使用できます。 平均ファイル サイズが 10 MB より大きい場合は、**[Large file download] \(大容量ファイルのダウンロード)** を **[Optimized for] \(最適化の種類)** ドロップダウン リストから選択します。

### <a name="general-media-streaming"></a>一般的なメディア ストリーミング

ライブ ストリーミングとビデオ オン デマンド ストリーミングの両方のエンドポイントを使用する必要がある場合は、一般的なメディア ストリームの最適化をお勧めします。

メディア ストリーミングは時間に厳しく、クライアントへのパケットの到着が遅れるとビデオ コンテンツのバッファリングが頻繁に発生するなど画像の品質が低下します。 メディア ストリーミングの最適化は、メディア コンテンツ配信の遅延を低減し、ユーザーにスムーズなストリーミングを提供します。 

これは、Azure メディア サービスのお客様に一般的に当てはまるシナリオです。 Azure メディア サービスを使用する場合、ライブとオン デマンド ストリーミングの両方に使用できる 1 つのストリーミング エンドポイントを取得します。 このシナリオでは、お客様は、ライブからオン デマンド ストリーミングに変更するときに別のエンドポイントに切り替える必要はありません。 一般的なメディア ストリーミングの最適化は、この種類のシナリオがサポートします。

**Azure Content Delivery Network from Verizon** は、一般的な Web 配信の最適化の種類でストリーミング メディア コンテンツを配信します。

メディア ストリーミングの最適化の詳細については、[メディア ストリーミングの最適化](cdn-media-streaming-optimization.md)に関するページをご覧ください。

### <a name="video-on-demand-media-streaming"></a>ビデオ オン デマンド メディア ストリーミング

ビデオ オン デマンド (VOD) メディア ストリーミングの最適化は、ビデオ オン デマンドのストリーミング コンテンツを向上します。 ビデオ オン デマンド ストリーミングのエンドポイントを使用する場合は、このオプションを使用します。

**Azure Content Delivery Network from Verizon** は、一般的な Web 配信の最適化の種類でストリーミング メディア コンテンツを配信します。

メディア ストリーミングの最適化の詳細については、[メディア ストリーミングの最適化](cdn-media-streaming-optimization.md)に関するページをご覧ください。

> [!NOTE]
> エンドポイントが主にビデオ オンデマンドのコンテンツを処理する場合は、この種類の最適化を使用します。 この最適化と一般的なメディア ストリーミングの最適化の主な違いは、接続の再試行タイムアウトです。ライブ ストリーミング シナリオの場合、非常に短い時間でタイムアウトします。

### <a name="large-file-download"></a>大容量ファイルのダウンロード

**Azure Content Delivery Network from Akamai** を使用している場合、1.8 GB より大きいファイルを配信するには、大きなファイルのダウンロードを使用する必要があります。 **Azure Content Delivery Network from Verizon** には、一般的な Web 配信の最適化にファイル ダウンロード サイズの制限はありません。

**Azure Content Delivery Network from Akamai** を使用している場合、大きなファイルのダウンロードは、10 MB より大きいコンテンツ用に最適化されています。 平均ファイル サイズが 10 MB より小さい場合は、一般的な Web 配信を使用します。 平均ファイル サイズが常に 10 MB より大きい場合、大容量ファイル用に個別のエンドポイントを作成する方がより効率的な場合があります。 たとえば、ファームウェアやソフトウェアの更新プログラムは、通常、サイズの大きなファイルになります。

**Azure Content Delivery Network from Verizon** は、一般的な Web 配信の最適化の種類で大きなファイルのダウンロード コンテンツを配信します。

大きなファイルの最適化の詳細については、「[大きなファイルの最適化](cdn-large-file-optimization.md)」をご覧ください。

### <a name="dynamic-site-acceleration"></a>動的サイト アクセラレーション

 動的サイトの高速化は、**Azure Content Delivery Network from Akamai** と **Azure Content Delivery Network from Verizon** の両方のプロファイルで使用できます。 この最適化を使用すると追加料金が発生します。詳細については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」を参照してください。

動的サイト アクセラレーションには、動的なコンテンツの遅延とパフォーマンスを改善するさまざまな技術が取り入れられています。 それらの技術には、ルートとネットワークの最適化、TCP の最適化などがあります。 

この最適化で、キャッシュ不可能な多数の応答を含む Web アプリを高速化することができます。 たとえば、検索結果、チェック アウト トランザクション、リアルタイム データなどを高速化できます。 静的なデータでは、主要な CDN のキャッシュ機能を継続して使用することができます。 

動的サイトの高速化の詳細については、[動的サイトの高速化](cdn-dynamic-site-acceleration.md)に関するページを参照してください。



