---
title: "Event Hubs のよく寄せられる質問 (FAQ) | Microsoft Docs"
description: "Event Hubs の FAQ"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: a584086e459c5446a814bbca3e50ac343fa9201e
ms.openlocfilehash: f7b3974bf789df8c87254cc4186d8c7c85282aaa


---
# <a name="event-hubs-faq"></a>Event Hubs の FAQ
Event Hubs は、高スループット データ ソースや百万単位のデバイスからのデータ イベントの大規模な取り込み、保存、および処理を実行します。 Event Hubs を Service Bus キューおよびトピックと組み合わせると、 [モノのインターネット (IoT)](https://azure.microsoft.com/services/iot-hub/) のシナリオ向けの持続的なコマンドと制御のデプロイが可能になります。

この記事では、Event Hubs の価格の情報について説明し、いくつかのよく寄せられる質問に回答します。

## <a name="pricing-information"></a>料金情報
Event Hubs 料金の詳細については、「 [Event Hubs 料金](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs のイングレス イベントはどのように計算されますか。
Event Hubs に送信されたイベントは、それぞれが課金対象メッセージとしてカウントされます。 1 つの *イングレス イベント* は 64 KB 以下の単位のデータと定義されます。 サイズが 64 KB 以下のイベントは、1 つの課金対象イベントとみなされます。 イベントが 64 KB よりも大きい場合、課金対象イベントの数はイベント サイズに従って計算され、64 KB の倍数になります。 たとえば、Event Hub に送信された 8 KB のイベントは 1 つのイベントとして課金されますが、Event Hub に送信された 96 KB のメッセージは 2 つのイベントとして課金されます。

Event Hub や管理操作、チェックポイントなどの制御呼び出しで使用されるイベントは、課金対象受信イベントとしてはカウントされませんが、スループット単位の上限まで蓄積されます。

## <a name="what-are-event-hubs-throughput-units"></a>Event Hubs のスループット単位とは何ですか。
Event Hubs のスループット単位は、Azure ポータルまたは Event Hubs リソース マネージャー テンプレートによってユーザーが明示的に選択します。 スループット単位はイベント ハブ名前空間のすべての Event Hubs に適用され、それぞれのスループット単位はその名前空間に次の使用許可を与えます。

* 最大 1 MB/秒の受信イベント (Event Hub に送信されるイベント)。ただし、1 秒あたり 1000 個未満の受信イベント、管理操作、または制御 API 呼び出し。
* 最大 2 MB/秒の送信イベント (Event Hub から使用されるイベント)。
* 最大 84 GB のイベント ストレージ (既定の 24 時間の保持期間に対応できる十分なストレージ)。

Event Hubs のスループット単位は、特定の時間に選択された単位の最大数に基づいて時間単位で課金されます。

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Event Hubs のスループット単位制限はどのように適用されますか。
名前空間内のすべての Event Hubs の合計受信スループットまたは合計受信イベント レートがスループット単位の上限の総計を超過した場合は、送信側が調整され、受信クォータを超えたことを示すエラーを受信します。

名前空間内のすべての Event Hubs の合計送信スループットまたは合計送信イベント レートがスループット単位の上限の総計を超過した場合は、受信側が調整され、送信クォータが超過したことを示すエラーを受信します。 送信側でイベントの使用速度が低下しないようにするため、また受信側で Event Hubs にイベントを送信できなくなることを避けるために、受信クォータと送信クォータは別々に適用されます。

スループット単位の選択は、Event Hubs のパーティション数とは無関係であることに注意してください。 各パーティションは、最大 1 MB/秒の受信スループット (秒あたり最大 1000 個のイベント)、最大 2 MB/秒の送信スループットを提供しますが、パーティション自体の固定料金はありません。 料金は、Event Hubs 名前空間内のすべての Event Hubs に関するスループット単位の総計に対して発生します。 このパターンによって、システムで予想される最大負荷をサポートするための十分なパーティションを作成でき、システムのイベント負荷がより多くのスループット数を実際に必要とするまでスループット単位の料金は発生せず、システムに対する負荷が増加したときにシステムの構造やアーキテクチャを変更する必要はありません。

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>選択できるスループット単位の数に制限はありますか。
名前空間ごとに 20 というスループット単位の既定のクォータがあります。 サポート チケットを送信することで、スループット単位の大きなクォータを要求できます。 20 というスループット単位の制限を超える 20 ～ 100 スループット単位のバンドルを使用できます。 20 を超えるスループット単位を使用すると、サポート チケットの送信なしでスループット単位の数を変更する機能は無効になることに注意してください

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Event Hubs のイベントを 24 時間以上保持する場合に料金はかかりますか。
Event Hubs Standard レベルでは、24 時間を超えて最大 30 日間メッセージを保持できます。 保存されたイベントの合計数が選択したスループット単位数のストレージの上限 (スループット単位あたり 84 GB) を超える場合、上限を超えるサイズには公開された Azure Blob ストレージ レートの料金が発生します。 各スループット単位のストレージの上限は、スループット単位が受信の上限まで使用された場合でも、24 時間の保持期間に対するすべてのストレージ コストをカバーします。

## <a name="what-is-the-maximum-retention-period"></a>最大リテンション期間とは何ですか。
Event Hubs Standard レベルでは、現在最大 7 日間のリテンション期間をサポートしています。 Event Hubs は永続的なデータ ストアになることを目的としていません。 24 時間を超える保持期間の目的は、同じシステムでイベント ストリームを再生すると便利なシナリオ (たとえば既存データで新しい機械学習モデルのトレーニングや検証を行うこと) に対応することです。

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs のストレージ サイズはどのように計算され、課金されますか。
保存されたすべてのイベントの合計サイズは、すべての Event Hubs のイベント ヘッダーまたはディスク ストレージ構造の内部オーバーヘッドを含めて、1 日中測定されます。 1 日の終わりに、ピーク ストレージ サイズが計算されます。 1 日あたりのストレージの上限は、その日に選択されたスループット単位の最小数に基づいて計算されます (それぞれのスループット単位には 84 GB の上限が与えらえます)。 合計サイズが計算された 1 日あたりのストレージの上限を超過した場合は、超過したストレージが、Azure Blob ストレージ レート ( **Locally Redundant Storage (LRS)** ) を使用して課金されます。

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>単一の AMQP 接続を使用して、Event Hubs と Service Bus のキュー/トピック間で送受信を実行できますか。
はい。すべての Event Hubs、キュー、トピックが同じ名前空間にある限り可能です。 このため、遅延時間が 1 秒未満の双方向の仲介型接続を、費用効率が良くスケーラブルな方法で多数のデバイスに実装できます。

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>仲介型接続料金は Event Hubs に適用されますか。
送信側では、接続料金は AMQP プロトコルが使用される場合にのみ適用されます。 送信側システムまたはデバイスの数に関係なく、HTTP を使用したベントの送信には接続料金は発生しません。 AMQP を使用する場合 (たとえば、イベント ストリーミングの効率を高めたり、IoT のコマンドと制御のシナリオで双方向通信を可能にする場合) は、「 [Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/) 」ページの仲介型接続の構成とその測定方法に関する情報を参照してください。

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs の Basic レベルと Standard レベルは何が違いますか。
Event Hubs の Standard レベルは、Basic レベルはもちろん、いくつかの競合システムよりも多くの機能を提供します。 これらの機能には、24 時間を超えるリテンション期間と単一の AMQP 接続を使用する機能が含まれ、AMQP 接続では、1 秒未満の遅延時間で多数のデバイスにコマンドを送信でき、デバイスから Event Hubs にテレメトリを送信できます。 機能の一覧については、 [Event Hubs の価格の詳細](https://azure.microsoft.com/pricing/details/event-hubs/)を参照してください。

## <a name="geographic-availability"></a>ご利用可能な地域

Azure Event Hubs は、サポートされているすべての Azure リージョンで利用できます。 一覧については、「[Azure リージョン][]」ページを参照してください。  

## <a name="support-and-sla"></a>サポートと SLA
Event Hubs のテクニカル サポートは、 [コミュニティ フォーラム](https://social.msdn.microsoft.com/forums/azure/home)を通して利用できます。 課金とサブスクリプション管理のサポートは無料で提供されます。

SLA の詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) 」ページを参照してください。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次の記事を参照してください。

* [Event Hubs の概要][Event Hubs overview]
* [Event Hubs を使用する完全なサンプル アプリケーション][sample application that uses Event Hubs]

[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Azure リージョン]: https://azure.microsoft.com/regions/



<!--HONumber=Dec16_HO3-->


