---
author: baanders
description: Azure Digital Twins 設定の手順の概要のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478876"
---
>[!NOTE]
>これらの操作は、Azure サブスクリプションでリソースとユーザー アクセスの両方を管理する権限を持つユーザーが実行するよう意図されています。 一部の手順は下位のアクセス許可でも実行できますが、使用可能なインスタンスを完全に設定するためには、これらのアクセス許可を持つユーザーの協力が必要です。 詳細については、下の「 [*前提条件:必要なアクセス許可*](#prerequisites-permission-requirements)」セクションを参照してください。

新しい Azure Digital Twins インスタンスのための設定全体は、2 つの部分で構成されます。
1. **インスタンスの作成**
2. **ユーザーのアクセス許可の設定** :Azure ユーザーが Azure Digital Twins インスタンスとそのデータを管理するには、 *"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)* ロールが必要です。 この手順では、Azure サブスクリプションの所有者/管理者が、Azure Digital Twins インスタンスを管理することになる人にこのロールを割り当てます。 これは、自分自身のこともあれば、組織内の他の人であることもあります。

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]