---
title: "Azure CDN によるメディア ストリーミングの最適化"
description: "スムーズな配信のためのメディア ファイルのストリーミングの最適化"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Azure CDN によるメディア ストリーミングの最適化 
 
高解像度ビデオは、インターネットでかつてないほど大きな比重を占めるようになっています。そのため、インターネット経由でこのような大容量ファイルを効率的に配信する際にさまざまな困難が生まれています。 お客様は、全世界のさまざまなネットワークおよびクライアントが VOD やライブ ビデオ アセットをスムーズに再生できることを期待しています。 メディア ストリーミング ファイルの高速で効率的な配信メカニズムを提供することは、お客様がスムーズで楽しい経験をすることを可能にするために極めて重要です。  

ライブ ストリーミング メディアは特に配信が困難です。それは、サイズが大きく同時に視聴するユーザー数が多いからだけではなく、遅延はユーザーにとって交渉を困難にするものだからです。 ライブ ストリームは事前にキャッシュ化できず、大きな遅延が視聴者に受け入れられることはないため、ビデオ フラグメントをタイムリーに配信する必要があります。 

ストリーミングの要求パターンも新しい困難をもたらします。 人気のあるライブ ストリームでも、ビデオ オン デマンド (VOD) の新シリーズがリリースされるときでも、数千人から数百万人の視聴者が同時にストリームを要求する場合があります。 この場合、アセットがまだキャッシュされていないときに配信元サーバーがパンクしないように要求を賢く整理することが重要です。
 
Azure CDN from Akamai は、大規模なストリーミング メディア アセットを世界中のエンド ユーザーに遅延を低減して効率的に配信し、かつ配信元サーバーの負荷を低減する機能を提供するようになりました。 この機能は、"Standard Akamai" の価格レベルで Azure CDN プロファイルで作成された Azure CDN エンドポイントの “Optimized For” 機能を使用して実現されます。 ライブと VOD ストリーミングの組み合わせを行う場合、VOD アセットの “Video on demand media streaming”(ビデオ オン デマンド メディア ストリーム) 最適化、および “General media streaming”(一般的なメディア ストリーミング) 最適化タイプを使用する必要があります。

Azure CDN from Verizon は、“general web delivery” (一般的な Web 配信) 最適化タイプでストリーミング メディアを直接配信できます。
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>CDN エンドポイントを構成して Azure CDN from Akamai でのメディア ストリーミングの配信を最適化する
 
CDN エンドポイントを構成して Azure Portal を介した大容量ファイルの配信を最適化するには、エンドポイント作成時に “Optimized For” プロパティ セクションで “General media streaming”(一般的なメディア ストリーミング) または “Video on demand media streaming”(ビデオ オン デマンド メディア ストリーム) オプションを選択するだけです。 REST API またはクライアント SDK のいずれかを使用してこれを行うこともできます。 次のスクリーン ショットは、Azure Portal を使用したプロセスを示しています。 
  
![新しいエンドポイントを追加する](./media/cdn-media-streaming-optimization/01_Adding.png)

*図 1: CDN プロファイルから新しい CDN エンドポイントを追加*

![選択したストリーミング](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*図 2: 選択されているビデオ オン デマンド メディア ストリーミングで CDN エンドポイントを作成* 
 
CDN エンドポイントを作成した後は、特定の条件に一致するすべてのファイルの最適化が適用されます。 次のセクションでは、これを詳しく説明します。 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Azure CDN from Akamai のメディア ストリーミングの最適化
 
Akamai のメディア ストリーミングの最適化は、個別のメディア フラグメントを配信に使用するライブまたは VOD ストリーミング メディアに対して有効です。これは、プログレッシブ ダウンロードまたはバイト範囲要求を使用して転送される単一の大容量アセットと対照的です。 メディア配信のスタイルについては、「[大きなファイルの最適化](cdn-large-file-optimization.md)」を参照してください。

一般的なメディア配信または VOD メディア配信のいずれの最適化タイプを使用しても、バックエンドの最適化を備えた CDN ネットワークを使用し、メディア アセットを高速で配信します。また、これから説明するベスト プラクティスに基づくメディア アセットの構成も使用します。

### <a name="caching"></a>Caching (キャッシュ)

アセットがストリーミング マニフェストまたはフラグメント (下記の一覧を参照) であることを Azure CDN from Akamai が検出すると、CDN は、一般的な配信とは異なるキャッシュの有効期限を使用します。 通常どおりに、配信元から送信されるキャッシュコントロールまたは有効期限のヘッダーが優先的に使用されますが、アセットがメディア アセットではない場合、一般的な Web 配信の有効期限を使用してキャッシュします。

配信元でパケットを使用できないライブ ストリームなど、まだ存在していないフラグメントを多くのユーザーが要求している場合、配信元のオフロードには短い負のキャッシュ時間が便利です。 通常ビデオ コンテンツは変更されないため、キャッシュ間隔を長くしても配信元からの要求をオフロードできます。
 

|    | 全般<br> web<br>配信を使用) | 全般<br> メディア<br> ストリーミング | VOD<br>メディア<br> ストリーミング  
--- | --- | --- | ---
キャッシュ - 正の値 <br> HTTP 200、203、300、 <br> 301、302、および 410 | 7 日 |365 日 | 365 日   
キャッシュ - 負の値 <br> HTTP 204、305、404、 <br> および 405 | なし | 1 秒 | 1 秒
 
### <a name="dealing-with-origin-failure"></a>配信元のエラーの処理  

一般的なメディアの配信および VOD メディアの配信にも、一般的な要求パターンのベスト プラクティスに基づく配信元のタイムアウトと再試行ログがあります。 たとえば、一般的なメディアの配信は、ライブと VOD メディアの配信の両方を対象にしているため、時間に厳しいライブ ストリーミングの特徴のため、非常に短い接続タイムアウトを使用します。

接続がタイムアウトになると、CDN は特定の回数再試行した後、クライアントに 504 ゲートウェイ タイムアウト エラーを送信します。 

ファイルがファイルの種類およびサイズ条件の一覧と一致すると、CDN はメディア ストリーミングの動作を使用し、それ以外の場合には一般的な Web 配信の動作を使用します。 
   
### <a name="conditions-for-media-streaming-optimization"></a>メディア ストリーミングの最適化の条件 

次の表に、この最適化が満たされる一連の条件を示します。 
 
サポート対象のストリーミング タイプ | ファイル拡張子  
--- | ---  
Apple HLS | m3u8、m3u、m3ub、key、ts、aac
Adobe HDS | f4m、f4x、drmmeta、bootstrap、f4f、<br>Seg-Frag URL 構造 <br> (対応する正規表現: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd、dash、divx、ismv、m4s、m4v、mp4、mp4v、 <br> sidx、webm、mp4a、m4a、isma
スムーズ ストリーミング | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Azure CDN from Verizon のメディア ストリーミングの最適化

Azure CDN from Verizon は、‘general web delivery’(一般的な Web 配信) 最適化タイプを使用して、ストリーミング メディア アセットを直接配信できます。 CDN には、既定ではメディア アセットの配信を直接支援するいくつかの機能もあります。

### <a name="partial-cache-sharing"></a>Partial Cache Sharing (部分キャッシュ共有)

この機能を使用すると、CDN から新しい要求への部分的にキャッシュ化されたコンテンツを使用できます。 つまり、たとえば、CDN への最初の要求がキャッシュ ミスになり、その要求は配信元に送信されます。 このコンテンツはそれでも CDN のキャッシュに読み込まれ (ただし、まだ不完全)、CDN へのその他の要求がこのデータの取得を開始します。 

### <a name="cache-fill-wait-time"></a>キャッシュ フィル待機時間

キャッシュ フィル待機時間機能は、配信元サーバーから HTTP 応答ヘッダーが到着するまで、同じリソースの後続のすべての要求をエッジ サーバーに強制的に保留させるため、部分キャッシュ共有と組み合わせて使用すると役に立ちます。 タイマーの有効期限が切れる前に配信元サーバーから HTTP 応答ヘッダーが到着すると、保留になったすべての要求はキャッシュを増大することなく使用されます (同時に、配信元からのデータによってフィルされます)。 既定では、キャッシュ フィル待機時間は 3000 ミリ秒に設定されます。 


