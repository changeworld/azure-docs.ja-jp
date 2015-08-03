<properties 
 pageTitle="Scheduler の制限、既定値、エラー コード" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# Scheduler の制限、既定値、エラー コード

## Scheduler のクォータ、制限、既定値、調整

次の表に、Azure Scheduler の主要なクォータ、制限、既定値、および調整について説明します。

|リソース|制限についての説明|
|---|---|
|**ジョブ サイズ**|ジョブの最大サイズは 16 K です。PUT または PATCH 操作の結果としてジョブのサイズがこの制限値を超える場合、"400 正しくない要求" 状態コードが返されます。|
|**要求 URL のサイズ**|要求 URL の最大サイズは 2,048 文字です。|
|**ヘッダーの合計サイズ**|ヘッダーの合計最大サイズは 4,096 文字です。|
|**ヘッダーの数**|ヘッダーの最大数は 50 個です。|
|**本文のサイズ**|本文の最大サイズは 8,192 文字です。|
|**定期実行のスパン**|定期実行の最大スパンは 18 か月です。|
|**開始時刻までの時間**|"開始時刻までの時間" の最大値は 18 か月です。|
|**ジョブ履歴**|ジョブ履歴に格納される最大の応答本文は 2,048 バイトです。|
|**頻度**|既定の最大頻度クォータは、Free ジョブ コレクションの場合で 1 時間、Standard ジョブ コレクションの場合で 1 分です。最大頻度は、最大値よりも小さくなるようにジョブ コレクションで構成できます。ジョブ コレクション内のすべてのジョブは、ジョブ コレクションに対して設定された値に制限されます。ジョブ コレクションに対する最大頻度より高い頻度でジョブを作成しようとすると、要求は状態コード "409 競合" で失敗します。|
|**ジョブ**|既定の最大ジョブ クォータは、Free ジョブ コレクションの場合で 5 個のジョブ、Standard ジョブ コレクションの場合で 50 個のジョブです。ジョブの最大数は、ジョブ コレクションに対して構成できます。ジョブ コレクション内のすべてのジョブは、ジョブ コレクションに対して設定された値に制限されます。最大ジョブ クォータを超える数のジョブを作成しようとすると、要求は状態コード "409 競合" で失敗します。|
|**ジョブ履歴の保有期間**|ジョブ履歴は、最大で 2 か月間保持されます。|
|**完了したジョブとエラーが発生したジョブの保有期間**|完了したジョブとエラーが発生したジョブは、60 日間保持されます。|
|**タイムアウト**|HTTP アクションに対しては、30 秒間の静的 (構成不可) 要求タイムアウトがあります。長時間実行される処理の場合は HTTP 非同期プロトコルに従います。たとえば、202 をすぐに返す一方で、バックグラウンドで処理を続行します。|

## x-ms-request-id ヘッダー

Scheduler サービスに対するすべての要求は、**x-ms-request-id** という名前の応答ヘッダーを返します。このヘッダーには、要求を一意に識別する非透過の値が含まれています。

要求の形式が正しいにもかかわらず要求が常に失敗する場合は、この値を使用して Microsoft にエラーを報告することができます。このとき、x-ms-request-id の値、要求が行われたおおよその時間、サブスクリプション、クラウド サービス、ジョブ コレクション、ジョブの識別子のほかに、要求で試みた操作の種類も報告に含めてください。

## Scheduler の状態コードとエラー コード

Azure Scheduler REST API は、標準の HTTP 状態コードに加えて、拡張エラー コードとエラー メッセージを返します。拡張コードは、標準 HTTP 状態コードを置き換えるものではなく、標準 HTTP 状態コードと組み合わせて使用できる実用的な追加情報を提供します。

たとえば、HTTP 404 エラーはさまざまな原因で発生するため、拡張メッセージに追加情報を含めることで問題の解決に役立ちます。REST API から返される標準 HTTP コードの詳細については、「[サービス管理のステータス コードとエラー コード](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx)」を参照してください。サービス管理 API の REST API 操作は、[HTTP/1.1 の状態コードの定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に示されている標準の HTTP 状態コードを返します。次の表に、サービスから返される可能性のある一般的なエラーについて説明します。

|エラー コード|HTTP 状態コード|ユーザー メッセージ|
|----|----|----|
|MissingOrIncorrectVersionHeader|正しくない要求 (400)|バージョン管理ヘッダーが指定されていないか、正しく指定されていませんでした。|
|InvalidXmlRequest|正しくない要求 (400)|要求本文の XML が無効または正しく指定されていませんでした。|
|MissingOrInvalidRequiredQueryParameter|正しくない要求 (400)|この要求に対して必要なクエリ パラメーターが指定されていないか、正しく指定されていませんでした。|
|InvalidHttpVerb|正しくない要求 (400)|指定した HTTP 動詞はサーバーで認識されなかったか、このリソースに対して有効ではありません。|
|AuthenticationFailed|禁止 (403)|サーバーは要求を認証できませんでした。証明書が有効で、このサブスクリプションと関連付けられていることを確認してください。|
|ResourceNotFound|見つかりません (404)|指定したリソースは存在しません。|
|InternalError|内部サーバー エラー (500)|サーバーで内部エラーが発生しました。要求を再試行してください。|
|OperationTimedOut|内部サーバー エラー (500)|許可された時間内に操作を完了できません。|
|ServerBusy|サービスを利用できません (503)|サーバー (または内部コンポーネント) は現在要求の受信に使用できません。要求を再試行してください。|
|SubscriptionDisabled|禁止 (403)|サブスクリプションは無効の状態にあります。|
|BadRequest|正しくない要求 (400)|パラメーターが正しくありません。|
|ConflictError|競合 (409)|操作の完了を妨げる競合が発生しました。|
|TemporaryRedirect|一時リダイレクト (307)|指定したオブジェクトが見つかりません。オブジェクトの新しい場所の一時的 URI は応答の Location フィールドから取得できます。この新しい URI で元の要求を繰り返すことができます。|

API 操作は、管理サービスで定義されている追加のエラー情報も返す可能性があります。この追加のエラー情報は、応答本文で返されます。エラー応答の本文は、次に示す基本形式に従います。

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## 関連項目

 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)
 
 [管理ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)
 
 [Plans and Billing in Azure Scheduler (Azure Scheduler のプランと課金)](scheduler-plans-billing.md)
 
 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler (Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法)](scheduler-advanced-complexity.md)
 
 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)
 
 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)
 
 [Scheduler 送信認証](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO4-->