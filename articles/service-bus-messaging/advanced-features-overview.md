---
title: Azure Service Bus メッセージング - 高度な機能
description: この記事では、Azure Service Bus の高度な機能の概要について説明します。
ms.topic: overview
ms.date: 06/11/2021
ms.openlocfilehash: 80fab956c182b6f934a767f2dffc09ca2d0c0214
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112034850"
---
# <a name="azure-service-bus---advanced-features"></a>Azure Service Bus - 高度な機能
Service Bus には、より複雑なメッセージングの問題を解決できる高度な機能があります。 この記事では、これらの機能のいくつかについて説明します。

## <a name="message-sessions"></a>メッセージ セッション
Service Bus の先入れ先出し (FIFO) 処理を作成するには、セッションを使用します。 メッセージ セッションでは、関連メッセージのバインドなしシーケンスの排他的な順序指定処理が可能です。 高スケールな高可用性システムでセッションを処理できるようにするために、セッション機能によってセッション状態を保存することもできます。これにより、セッションはハンドラー間を安全に移動できます。 詳細については、「[メッセージ セッション: 先入れ先出し (FIFO)](message-sessions.md)」を参照してください。

## <a name="autoforwarding"></a>自動転送
自動転送機能は、キューまたはサブスクリプションを同じ名前空間内にある別のキューまたはトピックにチェーンします。 この機能を使用すると、Service Bus はキューまたはサブスクリプションからターゲットのキューまたはトピックにメッセージを自動的に移動します。 そうした移動はすべてトランザクションとして実行されます。 詳細については、「[自動転送を使用した Service Bus エンティティのチェーン](service-bus-auto-forwarding.md)」を参照してください。

## <a name="dead-letter-queue"></a>配信不能キュー
Service Bus のすべてのキューおよびトピック サブスクリプションには、配信不能キュー (DLQ) が関連付けられています。 DLQ は、次の条件を満たすメッセージを保持します。 

- どの受信者にも正常に配信できない。
- タイムアウトした。
- 受信側アプリケーションによって明示的に一時保留されている。 

配信不能キュー内のメッセージには、それらがそこに配置された理由が注釈として付けられます。 配信不能キューには特殊なエンドポイントがありますが、その点以外は通常のキューと同様に動作します。 アプリケーションやツールは、DLQ を参照したり、そこからデキューしたりできます。 配信不能キューから自動転送することもできます。 詳しくは、「[Service Bus の配信不能キューの概要](service-bus-dead-letter-queues.md)」をご覧ください。

## <a name="scheduled-delivery"></a>スケジュールされた配信
メッセージを遅延処理されるようにキューまたはトピックに送信し、メッセージを処理できるようになる時間を設定できます。 スケジュール設定されたメッセージはキャンセルすることもできます。 詳細については、「[スケジュールされたメッセージ](message-sequencing.md#scheduled-messages)」を参照してください。

## <a name="message-deferral"></a>メッセージ遅延
キューまたはサブスクリプション クライアントは、受信したメッセージの取得を遅延させることができます。 メッセージは想定外の順序でポストされている場合があり、クライアントは別のメッセージを受信するまで待機する必要があります。 遅延メッセージはキューまたはサブスクリプション内に残り、サービスによって割り当てられたシーケンス番号を使用して明示的に再アクティブ化する必要があります。 詳細については、「[メッセージの遅延](message-deferral.md)」を参照してください。

## <a name="batching"></a>バッチ処理
クライアント側のバッチ処理により、キューまたはトピックのクライアントは一連のメッセージを蓄積し、まとめて転送できます。 これは、多くの場合、帯域幅を節約するために、またはスループットを向上させるために行われます。 詳細については、「[クライアント側のバッチ処理](service-bus-performance-improvements.md#client-side-batching)」を参照してください。

## <a name="transactions"></a>トランザクション
トランザクションにより、複数の操作が 1 つの実行スコープにグループ化されます。 Service Bus では、単一トランザクションのスコープ内の複数のメッセージング エンティティに対してグループ化操作を実行できます。 メッセージ エンティティは、キュー、トピック、またはサブスクリプションとすることができます。 詳しくは、「[Service Bus のトランザクション処理の概要](service-bus-transactions.md)」を参照してください。

## <a name="autodelete-on-idle"></a>アイドル状態時の自動削除
アイドル状態時の自動削除機能を使用すると、アイドル間隔を指定できます。この間隔が経過すると、キューまたはトピックス サブスクリプションが自動的に削除されます。 最小時間は、5 分です。 

## <a name="duplicate-detection"></a>重複検出
重複検出機能を使用すると、送信側は同じメッセージを再送信し、重複する可能性のあるものをブローカーが削除できます。 詳しくは、「[重複検出](duplicate-detection.md)」をご覧ください。

## <a name="geo-disaster-recovery"></a>geo ディザスター リカバリー
Azure リージョンでダウンタイムが発生した場合、ディザスター リカバリー機能により、異なるリージョンまたはデータ センター内でメッセージ処理を継続できます。 この機能は、セカンダリ リージョン内で使用可能な名前空間の構造ミラーを保持し、名前空間 ID をセカンダリ名前空間に切り替えることができるようにします。 既にポストされているメッセージは、可用性エピソードが収まったら復旧できるよう、以前のプライマリ名前空間に残ります。 詳細については、「[Azure Service Bus の geo ディザスター リカバリー](service-bus-geo-dr.md)」を参照してください。

## <a name="security"></a>Security
Service Bus は、[AMQP 1.0](service-bus-amqp-overview.md) 標準および [HTTP または REST](/rest/api/servicebus/) プロトコルとそれぞれのセキュリティ機構 (トランスポート レベルのセキュリティ (TLS) など) をサポートしています。 [Shared Access Signature](service-bus-sas.md) または [Azure Active Directory](service-bus-authentication-and-authorization.md) のロール ベース セキュリティを使用して、クライアントにアクセスを許可することができます。 

不要なトラフィックから保護するために、Service Bus には、IP ファイアウォールや、仮想ネットワークとの統合などの[セキュリティ機能](network-security.md)が用意されています。 

## <a name="next-steps"></a>次のステップ
これらの Service Bus 機能の使い方を示す「[Service Bus メッセージングのサンプル](service-bus-samples.md)」を参照してください。
