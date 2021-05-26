---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要あり
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: eacabf431d396fa042bf4dd60d3b32c688df46bf
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065131"
---
### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) を使用してユーザーを認証します。 Azure Digital Twins に対してクライアント アプリの認証を行う各種の方法について詳しくは、[アプリ認証コードを作成する方法](../articles/digital-twins/how-to-authenticate-client.md)に関するページで確認します。

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]