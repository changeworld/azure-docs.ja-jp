---
author: baanders
description: Azure Digital Twins 設定でのアクセス許可の手順のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408178"
---
Azure Digital Twins インスタンスを設定したら、クライアント アプリケーションを使用してそのインスタンスを操作することが一般的です。 機能するクライアント アプリケーションを作成するには、そのクライアント アプリが Azure Digital Twins に対して認証できることを確認する必要があります。 これは、使用するクライアント アプリに対して [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) **アプリ登録**を設定することによって実行されます。

このアプリの登録では、[Azure Digital Twins API](../articles/digital-twins/how-to-use-apis-sdks.md) に対するアクセス許可を構成します。 後で、クライアント アプリはこのアプリ登録に対して認証し、結果として API への構成済みのアクセス許可が付与されます。

>[!TIP]
> サブスクリプションの所有者は、新しい Azure Digital Twins インスタンスごとに新しいアプリ登録を設定するか、*または*これを 1 回だけ行って、サブスクリプション内のすべての Azure Digital Twins インスタンス間で共有される 1 つのアプリ登録を確立するかを選択できます。

### <a name="create-the-registration"></a>登録を作成する