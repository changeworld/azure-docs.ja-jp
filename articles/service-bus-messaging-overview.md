<properties
	pageTitle="Service Bus メッセージングの概要 - Azure"
	description="Service Bus メッセージングクラウドでの柔軟なデータ配信"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor="mattshel"/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>


# Service Bus メッセージングクラウドでの柔軟なデータ配信

Microsoft Azure Service Bus メッセージングは信頼性の高い情報配信サービスです。このサービスの目的は、通信を容易にすることです。2 名以上の当事者が情報を交換する場合、通信機構が必要にあります。Service Bus メッセージングは仲介型またはサード パーティの通信機構です。これは、現実世界の郵便サービスと似ています。郵便サービスを利用すると、さまざまな配送保証を付けて手紙や荷物を世界のどこにでも簡単に送ることができます。

郵便配達と同様に、Azure Service Bus メッセージングでは、送信者と受信者の両方から情報を柔軟に配信できます。メッセージング サービスでは、当事者が両方とも同時にオンラインでなくても、また同じ時刻にいなくても、情報が確実に配信されるようにします。このようにメッセージングは郵便と似ており、仲介型ではない通信は通話 (キャッチホンや発信者番号通知サービスがなかった頃の電話、こうしたサービスは仲介型メッセージングに似ています) に似ています。

メッセージの送信者は、トランザクション、重複の検出、時間ベースの有効期限、バッチ処理などのさまざまな配信機能も要求できます。再配送、署名要請、住所変更、呼び戻しなどのパターンも郵便に似ています。

Service Bus メッセージングにはキューとトピックの 2 つの独立した機能があります。両方のメッセージング エンティティでは、上記のすべてのコンセプトなどをサポートします。主な違いは、トピックでは高度なコンテンツ ベースのルーティングと、複数の受信者への送信を含む配信ロジックに使用できるパブリッシュ/サブスクライブ機能をサポートすることです。

## 次のステップ

Service Bus メッセージングについての詳細は次のトピックをご覧ください。

- [Azure Service Bus Architectural Overview (Azure Service Bus アーキテクチャの概要)](fundamentals-service-bus-hybrid-solutions.md)

- [How to use Service Bus Queues ( Service Bus キューの利用方法)](service-bus-dotnet-how-to-use-queues.md)

- [How to use Service Bus Topics (Service Bus トピックの利用方法)](service-bus-dotnet-how-to-use-topics-subscriptions.md)

<!--HONumber=47-->
