---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993105"
---
### <a name="access-tokens"></a>アクセス トークン

アクセス トークンは、Azure Spatial Anchors で認証するためのより堅牢な方法です。 特に運用環境デプロイメントのアプリケーションを準備するきにはそのように言えます。 このアプローチの概要は、クライアント アプリケーションが安全に認証できるバックエンド サービスを設定することです。 バック エンド サービスは、実行時に AAD と連動し、Azure Spatial Anchors の Secure Token Service と連動してアクセス トークンを要求します。 このトークンは、クライアント アプリケーションに配信され、SDK で Azure Spatial Anchors で認証するために使用されます。
