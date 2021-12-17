---
author: baanders
description: Azure Digital Twins サンプルで DefaultAzureCredential のローカル認証を設定するためのインクルード ファイル - 概要あり
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 2d1a1636ca1ce7ea3bacaa872178d100ec2536ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437677"
---
### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

このサンプルでは、ローカル コンピューターで実行された Azure Digital Twins インスタンスに対し、(`Azure.Identity` ライブラリの) [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) を使用してユーザーを認証します。 Azure Digital Twins に対してクライアント アプリの認証を行う各種の方法について詳しくは、「[アプリ認証コードを作成する](../articles/digital-twins/how-to-authenticate-client.md)」を参照してください。

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]