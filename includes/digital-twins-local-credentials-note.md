---
author: baanders
description: Azure Digital Twins サンプルにおける DefaultAzureCredential 用のインクルード ファイル - 注意事項
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494494"
---
>[!NOTE]
> このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) を使用してユーザーを認証します。 このサンプルは、この種類の認証を使用してローカル環境内の Azure 資格情報 (ローカルの [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) からのログインや、Visual Studio または Visual Studio Code でのログインなど) を検索します。
>
> `DefaultAzureCredential` とその他の認証オプションの使用の詳細については、 [*アプリ認証コードを作成する方法*](../articles/digital-twins/how-to-authenticate-client.md)に関するページで確認します。