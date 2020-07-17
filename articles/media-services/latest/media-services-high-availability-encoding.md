---
title: Azure Media Services の高可用性エンコード
description: リージョンのデータセンターの停止や障害が発生した場合に、セカンダリ Media Services アカウントにフェールオーバーする方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934196"
---
# <a name="media-services-high-availability-encoding"></a>Media Services の高可用性エンコード 

Azure Media Services エンコード サービスは、リージョンのバッチ処理プラットフォームであり、現在は 1 つのリージョン内の高可用性に合わせて設計されていません。 現在、リージョンのデータセンターが停止した場合、基となるコンポーネントや依存するサービス (ストレージ、SQL など) に障害が発生した場合、エンコード サービスにはサービスの即時フェールオーバー機能がありません。 この記事では、Media Services をデプロイして、フェールオーバーを備えた高可用アーキテクチャを維持し、アプリケーションに最適な可用性を確保する方法について説明します。

この記事で説明されているガイドラインとベスト プラクティスに従うことで、1 つのリージョンで停止が発生した場合に、エンコード エラー、遅延のリスクを下げ、復旧時間を最小限に抑えることができます。

## <a name="how-to-build-a-cross-regional-encoding-system"></a>リージョン間のエンコード システムを構築する方法

* 2 つ (以上) の Azure Media Services アカウントを[作成](create-account-cli-how-to.md)します。

    2 つのアカウントは所属するリージョンが異なっている必要があります。 詳細については、「[Azure Media Services サービスがデプロイされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)」を参照してください。
* ジョブを送信する予定のリージョンにメディアをアップロードします。 エンコードを開始する方法の詳細については、「[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)」または「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」を参照してください。

    その後、[ジョブ](transforms-jobs-concept.md)を別のリージョンに再送信する必要がある場合、JobInputHttp を使用するか、[Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) を使用してソース アセット コンテナーから代替リージョンのアセット コンテナーにデータをコピーします。
* Azure Event Grid を介して各アカウントで JobStateChange メッセージをサブスクライブします。 詳細については、次を参照してください。

    * [オーディオ分析サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)からは、Azure Event Grid メッセージが何らかの理由で遅延した場合のフォールバックを追加するなど、Azure Event Grid でジョブを監視する方法がわかります。
    * [Media Services 用の Azure Event Grid スキーマ](media-services-event-schemas.md)
    * [Azure portal または CLI を使用してイベントに登録する](reacting-to-media-services-events.md) (EventGrid Management SDK を使用して行うこともできます)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (ネイティブで Media Services イベントをサポートしています)。

    Azure Functions を介して Event Grid イベントを使用することもできます。
* [ジョブ](transforms-jobs-concept.md)を作成する場合:

    * 現在使用しているアカウントの一覧からアカウントをランダムに選択します (通常、この一覧には両方のアカウントが含まれますが、問題が検出された場合は、アカウントを 1 つだけ含めることができます)。 一覧が空の場合は、オペレーターが調査できるようにアラートを生成します。
    * 一般的なガイダンスとしては、[JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) ごとに[メディア占有ユニット](media-reserved-units-cli-how-to.md)が 1 つ必要になります (JobOutput ごとに 3 つのメディア占有ユニットが推奨される [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) を使用している場合を除く)。
    * 選択したアカウントのメディア占有ユニット (MRU) の数を取得します。 現在の**メディア占有ユニット**の数がまだ最大値に達していない場合は、ジョブに必要な数の MRU を追加し、サービスを更新します。 ジョブの送信レートが高く、最大に達していることを確認するために MRU に対して頻繁にクエリを実行している場合は、その値に分散キャッシュを使用し、適切なタイムアウトを設定します。
    * 実行中のジョブ数のカウントを保持します。

* ジョブがスケジュールされた状態に到達したことを示す通知を JobStateChange ハンドラーが受け取ったら、ジョブがスケジュールの状態になった時刻と、使用されているリージョンとアカウントを記録します。
* ジョブが処理中の状態に達したという通知を JobStateChange ハンドラーが受け取ったら、ジョブのレコードに処理中とマークします。
* ジョブが完了、エラー発生、またはキャンセル状態になったという通知を JobStateChange ハンドラーがを受け取ったら、ジョブのレコードに最終とマークし、実行中のジョブ カウントをデクリメントします。 選択したアカウントのメディア占有ユニットの数を取得し、現在の MRU 番号を実行中のジョブ カウントと比較します。 実行中のカウントが MRU のカウントより少ない場合は、それをデクリメントしてサービスを更新します。
* ジョブのレコードを定期的に確認する別のプロセスを用意します
    
    * 特定のリージョンについて、スケジュールされた状態のジョブのうち、適切な時間内に処理中の状態に進まないものがある場合は、現在使用されているアカウントの一覧からそのリージョンを削除します。  ビジネスの要件によっては、このようなジョブをすぐに取り消して、他のリージョンに再送信することもできます。 また、次の状態に移行するための時間を増やすこともできます。
    * アカウントに構成されているメディア占有ユニットの数と送信速度によっては、キューに入れられた状態のジョブがまだ処理のために選択されていない可能性もあります。  キューに入れられた状態のジョブの一覧がリージョンで許容される上限を超えた場合、そのようなジョブをキャンセルしたり、他のリージョンに送信したりできます。  ただし、現在の負荷に対して十分なメディア占有ユニットがアカウントで構成されていないことを示す兆候の可能性があります。  必要に応じて、Azure サポートを通して、メディア占有ユニットの割当量を増やすように要求できます。
    * リージョンがアカウントの一覧から削除された場合、一覧に戻す前に回復させるか観察してください。  一定の時間が経過したらアカウントを一覧に戻すか、Azure Media Services に影響を与える可能性のある停止期間がないかオペレーターが Azure の通信を観察することによって、リージョンの正常性は、リージョンの既存ジョブから監視できます (キャンセルされておらず、再送信されていない場合)。
    
MRU のカウントが多すぎることがわかった場合は、デクリメント ロジックを定期的なタスクに移動します。 ジョブ前のロジックで、実行中のカウントを現在の MRU のカウントと比較して、MRU を更新する必要があるかどうかを確認します。

## <a name="next-steps"></a>次のステップ

* [ビデオオンデマンドのリージョン間ストリーミングを構築する](media-services-high-availability-streaming.md)
* [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-media-services)を確認する
