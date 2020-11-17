---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要あり
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329515"
---
### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) を使用してユーザーを認証します。 Azure Digital Twins に対してクライアント アプリの認証を行う各種の方法について詳しくは、[*アプリ認証コードを作成する方法*](../articles/digital-twins/how-to-authenticate-client.md)に関するページで確認します。

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]