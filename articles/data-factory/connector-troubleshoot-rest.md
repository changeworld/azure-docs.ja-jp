---
title: REST コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics での REST コネクタに関する問題のトラブルシューティング方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390395"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse での REST コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse での REST コネクタに関する一般的な問題のトラブルシューティングのための提案を示します。

## <a name="error-code-restsinkcallfailed"></a>エラー コード:RestSinkCallFailed

- **メッセージ**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**: このエラーは、データ ファクトリまたは Synapse のパイプラインが HTTP プロトコル経由で REST エンドポイントと通信し、要求操作が失敗した場合に発生します。

- **推奨事項**: エラー メッセージの HTTP 状態コードまたはメッセージを確認し、リモート サーバーの問題を修正します。

## <a name="error-code-restsourcecallfailed"></a>エラー コード: RestSourceCallFailed

- **メッセージ**: `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **原因**:このエラーは Azure Data Factory が HTTP プロトコル経由で REST エンドポイントと通信し、要求操作が失敗した場合に発生します。

- **推奨事項**: エラー メッセージの HTTP 状態コード、要求 URL、または応答ペイロードを確認し、リモート サーバーの問題を修正します。

## <a name="error-code-restsinkunsupportedcompressiontype"></a>エラー コード: RestSinkUNSupportedCompressionType

- **メッセージ**: `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **推奨事項**: REST シンクでサポートされている圧縮の種類を確認します。

## <a name="unexpected-network-response-from-the-rest-connector"></a>REST コネクタからの予期しないネットワーク応答

- **現象**:エンドポイントは、REST コネクタから予期しない応答 (400、401、403、500) を受け取ることがあります。

- **原因**:REST ソース コネクタは、HTTP 要求を構築するときに、リンク サービス/データセット/コピー元からの URL および HTTP メソッド、ヘッダー、本文をパラメーターとして使用します。 この問題は、指定された 1 つ以上のパラメーターにおける何らかの誤りが原因で発生している可能性があります。

- **解決方法**: 
    - コマンド プロンプト ウィンドウで "curl" を使用して、パラメーターが原因であるかどうかを確認します (**Accept** ヘッダーと **User-Agent** ヘッダーを常に含める必要があります)。
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      コマンドが同じ予期しない応答を返す場合は、予期される応答を返すまで、"curl" を使って上記のパラメーターを修正してください。 

      "curl--help" を使用して、より高度な方法でコマンドを使用することもできます。

    - REST コネクタのみが予期しない応答を返す場合、詳細なトラブルシューティングについて、Microsoft サポートにお問い合わせください。
    
    - "curl" は SSL 証明書の検証問題を再現するのに適さない場合があることに注意してください。 一部のシナリオでは、SSL 証明書の検証に問題が発生せず、"curl" コマンドが正常に実行されました。 ただし、同じ URL をブラウザーで実行すると、クライアントがサーバーとの信頼関係を確立するための SSL 証明書は実際には返されません。

      上記のケースでは、**Postman** や **Fiddler** などのツールを使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
