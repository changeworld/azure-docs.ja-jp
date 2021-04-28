---
author: baanders
description: Azure Digital Twins でのテナント間の制限を記述するインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 16684f8c5947f8b6a09a9a785968dabf3e644c18
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589393"
---
結果として、Azure Digital Twins API への要求には、Azure Digital Twins インスタンスが存在するのと同じテナントの一部であるユーザーまたはサービス プリンシパルが必要です。 Azure Digital Twins エンドポイントの悪意のあるスキャンを防ぐために、発信元のテナントの外部からのアクセス トークンを含む要求には、"404 サブドメインが見つかりませんでした" というエラー メッセージが返されます。 このエラーは、ユーザーまたはサービス プリンシパルに [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md) コラボレーションを通じて Azure Digital Twins データ所有者または Azure Digital Twins データ閲覧者の [ロール](../articles/digital-twins/concepts-security.md)が与えられた "*場合でも*" 返されます。 