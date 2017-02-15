---
title: "x12 の追跡スキーマ | Microsoft Docs"
description: "X12 の追跡スキーマについての詳細情報"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>X12 の追跡スキーマ
サポートされている X12 の追跡スキーマは、以下のとおりです。

* X12 トランザクション セットの追跡スキーマ
* X12 トランザクション セットの受信確認の追跡スキーマ
* X12 インターチェンジの追跡スキーマ
* X12 インターチェンジの受信確認の追跡スキーマ
* X12 機能グループの追跡スキーマ
* X12 機能グループの受信確認の追跡スキーマ

## <a name="x12-transaction-set-tracking-schema"></a>X12 トランザクション セットの追跡スキーマ
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "", 
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信パートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信パートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。  インターチェンジ制御番号を示します |
| functionalGroupControlNumber |省略可能、string。  機能制御番号を示します |
| transactionSetControlNumber |省略可能、string。  トランザクション セット制御番号を示します |
| CorrelationMessageId |省略可能、string。  関連付けメッセージ ID を示します。  関連付け ID は、{AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} の組み合わせです |
| messageType |省略可能、string。 トランザクション セットまたはドキュメントの種類を示します |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| isTechnicalAcknowledgmentExpected |必須、boolean。  X12 契約で技術確認が構成されているか構成されていないかを示します |
| isFunctionalAcknowledgmentExpected |必須、boolean。  X12 契約で機能確認が構成されているか構成されていないかを示します |
| needAk2LoopForValidMessages |必須、boolean。  - 有効なメッセージのために AK2 ループが必要かどうか。 - 型は boolean |
| segmentsCount |省略可能、int。  X12 トランザクション セット内のセグメントの数を示します |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 トランザクション セットの受信確認の追跡スキーマ
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信者のパートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信者のパートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。  機能確認のインターチェンジ制御番号を示します。 パートナーに送信されたメッセージに対する機能確認が受信された場合に、送信側にのみ値が設定されます |
| functionalGroupControlNumber |省略可能、string。  機能確認の機能グループ制御番号を示します。 パートナーに送信されたメッセージに対する機能確認が受信された場合に、送信側にのみ値が設定されます |
| isaSegment |省略可能、string。  メッセージの ISA セグメントを示します。 パートナーに送信されたメッセージに対する機能確認が受信された場合に、送信側にのみ値が設定されます |
| gsSegment |省略可能、string。  メッセージの GS セグメントを示します。 パートナーに送信されたメッセージに対する機能確認が受信された場合に、送信側にのみ値が設定されます |
| respondingfunctionalGroupControlNumber |省略可能、string。  応答インターチェンジ制御番号を示します |
| respondingFunctionalGroupId |省略可能、string。 応答機能グループ ID を示します (受信確認の AK101 にマップされます) |
| respondingtransactionSetControlNumber |省略可能、string。  応答トランザクション セット制御番号を示します |
| respondingTransactionSetId |省略可能、string。  応答トランザクション セット ID を示します (受信確認の AK201 にマップされます) |
| StatusCode |必須、boolean。  トランザクション セットの受信確認の状態コードを示します |
| segmentsCount |必須、enum。  受信確認の状態コードを示します。  許可されている値は、Accepted、Rejected、AcceptedWithErrros です |
| processingStatus |必須、enum。  受信確認の処理状態を示します。  許可されている値は、Received、Generated、Sent です |
| CorrelationMessageId |省略可能、string。  関連付けメッセージ ID を示します。  関連付け ID は、{AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} の組み合わせです |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| ak2Segment |省略可能、string。 ak2 セグメントを示します。 ak2 セグメントは、受信された機能グループ内のトランザクション セットに対する受信確認を示します |
| ak3Segment |省略可能、string。 ak3 セグメントを示します。  ak3 セグメントは、データ セグメントでのエラーを報告します |
| ak5Segment |省略可能、string。 ak5 セグメントを示します。  ak5 セグメントは、AK2 セグメント内で識別されたトランザクション セットが承認されるか拒否されるかと、その理由を報告します |

## <a name="x12-interchange-tracking-schema"></a>X12 インターチェンジの追跡スキーマ
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信者のパートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信者のパートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。  インターチェンジ制御番号を示します |
| isaSegment |省略可能、string。  メッセージ ISA セグメントを示します |
| isTechnicalAcknowledgmentExpected |必須、boolean。  X12 契約で技術確認が構成されているか構成されていないかを示します |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| isa09 |省略可能、string。  X12 ドキュメントのインターチェンジ日付を示します |
| isa10 |省略可能、string。 X12 ドキュメントのインターチェンジ時刻を示します |
| isa11 |省略可能、string。 X12 インターチェンジ制御標準識別子を示します |
| isa12 |省略可能、string。  X12 インターチェンジ制御バージョン番号を示します |
| isa14 |省略可能、string。  X12 受信確認が要求されることを示します |
| isa15 |省略可能、string。  テストか実稼働かのインジケーターを示します |
| isa16 |省略可能、string。 要素の区切り記号を示します |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 インターチェンジの受信確認の追跡スキーマ
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信者のパートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信者のパートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。  技術確認のインターチェンジ制御番号を示します。 パートナーから受信した技術確認は、この値になります |
| isaSegment |省略可能、string。  技術確認の ISA セグメントを示します。 パートナーから受信した技術確認は、この値になります |
| respondingInterchangeControlNumber |省略可能、string。  技術確認のインターチェンジ制御番号を示します。 パートナーから受信した技術確認は、この値になります |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| StatusCode |必須、enum。  インターチェンジ受信確認の状態コードを示します。  許可されている値は、Accepted、Rejected、AcceptedWithErrros です |
| processingStatus |必須、enum。  受信確認の状態を示します。  許可されている値は、Received、Generated、Sent です |
| ta102 |省略可能、string。 インターチェンジの日付を示します |
| ta103 |省略可能、string。 インターチェンジの時刻を示します |
| ta105 |省略可能、string。 インターチェンジのノート コードを示します |

## <a name="x12-functional-group-tracking-schema"></a>X12 機能グループの追跡スキーマ
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信者のパートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信者のパートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。 インターチェンジ制御番号を示します |
| functionalGroupControlNumber |省略可能 - 機能制御番号 - string 型 |
| gsSegment |省略可能、string。  メッセージ GS セグメントを示します |
| isTechnicalAcknowledgmentExpected |必須、boolean。  X12 契約で技術確認が構成されているか構成されていないかを示します |
| isFunctionalAcknowledgmentExpected |必須、boolean。  X12 契約で機能確認が構成されているか構成されていないかを示します |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| gs01 |省略可能、string。 機能識別コードを示します |
| gs02 |省略可能、string。 アプリケーション送信者のコードを示します |
| gs03 |省略可能、string。 アプリケーション受信者のコードを示します |
| gs04 |省略可能、string。 機能グループの日付を示します |
| gs05 |省略可能、string。 機能グループの時刻を示します |
| gs07 |省略可能、string。 担当機関コードを示します |
| gs08 |省略可能、string。 バージョン/リリース/業界識別コードを示します - string 型 |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 機能グループの受信確認の追跡スキーマ
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| プロパティ | 説明 |
| --- | --- |
| senderPartnerName |省略可能、string。  X12 メッセージの送信者のパートナー名を示します |
| receiverPartnerName |省略可能、string。  X12 メッセージの受信者のパートナー名を示します |
| senderQualifier |必須、string。  送信パートナーの修飾子を示します |
| senderIdentifier |必須、string。  送信パートナーの識別子を示します |
| receiverQualifier |必須、string。  受信パートナーの修飾子を示します |
| receiverIdentifier |必須、string。  受信パートナーの識別子を示します |
| agreementName |省略可能、string。  メッセージが解決される X12 契約の名前 |
| direction |必須、enum。  メッセージ フローの方向を示します。  許可されている値は、receive と send です |
| interchangeControlNumber |省略可能、string。 インターチェンジ制御番号を示します。 パートナーから技術確認を受信したときに、送信側に値が設定されます |
| functionalGroupControlNumber |省略可能、string。 技術確認の機能グループ制御番号を示します。 パートナーから技術確認を受信したときに、送信側に値が設定されます |
| isaSegment |省略可能 - 特定の場合にだけ、上と同じインターチェンジ制御番号が設定されます。 - string 型 |
| gsSegment |省略可能 - 特定の場合にだけ、上と同じ機能グループ制御番号が設定されます。 - string 型 |
| respondingfunctionalGroupControlNumber |省略可能 - 元の機能グループの制御番号 - string 型 |
| respondingFunctionalGroupId |省略可能 - ack の AK101 にマップされます - 機能グループ ID - string 型 |
| isMessageFailed |必須、boolean。  X12 メッセージが成功したか失敗したかを示します |
| StatusCode |必須、enum。  受信確認の状態コードを示します。 許可されている値は、Accepted、Rejected、AcceptedWithErrros です |
| processingStatus |必須、enum。  受信確認の処理状態を示します。 許可されている値は、Received、Generated、Sent です |
| ak903 |省略可能、string。 受信したトランザクション セットの数を示します |
| ak904 |省略可能、string。 識別された機能グループで承認されたトランザクション セットの数を示します |
| ak9Segment |省略可能、string。  Ak9 セグメントは、AK1 セグメントで識別された機能グループが承認されるか拒否されるかと、その理由を示します |

## <a name="next-steps"></a>次のステップ
[B2B メッセージの監視方法についての詳細情報](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[OMS ポータルでの B2B メッセージの追跡](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[カスタム追跡スキーマについての詳細情報](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[S2 の追跡スキーマについての詳細情報](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


