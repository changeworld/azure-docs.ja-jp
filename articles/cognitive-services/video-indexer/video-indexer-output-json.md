---
title: Azure Video Indexer の出力の詳細 | Microsoft Docs
description: このトピックでは、Video Indexer の出力の詳細を説明します。
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378654"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>v1 API によって生成される Video Indexer の出力の詳細

> [!Note]
> Video Indexer V1 API は非推奨になり、2018 年 8 月 1 日に削除されます。 中断を回避するために、Video Indexer v2 API を使い始めてください。
>
> Video Indexer v2 API を使用して開発する場合は、[こちら](https://api-portal.videoindexer.ai/)の手順を参照してください。 

**Get Breakdowns** API を呼び出し、応答の状態が OK になると、応答コンテンツとして詳細な JSON 出力が表示されます。 JSON コンテンツには、指定されたビデオの分析情報 (トランスクリプト、OCR、人) の詳細が含まれています。 詳細には、キーワード (トピック)、顔、ブロックが含まれます。 各ブロックには、時間の範囲、トランスクリプトの行、OCR の行、センチメント、顔、ブロックのサムネイルが含まれています。

**Get Breakdowns** API を使用すると、ビデオの完全な分析結果を JSON コンテンツとして取得できます。  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Video Indexer ポータルのビデオの **[再生]** ボタンを押して、ビデオの要約された分析情報を視覚的に確認することもできます。 詳しくは、「[View and edit video insights](video-indexer-view-edit.md)」(ビデオの分析情報の表示と編集) をご覧ください。

![洞察](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

この記事では、**Get Breakdowns** API によって返される JSON コンテンツについて説明します。 [概念](video-indexer-concepts.md)に関する記事も確認すると役立ちます。

> [!NOTE]
> Video Indexer のすべてのアクセス トークンの有効期限は 1 時間です。

## <a name="root-elements"></a>ルート要素

Attribute | 説明
---|---
id|このビデオの ID。 63c6d532ff など
partition|後で検索するために、ユーザーがアップロード時に指定できる論理パーティション。
name|ビデオの名前 Azure Monitor など
description|ビデオの説明  (例: 「John Kemnetz と Scott Hanselman が、Azure Monitor を使用して Azure のデータ監視能力を活用する方法を解説します」)。
userName|ビデオの作成者  (例: Channel9 Videos)。
createTime |作成時刻  (例: 2017-03-31T16:36:41.4504249+00:00)。
privacyMode|ビデオでは、**非公開** または **公開**のいずれかのモードを使用できます。 **公開** - アカウント内のすべてのユーザーと、ビデオへのリンクを持っているユーザーがビデオを見ることができます。 **秘密** - ビデオは、アカウント内のすべてのユーザーに表示されます。
isOwned|現在のユーザーがビデオを所有している場合は true。 それ以外の場合は false。  
isBase|分析結果がソース ビデオに基づいている場合は true。 分析結果が別の分析結果から派生したプレイリストのものである場合は false。
durationInSeconds|ビデオの再生時間。
summarizedInsights|1 つの [summarizedInsights](#summarizedInsights) が含まれます。
breakdowns|1 つ以上の [breakdowns](#breakdowns) が含まれている場合があります。
social|ビデオの "いいね!" の数と視聴回数を示す **social** 要素が 1 つ含まれています。

## <a name="summarizedinsights"></a>summarizedInsights

このセクションには、分析情報の概要が表示されます。

Attribute | 説明
---|---
name|ビデオの名前 Azure Monitor など
shortId|ビデオの ID 63c6d532ff など
privacyMode|内訳には、次のいずれかのモードを含めることができます: **秘密**、**公開**。 **公開** - アカウント内のすべてのユーザーと、ビデオへのリンクを持っているユーザーがビデオを見ることができます。 **秘密** - ビデオは、アカウント内のすべてのユーザーに表示されます。
duration|分析情報が発生した時刻を示す 1 つの期間が含まれます。 期間は秒単位です。
thumbnailUrl|ビデオのサムネイルの完全な URL  (例: https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...)。ビデオが非公開の場合、URL には 1 時間のアクセス トークンが含まれます。 1 時間後に URL は無効になるので、新しい URL で分析結果を再度取得するか、GetAccessToken を呼び出して新しいアクセス トークンを取得し、完全な URL ("https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]") を手動で作成する必要があります。
faces|1 つ以上の [faces](#faces) が含まれている場合があります。
topics|1 つ以上の [topics](#topics) が含まれている場合があります。
sentiments|1 つ以上の [sentiments](#sentiments) が含まれている場合があります。
audioEffects| 1 つ以上の [audioEffects](#audioEffects) が含まれている場合があります。
brands| 0 個以上の [brands](#brands) が含まれている場合があります。
統計|詳細については、「[Statistics](#Statistics)」をご覧ください。
.
### <a name="statistics"></a>統計

|Name|説明|
|---|---|
|CorrespondenceCount|ビデオ内の通知の数|
|WordCount|話者あたり単語の数|
|SpeakerNumberOfFragments|ビデオでの話者のフラグメントの数|
|SpeakerLongestMonolog|話者の最も長いモノローグ。 モノローグでの話者の沈黙がある場合、それも含まれます。 モノローグの先頭と末尾の無音は削除されます。| 
|SpeakerTalkToListenRatio|計算は、ビデオの合計時間で割られた話者のモノローグに費やされた時間に基づきます (間の無音は含みません)。 時間は、小数点第 3 位に丸められます。|

## <a name="breakdowns"></a>breakdowns

このセクションには、分析情報の詳細が示されます。

Attribute | 説明
---|---
id|分析結果 ID。 63c6d532ff など
state|特定の分析結果 ID の処理の状態。Uploaded (アップロード済み)、Processing (処理中)、Processed (処理済み)、Failed (失敗) のいずれかになります。
processingProgress|進行状況  (例: 10%)。
externalId| アップロード時に externalId を設定できます  (例: "4f9c3500-eca7-4ab3-987e-a745017af698")。 後で、この外部 ID でビデオを検索できます。
externalUrl|アップロード時に externalUrl を設定できます。 
metadata|アップロード時に metadata を設定できます。 
insights|1 つ以上の [insights](#insights) が含まれている場合があります。
thumbnailUrl|ビデオのサムネイルの完全な URL  (例: "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO..")。 ビデオが非公開の場合、URL には 1 時間のアクセス トークンが含まれます。 1 時間後に URL は無効になるので、新しい URL で分析結果を再度取得するか、GetAccessToken を呼び出して新しいアクセス トークンを取得し、完全な URL ("https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]") を手動で作成する必要があります。
publishedUrl|発行された URL  (例: "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest")。
viewToken|ベアラー トークン。
sourceLanguage|ソース言語。 サポートされている言語は、中国語、英語、フランス語、ドイツ語、イタリア語、日本語、ポルトガル語、ロシア語、スペイン語です。
language|トランスクリプトの言語。

## <a name="insights"></a>insights

Attribute | 説明 
---|---
transcriptBlocks|1 つ以上の [transcriptBlocks](#transcriptBlocks) が含まれている場合があります。
topics|1 つ以上の [topics](#topics) が含まれている場合があります。
faces|1 つ以上の [faces](#faces) が含まれている場合があります。
participants|1 つ以上の [participants](#participants) が含まれている場合があります。
contentModeration|[contentModeration](#contentModeration) が 1 つ含まれている場合があります。
audioEffectsCategories|1 つ以上の [audioEffectsCategories](#audioEffectsCategories) が含まれている場合があります。

## <a name="faces"></a>faces

### <a name="summarizedinsights"></a>summarizedInsights

**summarizedInsights** に表示される **faces** は、ビデオで検出された各顔の概要を示します。

Attribute | 説明 
---|---
id|人物の ID  (例: 11775)。
shortId|短い ID。 プレイリストは複数の分析結果から派生する可能性があるため、各顔の基になる分析結果を特定するときにこの ID が必要になります。  
name|顔が認識されると、その人物の名前が追加されます  (例: "Scott Hanselman")。 顔が不明な場合は、"Unknown #" が追加されます。 
description|顔が認識されると、Bing API の検索に基づいて説明が設定されます。 それ以外の場合は、説明は **null** になります。
title|顔が認識されると、Bing API の検索に基づいて説明が設定されます。 それ以外の場合は、タイトルは **null** になります。
thumbnailFullUrl|顔のサムネイルの完全な URL  (例: https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...)。ビデオが非公開の場合、URL には 1 時間のアクセス トークンが含まれます。 1 時間後に URL は無効になるので、新しい URL で分析結果を再度取得するか、GetAccessToken を呼び出して新しいアクセス トークンを取得し、完全な URL ("https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]") を手動で作成する必要があります。
appearances|1 つ以上の [appearances](#appearances) が含まれている場合があります。
seenDuration|顔が表示されていた時間 (秒単位)。
seenDurationRatio|ビデオの再生時間 (0-1) を基準としたプレゼンス。

### <a name="breakdown-insights"></a>breakdown insights

**breakdowns** に表示される **faces** は、ビデオで検出された各顔の詳細を示します。

Attribute | 説明 
---|---
id|人物の ID  (例: 11775)。
bingId|
name|顔が認識されると、その人物の名前が追加されます  (例: "Scott Hanselman")。 顔が不明な場合は、"Unknown #" が追加されます。 
thumbnailId|例: 616468f0-1636-4efa-94e7-262f2e575059。
description|顔が認識されると、Bing API の検索に基づいて説明が設定されます。 それ以外の場合は、説明は **null** になります。
title|顔が認識されると、Bing API の検索に基づいて説明が設定されます。 それ以外の場合は、タイトルは **null** になります。
imageUrl|この URL は、ソース ビデオから取得した画像を参照します。  
confidence|信頼度スコア (高いほど良好)。
knownPersonId|既知の人物 (著名人など) の ID。 人物が知られていない場合、ID にはゼロが含まれます。 例: e3eaff5f-ee1b-4eac-80ce-ebac47aadf64。

## <a name="topics"></a>topics

### <a name="summarizedinsights"></a>summarizedInsights

**summarizedInsights** に表示される **topics** は、ビデオで検出された各トピックの概要を示します。

Attribute | 説明 
---|---
name|トピック名 (例: "Azure")。 
appearances|1 つ以上の [appearances](#appearances) が含まれている場合があります。
isTranscript|トランスクリプトで検出された場合は true。 OCR で検出された場合は false。

### <a name="breakdown-insights"></a>breakdown insights

**breakdowns** に表示される **topics** は、ビデオで検出された各トピックの詳細を示します。

|Attribute | 説明 |
|---|---|
|id|一意のトピック ID。|
|name|トピック名。|
|stem|現在、この属性は使用されていません。|
|words|現在、この属性は使用されていません。|
|rank|関連性スコア (高いほど良好)。|

## <a name="sentiments"></a>sentiments

Attribute | 説明
---|---
sentimentKey| 現在サポートされているセンチメントは、Positive、Neutral、Negative です。 
appearances|1 つ以上の [appearances](#appearances) が含まれている場合があります。|.
seenDurationRatio|ビデオの再生時間 (0-1) を基準としたプレゼンス。

## <a name="audioeffects"></a>audioEffects

Attribute | 説明 
---|---
audioEffectKey| 有効な値は、Speech、Silence、HandClaps です。
appearances|1 つ以上の [appearances](#appearances) が含まれている場合があります。
seenDurationRatio|ビデオの再生時間 (0-1) を基準としたプレゼンス。
seenDuration|オーディオ エフェクトの継続時間 (秒単位)。

## <a name="appearances"></a>appearances

Attribute | 説明 
---|---
startTime| 時刻値。
endTime|時刻値。
startSeconds| 時刻値。
endSeconds| 時刻値。

## <a name="participants"></a>participants

Attribute | 説明 
---|---
id|参加者の ID。
name|参加者の名前  (例: Speaker #1)。
pictureUrl|**pictureUrl** 属性は、将来使用するために予約されています。

## <a name="contentmoderation"></a>contentModeration

Attribute | 説明 
---|---
adultClassifierValue|ビデオに成人向けコンテンツが含まれている信頼度レベル。
racyClassifierValue|ビデオにわいせつなコンテンツが含まれている信頼度レベル。
bannedWordsCount|不適切な単語の数。 
bannedWordsRatio|単語の総数に対する不適切な単語の比率。
reviewRecommended|ビデオを手動でレビューする必要があるかどうかを示すブール値。
isAdult|ビデオが手動でレビューされた後に、成人向けビデオと見なされたかどうかを示すブール値。

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attribute | 説明 
---|---
type|カテゴリの ID。
key|Speech、Silence、HandClaps のいずれか。 

## <a name="transcriptblocks"></a>transcriptBlocks

Attribute | 説明
---|---
id|ブロックの ID
lines|1 つ以上の [lines](#lines) が含まれている場合があります。
sentimentIds|**sentimentIds** 属性は、将来使用するために予約されています。
thumbnailIds|**thumbnailIds** 属性は、将来使用するために予約されています。
センチメント|ブロック内のセンチメント (0-1、ネガティブからポジティブ)。
faces|1 つ以上の [faces](#faces) が含まれている場合があります。
ocrs|1 つ以上の [ocrs](#ocrs) が含まれている場合があります。
audioEffectInstances|1 つ以上の [audioEffectInstances](#audioEffectInstances) が含まれている場合があります。
scenes|1 つ以上の [scenes](#scenes) が含まれている場合があります。
annotations|0 個以上の [annotations](#annotations) が含まれている場合があります。

## <a name="ocrs"></a>ocrs

ビデオのどの時点でテキスト コンテンツが検出されたのかを示します。 

Attribute | 説明 
---|---
timeRange|元のビデオの時間の範囲。
adjustedTimeRange|AdjustedTimeRange は、現在のプレイリストを基準とした時間の範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオを取得し、その中の 1 行だけ (たとえば 10:00-10:15) を使用できます。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。
lines|1 つ以上の [lines](#lines) が含まれている場合があります。

## <a name="lines"></a>lines

### <a name="transcriptblocks"></a>transcriptBlocks

**transcriptBlocks** に表示される **lines** は、ビデオで検出されたトランスクリプトの行を示します。

Attribute | 説明 
---|---
id| 行の ID。
timeRange|元のビデオの時間の範囲。
adjustedTimeRange|AdjustedTimeRange は、現在のプレイリストを基準とした時間の範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオを取得し、その中の 1 行だけ (たとえば 10:00-10:15) を使用できます。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。
participantID| この行の話者の ID。
text| トランスクリプト。
isIncluded| 基本分析結果では、常に true です。 派生プレイリストでは、ソース ビデオに含まれていた行は isIncluded=true に設定されます。 他のすべての行は false です。

### <a name="ocrs"></a>ocrs

**ocrs** に表示される **lines** は、ビデオで検出された OCR の行を示します。

Attribute | 説明 
---|---
id|OCR ID。
width|現在、この属性は使用されていません。
height|現在、この属性は使用されていません。
language|OCR 言語。
textData|OCR テキスト。
confidence|信頼度スコア (高いほど良好)。

## <a name="scenes"></a>scenes

Attribute | 説明 
---|---
id|シーン ID。
timeRange|**timeRange** が 1 つ含まれています。
keyFrame|キー フレームの時刻。
shots|1 つ以上の [shots](#shots) が含まれている場合があります。

## <a name="shots"></a>shots

Attribute | 説明 
---|---
id||スナップショット ID。
timeRange|**timeRange** が 1 つ含まれています。
keyFrame|キー フレームの時刻。

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribute | 説明 
---|---
type|音声イベントのインデックス: Laughter = 1、HandClaps = 2、Music = 3、Speech = 4、Silence = 5
ranges|1 つ以上の [ranges](#ranges) が含まれている場合があります。

## <a name="ranges"></a>ranges

**audioEffectInstances** に表示される **ranges** は、それらの範囲内のオーディオ エフェクトを示します。

Attribute | 説明 
---|---
timeRange|元のビデオの時間の範囲。
adjustedTimeRange|AdjustedTimeRange は、現在のプレイリストを基準とした時間の範囲です。 複数のビデオの複数の行からプレイリストを作成できるため、1 時間のビデオを取得し、その中の 1 行だけ (たとえば 10:00-10:15) を使用できます。 その場合、時間の範囲は 10:00-10:15 であっても、adjustedTimeRange が 00:00-00:15 である 1 行を含むプレイリストが作成されます。

## <a name="annotations"></a>annotations

認識可能なオブジェクト、生物、風景、アクション、視覚的パターンに基づいてタグを返します。

|Attribute|説明|
|---|---|
|id|注釈の ID。|
|Name|注釈の名前 (例: Person、Athletic game、Black Frames)。|
|Appearances|1 つ以上の appearances が含まれている場合があります。|

## <a name="brands"></a>brands

音声からテキスト トランスクリプトまたはビデオ OCR への変換で検出されたビジネスおよび製品ブランド名 これには、ブランドまたはロゴ検出の画像認識は含まれません。

Attribute | 説明
---|---
id | ブランドの ID。
name | Bing から取得したブランドまたはカスタマイズされたブランドの名前。  
wikiId | ブランド Wikipedia の URL のサフィックス たとえば、"Target_Corporation" は [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) のサフィックスです。
wikiUrl | ブランドの Wikipedia の URL (ある場合) たとえば、[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) です。
confidence | Video Indexer ブランド検出機能の信頼度の値 (0-1)
description | Wikipedia から抽出されたブランドの説明。 
appearances | 1 つ以上の appearances が含まれている場合があります。
seenDuration | ビデオの再生時間 (0-1) を基準としたプレゼンス。

## <a name="next-steps"></a>次の手順

独自の分析結果を作成する方法については、「[View and edit Video Indexer insights (Video Indexer の分析情報の表示と編集)](video-indexer-view-edit.md)」をご覧ください。

アプリケーションにウィジェットを埋め込む方法については、「[Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md)」(アプリケーションに Video Indexer ウィジェットを埋め込む) を参照してください。 

## <a name="see-also"></a>関連項目

[Video Indexer API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Video Indexer の概要](video-indexer-overview.md)

