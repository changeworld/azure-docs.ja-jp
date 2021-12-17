---
author: baanders
description: Azure Digital Twins 設定の手順の概要のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 19d7c55a669a3975cb4bc1dce707e2d1165be0b3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759989"
---
新しい Azure Digital Twins インスタンスのための設定全体は、2 つの部分で構成されます。
1. **インスタンスの作成**
2. **ユーザーのアクセス許可の設定**:Azure ユーザーが Azure Digital Twins インスタンスとそのデータを管理するには、 **"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)** ロールが必要です。 この手順では、Azure サブスクリプションの所有者/管理者が、Azure Digital Twins インスタンスを管理することになる人にこのロールを割り当てます。 これは、自分自身のこともあれば、組織内の他の人であることもあります。
 
>[!IMPORTANT]
>このすべての記事を完了し、使用可能なインスタンスを完全に設定するには、Azure サブスクリプションのリソースとユーザーの両方へのアクセスを管理するためのアクセス許可が必要です。 最初の手順は、サブスクリプションでリソースを作成できる人であればだれでも完了できますが、2 番目の手順では、ユーザー アクセス管理のアクセス許可 (またはこれらのアクセス許可を持つ人の協力) が必要です。 この詳細については、ユーザー アクセス権限手順の「[前提条件:権限の要件](#prerequisites-permission-requirements)」セクションを参照してください。
