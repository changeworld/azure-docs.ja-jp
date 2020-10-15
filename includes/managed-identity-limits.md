---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971045"
---
- 「[Azure AD サービスの制限と制約](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md)」で説明されているように、各マネージド ID は、Azure AD テナントにおけるオブジェクトのクォータ制限に加算されます。
-   マネージド ID の作成速度には、次の制限があります。

    1. 各 Azure リージョンの各 Azure AD テナントについて: 20 秒あたり 200 件の作成操作。
    2. 各 Azure リージョンの各 Azure サブスクリプションについて: 20 秒あたり 40 件の作成操作。

- ユーザー割り当て済みマネージド ID を作成するときは、英数字 (0-9、a-z、A-Z) とハイフン (-) のみがサポートされます。 仮想マシンまたは仮想マシン スケール セットへの割り当てが適切に動作するように、名前は 24 文字に制限されています。
