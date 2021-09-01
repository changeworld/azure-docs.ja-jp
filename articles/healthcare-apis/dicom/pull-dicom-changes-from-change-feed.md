---
title: 変更フィードを使用して DICOM の変更をプルする
description: この攻略ガイドでは、Azure Healthcare APIs の DICOM 変更フィードを使用して DICOM 変更をプルする方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779039"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>変更フィードを使用して DICOM の変更をプルする

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

DICOM 変更フィードでは、DICOM サービスの履歴を確認し、サービス内の作成および削除イベントに対処する機能が顧客に提供されます。 この攻略ガイドでは、変更フィードの使用方法について説明します。

変更フィードには REST API を使用してアクセスします。 これらの API については、変更フィードの使用例と共に、[DICOM 変更フィードの概要](dicom-change-feed-overview.md)に関するページで説明しています。 REST API のバージョンは、[DICOM サービスの API バージョン管理](api-versioning-dicom-service.md)に関するドキュメントで説明しているように、要求 URL で明示的に指定する必要があります。

## <a name="consume-change-feed"></a>変更フィードの使用

次の C# コード例では、DICOM クライアント パッケージを使用して変更フィードを使用する方法を示しています。

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

**ChangeFeedRetrieveService.cs** のコード例を表示してアクセスするには、[変更フィードの使用](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

この攻略ガイドでは、変更フィードの使用方法について説明します。 変更フィードを使用すると、DICOM サービスの履歴を監視できます。 DICOM サービスの詳細については、以下を参照してください

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
