---
title: Azure Maps のアカウントとキーを管理する | Microsoft Docs
description: Azure Portal を使用して、Azure Maps アカウントを管理し、アクセス キーを管理することができます。
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 81746279e935f9fa5b6ef00bc8d31e75f4d3bd20
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103046"
---
# <a name="manage-your-azure-maps-account-and-keys"></a>Azure Maps のアカウントとキーを管理する

Azure Maps アカウントとキーは、Azure Portal で管理することができます。 アカウントとキーを取得した後、Web サイトまたはモバイル アプリケーションで API を実装できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-new-account"></a>新しいアカウントを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure portal の左上隅にある **[リソースの作成]** を選択します。

3. **[マップ]** を検索して選択します。 **[作成]** を選択します。

4. 新しいアカウントの情報を入力します。

![ポータルでアカウント情報を入力する](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>アカウント ページでキーを管理する

アカウントを作成した後、ランダムに生成された 2 つのキーを取得します。 マップ データを取得する場合や、新しい JavaScript マップ インスタンスを作成する場合に、これらのキーを使用して Azure Maps API に対して認証します。

キーは、Azure Portal で確認することができます。 自分のアカウントに移動します。 続いてメニューから **[キー]** を選択します。

![ポータルでアカウント キーを管理する](./media/how-to-manage-account-keys/account-keys-portal.png)

このページから、キーをコピーしたり、新しいキーを生成したりすることができます。

## <a name="delete-an-account"></a>アカウントの削除

Azure Portal からアカウントを削除できます。 [アカウントの概要] ページに移動し、**[削除]** を選択します。

![ポータルでアカウントを削除する](./media/how-to-manage-account-keys/account-delete-portal.png)

確認ページが表示されます。 その名前を入力し、アカウントの削除を確定できます。

## <a name="next-steps"></a>次の手順

* Azure Maps アカウントの価格レベルを管理する方法について確認します。
    > [!div class="nextstepaction"] 
    > [価格レベルを管理する](./how-to-manage-pricing-tier.md)

* Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について確認します。
    > [!div class="nextstepaction"] 
    > [使用状況メトリックを表示する](./how-to-view-api-usage.md)
