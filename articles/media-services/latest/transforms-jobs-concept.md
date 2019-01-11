---
title: Azure Media Services の Transform と Job | Microsoft Docs
description: Media Services を使用するときは、ビデオを処理するためのルールまたは仕様を説明する Transform を作成する必要があります。 この記事では、Transform とその使用方法の概要を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742781"
---
# <a name="transforms-and-jobs"></a>Transform と Job
 
[Transform](https://docs.microsoft.com/rest/api/media/transforms) を使用して、ビデオのエンコードや分析を行うための一般的なタスクを構成できます。 各 **Transform** は、ビデオまたはオーディオ ファイルを処理するためのレシピ､すなわちタスクのワークフローの記述です｡ 

各 [Job](https://docs.microsoft.com/rest/api/media/jobs) は、与えられた入力ビデオまたはオーディオ コンテンツに **Transform** を適用する Azuru Media Services への実際の要求です。 **ジョブ** には、入力ビデオの場所や出力先などの情報を指定します。 ビデオの場所は、HTTPS URL、SAS URL、または [Media Services アセット](https://docs.microsoft.com/rest/api/media/assets)を使用して指定できます。  

## <a name="typical-workflow"></a>一般的なワークフロー

1. Transform を作成します。 
2. その Transform に基づいて Job を送信します。 
3. Transform を一覧表示します。 
4. 将来利用する予定のない Transform は削除してください｡ 

すべてのビデオの最初のフレームをサムネイル画像として抽出するとします。実行する手順は次のようになります。 

1. ｢ビデオの先頭フレームをサムネールとして使用する｣などのビデオを処理するレシピ､すなわちルールを定義します｡ 
2. 1 つのビデオについて､サービスに次の情報を提供します｡ 
    1. ビデオがある場所｡  
    2. 出力するサムネイル画像を書き込む場所｡ 

**Transform** でレシピを 1 回作成すると (手順 1)､そのレシピを使って複数のジョブを送信することができます (手順 2)｡

## <a name="transforms"></a>変換

Transform は､v3 の API を直接使用して､あるいは公開されている任意の SDK を使用して､Media Services アカウントに作成することができます｡ Media Services v3 の API は Azure Resource Manager によって実行されるため、Resource Manager テンプレートを使用して、Media Services アカウントに Transforms を作成して デプロイすることもできます｡ ロールベースのアクセス制御を使用して､Transforms へのアクセスを管理することができます｡

### <a name="transform-definition"></a>変換定義

次の表は、変換のプロパティとその定義を示しています。

|Name|説明|
|---|---|
|Id|リソースの完全修飾リソース ID。|
|name|リソースの名前。|
|properties.created |変換の作成時の UTC 日時で、'YYYY-MM-DDThh:mm:ssZ' の形式です。|
|properties.description |変換の省略可能な詳細説明。|
|properties.lastModified |変換が最後に更新されたときの UTC 日時で、'YYYY-MM-DDThh:mm:ssZ' の形式です。|
|properties.outputs |変換を生成する必要のある 1 つ以上の TransformOutput の配列。|
|type|リソースの種類。|

完全な定義については、「[Transforms](https://docs.microsoft.com/rest/api/media/transforms)」(変換) をご覧ください。

上記のように､Transform を使用して､ビデオを処理する際のレシピまたはルールを作成することができます｡ 1 つの Transform によって複数のルールを適用することができます｡ たとえば､1 つの Transform で各ビデオを指定されたビットレートの MP4 ファイルにエンコードして､そのビデオの先頭フレームからサムネール画像を生成するように指定できます｡ Transform に含めたいルールごとに TransformOutput エントリ 1 つを追加します｡

> [!NOTE]
> Transforms の定義は Update 操作をサポートしていますが､変更する場合は､事前に処理中のすべてのジョブが完了していることを確認してください｡ 一般に既存の Transform を更新するのは､その記述を変更したり､土台の TransformOutput の優先順位を変更したりしたい場合です｡ レシピを書き直したい場合は､新しい Transform を作成します｡

## <a name="jobs"></a>[ジョブ]

Transform を作成すると､Media Services API または公開されている任意の SDK を使用してジョブを送信することができます｡ ジョブの進捗状況と状態は､Event Grid でイベントを監視することによって取得できます｡ 詳しくは､｢[EventGrid を使ってイベントを監視する](job-state-events-cli-how-to.md )｣を参照してください｡

### <a name="jobs-definition"></a>ジョブの定義

次の表は、ジョブのプロパティとその定義を示しています。

|Name|説明|
|---|---|
|id|リソースの完全修飾リソース ID。|
|name   |リソースの名前。|
|properties.correlationData |顧客指定の関連付けデータで (変更不可)､ジョブおよびJobOutput の状態変更の通知の一部として返されます｡ 詳細は、[イベント スキーマ](media-services-event-schemas.md)を参照してください｡<br/><br/>プロパティは､マルチテナントでの Media Services の使用にも利用できます｡ ジョブにはテナント ID を入力できます｡ |
|properties.created |ジョブを作成した UTC 日時で、'YYYY-MM-DDThh:mm:ssZ' の形式。|
|properties.description |ジョブに関する顧客指定の説明 (省略可能)｡|
|properties.input|ジョブに対する入力｡|
|properties.lastModified    |ジョブが最後に更新された UTC 日時で、'YYYY-MM-DDThh:mm:ssZ' の形式。|
|properties.outputs|ジョブの出力。|
|properties.priority    |ジョブの処理の優先順位。 優先順位が高いジョブは、優先順位が低いジョブより前に処理されます。 設定されていない場合、既定値は normal です。|
|properties.state   |ジョブの現在の状態。|
|type   |リソースの種類。|

完全な定義については、「[Jobs](https://docs.microsoft.com/rest/api/media/jobs)」(ジョブ) をご覧ください。

> [!NOTE]
> ジョブの定義は Update 操作をサポートしていますが､ジョブの送信後に変更できるプロパティは説明と優先順位だけです｡ また､優先順位の変更は､そのジョブがキュー状態にある場合にのみ有効です｡ ジョブの処理がすでに開始されているか､完了している場合､優先順位の変更は適用されません｡

### <a name="pagination"></a>改ページ位置の自動修正

Media Services v3 では､Jobs の改ページ位置を変更できます｡

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 

コレクションのページング中にジョブが作成または削除された場合、その変更は返される結果に反映されます (コレクション内の、ダウンロードされていない部分に対する変更の場合)。 

次の C# 例は、アカウント内のすべてのジョブを列挙する方法を示しています。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST の例については､[Jobs - List](https://docs.microsoft.com/rest/api/media/jobs/list) を参照してください｡


## <a name="next-steps"></a>次の手順

[ビデオ ファイルのストリーミング](stream-files-dotnet-quickstart.md)
