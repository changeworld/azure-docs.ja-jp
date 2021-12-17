---
title: インクルード ファイル
description: インクルード ファイル
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212489"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料サブスクリプション](https://azure.microsoft.com/free/)を作成してください。

* お使いのサブスクリプションに関連付けられている [Azure Active Directory テナント](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* Azure へのサインインに使用するユーザー アカウントは、"*共同作成者*" ロールまたは "*所有者*" ロールのメンバーであるか、Azure サブスクリプションの "*管理者*" である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、[Azure portal](https://portal.azure.com) に移動し、右上にあるユーザー名を選択してください。" **...** " アイコンを選択してその他のオプションを表示し、 **[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。

* **ストレージ アカウント** または **イベント ハブの名前空間** の作成を防止する [Azure ポリシー](../../governance/policy/overview.md)がないこと。 マネージド ストレージ アカウントおよびイベント ハブが作成されると、Purview によりそれらがデプロイされます。 ブロック ポリシーが存在し、そのまま維持する必要がある場合、[Purview 例外タグ ガイド](../create-purview-portal-faq.md)の手順に従い、Purview アカウントの例外を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。
