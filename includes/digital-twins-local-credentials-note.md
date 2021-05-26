---
author: baanders
description: Azure Digital Twins サンプルにおける DefaultAzureCredential 用のインクルード ファイル - 注意事項
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 1275e7e230fedc4fae77592bf1232e62ca757b8b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078934"
---
>[!NOTE]
> このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) を使用してユーザーを認証します。 このサンプルは、この種類の認証を使用してローカル環境内の Azure 資格情報 (ローカルの [Azure CLI](/cli/azure/install-azure-cli) からのログインや、Visual Studio または Visual Studio Code でのログインなど) を検索します。
>
> `DefaultAzureCredential` とその他の認証オプションの使用の詳細については、[アプリ認証コードを作成する方法](../articles/digital-twins/how-to-authenticate-client.md)に関するページで確認します。