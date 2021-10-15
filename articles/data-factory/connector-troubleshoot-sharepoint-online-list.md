---
title: SharePoint Online リスト コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics で SharePoint Online リスト コネクタの問題を解決する方法を説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 2407521ef6abcea5a27f7810af527e47a2739410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390400"
---
# <a name="troubleshoot-the-sharepoint-online-list-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse で SharePoint Online リスト コネクタのトラブルシューティングを行います

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse で、SharePoint Online リスト コネクタのよくある問題を解決するための方法を説明します。

## <a name="error-code-sharepointonlineauthfailed"></a>エラー コード:SharePointOnlineAuthFailed

- **メッセージ**: `The access token generated failed, status code: %code;, error message: %message;.`

- **原因**:サービス プリンシパル ID とキーが正しく設定されていない可能性があります。

- **推奨事項**:登録されているアプリケーション (サービス プリンシパル ID) とキーが正しく設定されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
