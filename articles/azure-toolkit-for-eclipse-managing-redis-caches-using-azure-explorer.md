---
title: "Azure Explorer for Eclipse を使用して Redis Cache を管理する | Microsoft Docs"
description: "Azure Explorer for Eclipse を使って Azure Redis Cache を管理する方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/14/2017
ms.author: robmcm
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: dc1ed15cb83e6ddc8cf84f5c52a0482231f75e40
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="managing-redis-caches-using-the-azure-explorer-for-eclipse"></a>Azure Explorer for Eclipse を使用して Redis Cache を管理する

Azure Toolkit for Eclipse の一部である Azure Explorer は、Eclipse IDE 内から Azure アカウントの Redis Cache を管理するための使いやすいソリューションを Java 開発者に提供します。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-eclipse"></a>Eclipse を使用して Redis Cache を作成する

次の手順では、Azure Explorer を使って Redis Cache を作成する方法を説明します。

1. 「[Azure Toolkit for Eclipse のサインイン手順]」の記事の手順を使用して、Azure アカウントにサインインします。

1. **Azure Explorer** ツール ウィンドウで、**[Azure]** ノードを展開し、**[Redis Cache]** を右クリックして、**[Create Redis Cache]\(Redis Cache の作成\)** をクリックします。

   ![[Create Redis Cache]\(Redis Cache の作成\) メニュー][CR01]

1. **[新規 Redis Cache]** ダイアログ ボックスが表示されたら、次のオプションを指定します。

   ![新規 Redis Cache 作成ダイアログ ボックス][CR02]

   a. **[DNS 名]**: 新しい Redis Cache の DNS サブドメインを指定します。この名前は、".redis.cache.windows.net" の前に付加されます (例: *wingtiptoys.redis.cache.windows.net*)。

   b. **[サブスクリプション]**: 新しい Redis Cache に使う Azure サブスクリプションを指定します。

   c. **[リソース グループ]**: Redis Cache のリソース グループを指定します。以下のオプションのいずれかを選ぶ必要があります。
      * **[新規作成]**: 新しいリソース グループを作成することを指定します。
      * **[既存のものを使用]**: Azure アカウントに関連付けられているリソース グループの一覧から選ぶことを指定します。

   d. **[場所]**: Redis Cache を作成する場所を指定します (例: *米国西部*)。

   e. **[価格レベル]**: Redis Cache が使う価格レベルを指定します。この設定により、クライアント接続の数が決まります。 詳しくは、「[Redis Cache の価格]」をご覧ください。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[非 SSL ポート]**: Redis Cache が非 SSL 接続を許可するかどうかを指定します。既定では、SSL 接続のみが許可されます。

1. Redis Cache のすべての設定を指定したら、**[OK]** をクリックします。

Redis Cache が作成されて、Azure Explorer に表示されます。

   ![Azure Explorer の Redis Cache][CR03]

> [!NOTE]
>
> Azure Redis Cache の設定の構成について詳しくは、「[Azure Redis Cache の構成方法]」をご覧ください。
>

## <a name="display-the-properties-for-your-redis-cache-in-eclipse"></a>Eclipse で Redis Cache のプロパティを表示する

1. Azure Explorer で Redis Cache を右クリックし、**[プロパティの表示]** をクリックします。

   ![Redis Cache のプロパティを表示する Azure Explorer のコンテキスト メニュー][SP01]

1. Azure Explorer で、Redis Cache のプロパティが表示されます。

   ![Redis cache properties][SP02]

## <a name="delete-your-redis-cache-by-using-eclipse"></a>Eclipse を使用して Redis Cache を削除する

1. Azure Explorer で Redis Cache を右クリックし、**[削除]** をクリックします。

   ![Redis Cache を削除する Azure Explorer のコンテキスト メニュー][DE01]

1. Redis Cache の削除を確認するメッセージが表示されたら、**[OK]** をクリックします。

   ![Redis Cache 削除のプロンプト][DE02]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

Azure Redis Cache、構成設定、および料金について詳しくは、次のリンクをご覧ください。

* [Azure Redis Cache]
* [Redis Cache のドキュメント]
* [Redis Cache の価格]
* [Azure Redis Cache の構成方法]

<!-- URL List -->

[Redis Cache の価格]: https://azure.microsoft.com/pricing/details/cache/
[Azure Redis Cache]: https://azure.microsoft.com/services/cache/
[Redis Cache のドキュメント]: ./redis-cache/index.md
[Azure Redis Cache の構成方法]: ./redis-cache/cache-configure.md
[Azure Toolkit for Eclipse のサインイン手順]: ./azure-toolkit-for-eclipse-sign-in-instructions.md

<!-- IMG List -->

[CR01]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: ./media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/DE02.png

