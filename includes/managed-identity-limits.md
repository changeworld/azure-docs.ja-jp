---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201482"
---
- 「[Azure AD サービスの制限と制約](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md)」で説明されているように、各マネージド ID は、Azure AD テナントにおけるオブジェクトのクォータ制限に加算されます。
-   マネージド ID の作成速度には、次の制限があります。

    1. 各 Azure リージョンの各 Azure AD テナントについて: 20 秒あたり 400 件の作成操作。
    2. 各 Azure リージョンの各 Azure サブスクリプションについて: 20 秒あたり 80 件の作成操作。

-   ユーザー割り当てマネージド ID を Azure リソースに割り当てることができる速度は、次の通りです。

    1. 各 Azure リージョンの各 Azure AD テナントについて: 20 秒あたり 400 件の割り当て操作。
    2. 各 Azure リージョンの各 Azure サブスクリプションについて: 20 秒あたり 300 件の割り当て操作。




