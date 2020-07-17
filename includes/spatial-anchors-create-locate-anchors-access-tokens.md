---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "67180870"
---
### <a name="access-tokens"></a>アクセス トークン

アクセス トークンは、Azure Spatial Anchors で認証するためのより堅牢な方法です。 特に運用環境デプロイメントのアプリケーションを準備するきにはそのように言えます。 このアプローチの概要は、クライアント アプリケーションが安全に認証できるバックエンド サービスを設定することです。 バック エンド サービスは、実行時に AAD と連動し、Azure Spatial Anchors の Secure Token Service と連動してアクセス トークンを要求します。 このトークンは、クライアント アプリケーションに配信され、SDK で Azure Spatial Anchors で認証するために使用されます。
