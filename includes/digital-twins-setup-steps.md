---
author: baanders
description: Azure Digital Twins 設定の手順の概要のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407524"
---
>[!NOTE]
>これらの操作は、Azure サブスクリプションで "*所有者*" ロールを持つユーザーが実行するよう意図されています。 一部の操作はこの昇格されたアクセス許可がなくても実行できますが、使用可能なインスタンスを完全に設定するには、所有者の協力が必要になります。 詳細については、下の「[*前提条件:必要なアクセス許可*](#prerequisites-permission-requirements)」セクションを参照してください。

新しい Azure Digital Twins インスタンスのための設定全体は、3 つの部分で構成されます。
1. **インスタンスの作成**
2. **ユーザーのアクセス許可の設定**:Azure ユーザーが Azure Digital Twins インスタンスとそのデータを管理するには、"*Azure Digital Twins 所有者 (プレビュー)* " ロールが必要です。 この手順では、Azure サブスクリプションの所有者が、Azure Digital Twins インスタンスを管理することになる人にこのロールを割り当てます。 これは、自分自身のこともあれば、組織内の他の人であることもあります。
3. **クライアント アプリケーションのアクセス許可の設定**:インスタンスとやり取りするために使用するクライアント アプリケーションを作成するのは一般的です。 そのクライアント アプリが Azure Digital Twins にアクセスできるようにするには、そのクライアント アプリケーションがインスタンスに対して認証するために使用する "*アプリ登録*" を [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) で設定する必要があります。

続行するには、Azure サブスクリプションが必要です。 [こちら](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)から無料で設定できます。
