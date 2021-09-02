---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: ceb52a4e3e2b66388b73bcb63b68913d8dcb467a
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830535"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage の接続文字列を取得する

先ほど、関数アプリで使用する Azure ストレージ アカウントを作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 関数をローカルで実行するときは、*local.settings.json* ファイルに設定をダウンロードすることにより、その接続を使用して同じアカウントのストレージ キューへの書き込みを行うことができます。 

1. プロジェクトのルートから、次のコマンドを実行します。`<APP_NAME>` は、前のクイックスタートの関数アプリ名に置き換えてください。 このコマンドを実行すると、ファイル内の既存の値はすべて上書きされます。

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. *local.settings.json* を開き、`AzureWebJobsStorage` という名前の値を検索します。それがストレージ アカウントの接続文字列です。 `AzureWebJobsStorage` という名前と接続文字列は、この記事の他のセクションで使用します。

> [!IMPORTANT]
> *local.settings.json* には、Azure からダウンロードされたシークレットが含まれているため、このファイルは必ずソース管理から除外してください。 ローカル関数プロジェクトで作成される *.gitignore* ファイルからは、このファイルが既定で除外されます。