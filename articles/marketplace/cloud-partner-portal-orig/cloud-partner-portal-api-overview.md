---
title: Cloud パートナー ポータルの API リファレンス | Azure Marketplace
description: マーケットプレース API 操作の説明、使用のための前提条件、一覧。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288548"
---
<a name="cloud-partner-portal-api-reference"></a>Cloud パートナー ポータルの API リファレンス
==================================

Cloud パートナー ポータルの REST API を使用すると、ワークロード、プラン、発行元プロファイルのプログラムによる取得と操作が可能になります。 API は、処理の際に正しいアクセス許可を実施するために、ロールベースのアクセス制御 (RBAC) を使用します。

このリファレンスでは、Cloud パートナー ポータル REST API の技術的な詳細について説明します。 このドキュメントのペイロードのサンプルは参考にすぎず、新しい機能が追加されると変更される可能性があります。


<a name="prerequisites-and-considerations"></a>前提条件と考慮事項
-------------------------------

API を使用する前に、次の点を確認してください。

- アカウントにサービス プリンシパルを追加する方法、および認証用の Azure Active Directory (Azure AD) アクセス トークンを取得する方法については、[前提条件](./cloud-partner-portal-api-prerequisites.md)の記事を参照してください。 
- これらの API を呼び出すために利用可能な 2 つの[コンカレンシー制御](./cloud-partner-portal-api-concurrency-control.md)
戦略。
- バージョン管理やエラー処理などの API に関する追加の[考慮事項](./cloud-partner-portal-api-considerations.md)。


<a name="common-tasks"></a>一般的なタスク
------------
このリファレンスでは、以下の一般的なタスクを実行するための API について詳しく説明します。


### <a name="offers"></a>オファー

-   [すべてのプランの取得](./cloud-partner-portal-api-retrieve-offers.md)
-   [特定のプランの取得](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [プランの状態の取得](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [オファーの作成](./cloud-partner-portal-api-creating-offer.md)
-   [プランの発行](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>操作

-   [操作の取得](./cloud-partner-portal-api-retrieve-operations.md)
-   [操作のキャンセル](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>アプリの発行

-   [公開](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>その他のタスク

-   [仮想マシン プランの価格設定](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>トラブルシューティング

-   [認証エラーのトラブルシューティング](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
