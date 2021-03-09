---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812791"
---
エミュレーターでは、共有キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 このアカウントとキーのみ、エミュレーターで共有キー資格情報として使用できます。 これらは次のとおりです。

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> エミュレーターでサポートされている認証キーは、クライアント認証コードの機能をテストすることだけを目的としています。 セキュリティ機能は果たしません。 エミュレーターで運用環境のストレージ アカウントとキーを使用することはできません。 開発アカウントを運用データで使用することは避けてください。
>
> エミュレーターでは HTTP 経由の接続のみがサポートされます。 ただし、運用環境の Azure ストレージ アカウント内のリソースにアクセスする際は、HTTPS が推奨されるプロトコルです。
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>ショートカットを使用してエミュレーター アカウントに接続する

アプリケーションからエミュレーターに接続する最も簡単な方法は、ショートカット `UseDevelopmentStorage=true` を参照するアプリケーションの構成ファイル内で接続文字列を構成することです。 ショートカットは、エミュレーターの完全な接続文字列に相当します。この接続文字列で、各 Azure Storage サービスのアカウント名、アカウント キー、およびエミュレーター エンドポイントを指定します。

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

次の .NET コード スニペットは、接続文字列を受け取るメソッドからショートカットを使用する方法を示しています。 たとえば、[BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) コンストラクターが接続文字列を受け取ります。

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

スニペット内のコードを呼び出す前に、エミュレーターが実行されていることを確認します。
