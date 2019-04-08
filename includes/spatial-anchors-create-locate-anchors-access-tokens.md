---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 0e4c0b6d622fe0d8d20692ec6c0c889779e4601a
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751902"
---
### <a name="access-tokens"></a>アクセス トークン

アクセス トークンは、Azure Spatial Anchors で認証するためのより堅牢な方法です。 特に運用環境デプロイメントのアプリケーションを準備するきにはそのように言えます。 このアプローチの概要は、クライアント アプリケーションが安全に認証できるバックエンド サービスを設定することです。 バック エンド サービスは、実行時に AAD とインターフェイスし、Azure Spatial Anchors STS サービスとインターフェイスしてアクセス トークンを要求します。 このトークンは、クライアント アプリケーションに配信され、SDK で Azure Spatial Anchors で認証するために使用されます。
