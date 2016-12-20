---
title: "Service Bus に関する FAQ | Microsoft Docs"
description: "Azure Service Bus についてよく寄せられる質問 (FAQ) とその回答を紹介します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;juconway
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b350bac36f1d46c97da37a2807ead8c9c732d69a


---
# <a name="service-bus-faq"></a>Service Bus に関する FAQ
この記事では、Microsoft Azure Service Bus についてよく寄せられる質問 (FAQ) とその回答を紹介します。 Azure の価格およびサポートに関する一般的な情報については、「 [Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 」も参照してください。 次のトピックが含まれています。

* [Azure Service Bus メッセージングに関する一般的な質問](#general-questions-about-azure-service-bus-messaging)
* [Service Bus のベスト プラクティス](#service-bus-best-practices)
* [Service Bus 料金](#service-bus-pricing)
* [Service Bus のクォータ](#service-bus-quotas)
* [サブスクリプションと名前空間の管理](#subscription-and-namespace-management)
* [トラブルシューティング](#service-bus-troubleshooting)

## <a name="general-questions-about-azure-service-bus-messaging"></a>Azure Service Bus メッセージングに関する一般的な質問
### <a name="what-is-azure-service-bus-messaging"></a>Azure Service Bus メッセージングとは何ですか。
[Azure Service Bus メッセージング](service-bus-messaging-overview.md)は、分離されたシステム間でデータを送信できるようにする非同期メッセージング クラウド プラットフォームです。 Microsoft では、この機能をサービスとして提供しています。つまり、この機能を使用するために独自にハードウェアをホストする必要はありません。

### <a name="what-is-a-service-bus-namespace"></a>Service Bus 名前空間とは何ですか。
[名前空間](service-bus-create-namespace-portal.md)は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。 Service Bus を使用するには、Service Bus 名前空間を作成する必要があります。これは、最初に実行する手順の 1 つです。

### <a name="what-is-an-azure-service-bus-queue"></a>Azure Service Bus キューとは何ですか。
[Service Bus キュー](service-bus-queues-topics-subscriptions.md)は、メッセージが格納されるエンティティです。 キューは、複数のアプリケーションがある場合や、互いにやり取りする必要がある分散アプリケーションの複数の部分がある場合に特に有用です。 キューは、複数の製品 (メッセージ) の受け取り (受信) と発送 (送信) が行われる点で、集配送センターに似ています。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Service Bus トピックおよびサブスクリプションとは何ですか。
トピックは、キューとして視覚化できます。複数のサブスクリプションを使用している場合、トピックはより多機能なメッセージング モデルになります (基本的には一対多通信ツール)。 この発行/サブスクライブ モデル (または *pub/sub*) により、複数のサブスクリプションを持つトピックにメッセージを送信するアプリケーションで、そのメッセージが複数のアプリケーションによって受信されるように設定できます。

### <a name="what-is-a-partitioned-entity"></a>パーティション分割されたエンティティとは何ですか。
従来のキューまたはトピックは、単一のメッセージ ブローカーで処理されて 1 つのメッセージング ストアに格納されます。 [パーティション分割されたキューまたはトピック](service-bus-partitioning.md)は、複数のメッセージ ブローカーによって処理され、複数のメッセージング ストアに格納されます。 そのため、パーティション分割されたキューまたはトピックの全体のスループットは、単一のメッセージ ブローカーまたはメッセージング ストアのパフォーマンスによって制限されなくなりました。 また、1 つのメッセージング ストアが一時的に停止しても、パーティション分割されたキューまたはトピックは使用することができます。

パーティション分割されたエンティティを使用する場合、順序が保証されないことに注意してください。 あるパーティションが使用できなくなった場合は、他のパーティションからメッセージの送受信を行うことができます。

## <a name="service-bus-best-practices"></a>Service Bus のベスト プラクティス
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure Service Bus のベスト プラクティスを教えてください。
* [「Service Bus のブローカー メッセージングを使用したパフォーマンス向上のためのベスト プラクティス」][Service Bus の仲介型メッセージングを使用したパフォーマンス向上のためのベスト プラクティス] - この記事では、ブローカー メッセージを交換するときのパフォーマンスを最適化する方法について説明します。

### <a name="what-should-i-know-before-creating-messaging-entities"></a>メッセージング エンティティを作成する前に知っておく必要があることは何ですか。
キューとトピックの次のプロパティは変更できません。 エンティティをプロビジョニングするときはこの点に注意してください。代替の新しいエンティティを作成しない限り、これらのプロパティは変更できません。

* サイズ
* パーティション分割
* セッション
* 重複検出
* Express エンティティ

## <a name="service-bus-pricing"></a>Service Bus の価格
このセクションでは、Service Bus の価格体系についてよく寄せられる質問 (FAQ) とその回答を紹介します。 Microsoft Azure の全般的な価格情報については、 [Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) も参照してください。 Service Bus の価格の詳細については、 [Service Bus の価格の詳細](https://azure.microsoft.com/pricing/details/service-bus/)に関するページを参照してください。

### <a name="how-do-you-charge-for-service-bus"></a>Service Bus の課金方法を教えてください
Service Bus の価格の詳細については、[Service Bus の価格の詳細][料金の概要]に関するページを参照してください。 既に説明した価格に加え、ご利用のアプリケーションがプロビジョニングされているデータ センターから外部に送信される関連データ転送に料金が発生します。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Service Bus でデータ転送の対象となる用途と 対象外の用途を教えてください
特定の Azure リージョン内でのデータ転送はすべて無料です。 リージョンの外部へのデータ転送では、北米リージョンとヨーロッパ リージョンからの送信の場合は 1 GB につき $0.15、アジア太平洋リージョンからの送信の場合は 1 GB につき $0.20 の送信料がかかります。 受信データ転送はすべて無料です。

### <a name="does-service-bus-charge-for-storage"></a>Service Bus ではストレージに対して課金されますか
いいえ、Service Bus ではストレージに対して課金されません。 ただし、キューまたはトピックごとに保持できる最大データ量を制限するクォータはあります。 次の FAQ を参照してください。

## <a name="service-bus-quotas"></a>Service Bus のクォータ
Service Bus の制限とクォータの一覧については、[クォータの概要][クォータの概要] に関するページを参照してください。

### <a name="does-service-bus-have-any-usage-quotas"></a>Service Bus に使用量クォータはありますか
マイクロソフトは、既定で、任意のクラウド サービスに関して、お客様の全サブスクリプション全体で算出される月単位の総使用量クォータを設定しています。 実際のニーズがこれらの制限を上回る可能性があることは認識しておりますので、お客様のニーズを把握し、適宜制限を調整できるようにするためにも、お気軽にカスタマー サービスまでお問い合わせください。 Service Bus の総使用量クォータは次のとおりです。

* 50 億メッセージ

マイクロソフトは、ある月に使用量クォータを超えた場合に、お客様のアカウントを無効にする権利を保有しています。ただし、その場合は電子メールでお客様にその旨をお知らせし、何度かお客様に連絡を試みたうえで、措置を講じることといたします。 このようなクォータを超えたお客様についても、その超過分が課金の対象となります。

Azure の他のサービスと同様、Service Bus では、リソースが公平に使用されるように一連のクォータを適用します。 このサービスで適用される使用量クォータは次のとおりです。

#### <a name="queuetopic-size"></a>キュー/トピック サイズ
キューまたはトピックの最大サイズは、その作成時に指定します。 このクォータには、1 GB、2 GB、3 GB、4 GB、5 GB のいずれかの値を指定できます。 最大サイズに達すると、それ以上の受信メッセージは拒否され、呼び出し元のコードが例外を受け取ります。

#### <a name="naming-restrictions"></a>名前に関する制限
Service Bus 名前空間の名前は 6 ～ 50 文字にする必要があります。 キュー、トピック、サブスクリプションごとの文字数制限は 1 ～ 50 文字です。

#### <a name="number-of-concurrent-connections"></a>同時接続数
キュー/トピック/サブスクリプション - キュー/トピック/サブスクリプションに対する同時 TCP 接続数は 100 に制限されています。 このクォータに達すると、その後の接続要求は拒否され、呼び出し元のコードが例外を受け取ります。 Service Bus は、メッセージング ファクトリによって作成されたクライアントのいずれかでアクティブな操作が保留状態になっている場合や、操作が完了してからの経過時間が 60 秒未満である場合、メッセージング ファクトリごとに 1 つの TCP 接続を維持します。 REST 操作は、TCP 同時接続数に加算されません。

#### <a name="number-of-topicsqueues-per-service-namespace"></a>サービスの名前空間あたりのトピック/キュー数
サービスの名前空間のトピック/キュー (永続的なストレージを利用したエンティティ) の最大数は 10,000 に制限されます。 このクォータに達すると、以後、サービスの名前空間に対する新しいトピック/キューの作成要求は拒否されます。 この場合、Azure クラシック ポータルにエラー メッセージが表示されるか、または呼び出し元のクライアント コードが例外を受け取ります。どちらになるかは、作成要求をポータルとクライアント コードのどちらで行ったかによって決まります。

### <a name="message-size-quotas"></a>メッセージ サイズ クォータ
#### <a name="queuetopicsubscription"></a>キュー/トピック/サブスクリプション
**メッセージ サイズ** - メッセージ ヘッダーを含む合計サイズは、メッセージにつき 256 KB に制限されます。

**メッセージ ヘッダーのサイズ** - 各メッセージ ヘッダーは 64 KB に制限されます。

上記のサイズ クォータを超えるメッセージは拒否され、呼び出し元のコードが例外を受け取ります。

**トピックごとのサブスクリプション数** - トピックごとの最大サブスクリプション数は 2,000 に制限されます。 このクォータに達すると、以後、そのトピックに対する新しいサブスクリプションの作成要求は拒否されます。 この場合、Azure クラシック ポータルにエラー メッセージが表示されるか、または呼び出し元のクライアント コードが例外を受け取ります。どちらになるかは、作成要求をポータルとクライアント コードのどちらで行ったかによって決まります。

**トピックごとの SQL フィルター数** - トピックごとの最大 SQL フィルター数は 2,000 に制限されます。 このクォータに達すると、その後、そのトピックに追加のフィルターを作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。

**トピックごとの相関フィルター数** - トピックごとの相関フィルターの最大数は 100,000 に制限されます。 このクォータに達すると、その後、そのトピックに追加のフィルターを作成する要求は拒否され、呼び出し元のコードが例外を受け取ります。

## <a name="subscription-and-namespace-management"></a>サブスクリプションと名前空間の管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>別の Azure サブスクリプションに名前空間を移行する方法を教えてください
PowerShell コマンドを使用して、名前空間を Azure サブスクリプション間で移行することができます ([この記事][ここ] を参照してください)。 操作を実行するには、名前空間が既にアクティブになっている必要があります。 また、コマンドを実行するユーザーは、ソースとターゲットの両方のサブスクリプションの管理者である必要があります。

## <a name="service-bus-troubleshooting"></a>Service Bus のトラブルシューティング
[例外の概要][例外の概要]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions"></a>Azure Service Bus のメッセージング API によって生成される例外とその推奨される操作をいくつか教えてください
メッセージング API によって生成される例外は、次のカテゴリに分類されます。

* ユーザー コードのエラー
* セットアップ/構成エラー
* 一時的な例外
* その他の例外

「[Service Bus メッセージングの例外][例外の概要]」に、いくつかの例外と推奨されるアクションが説明されています。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Shared Access Signature とは何ですか。どの言語で署名の生成がサポートされますか。
Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 Node、PHP、Java、および C\# で独自の署名を生成する方法については、[「Shared Access Signatures」][Shared Access Signatures]を参照してください。

## <a name="next-steps"></a>次のステップ
Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Azure Service Bus Premium メッセージングの概要 (ブログの投稿)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure Service Bus Premium メッセージングの概要(Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [Azure Service Bus アーキテクチャの概要](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)

[Service Bus の仲介型メッセージングを使用したパフォーマンス向上のためのベスト プラクティス]: service-bus-performance-improvements.md
[Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス]: service-bus-outages-disasters.md
[料金の概要]: https://azure.microsoft.com/pricing/details/service-bus/
[クォータの概要]: service-bus-quotas.md
[ここ]: service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription
[例外の概要]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas-overview.md



<!--HONumber=Nov16_HO3-->


