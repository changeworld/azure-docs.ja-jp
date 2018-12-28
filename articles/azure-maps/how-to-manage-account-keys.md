---
title: Azure Maps のアカウントとキーを管理する方法 | Microsoft Docs
description: Azure Portal を使用して、Azure Maps アカウントを管理し、アクセス キーを管理することができます。
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 351bc26d6c5b3dcfa9479138f2bd523f64a2912a
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335814"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Azure Maps のアカウントとキーを管理する方法

Azure Maps アカウントとキーは、Azure Portal で管理することができます。 アカウントとキーを取得したら、Web サイトまたはモバイル アプリケーションで API を実装できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-new-account"></a>新しいアカウントを作成する

1. [Azure Portal](http://portal.azure.com) にサインインします。

2. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

3. **[Maps]** を探して選択し、**[作成]** をクリックします。

4. 新しいアカウントの情報を入力します。

![ポータルでアカウント情報を入力する](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>アカウント ページでキーを管理する

アカウントを作成すると、ランダムに生成された 2 つのキーを取得します。 マップ データを取得する場合や、新しい JavaScript マップ インスタンスを作成する場合に、このキーを使用して Maps API に対して認証します。

キーは、Azure Portal で確認することができます。 アカウントに移動し、メニューから **[キー]** を選択します。

![ポータルでアカウント キーを管理する](./media/how-to-manage-account-keys/account-keys-portal.png)

このページから、キーをコピーしたり、新しいキーを生成したりすることができます。

## <a name="delete-an-account"></a>アカウントの削除

Azure Portal からアカウントを削除できます。 [アカウントの概要] ページに移動し、**[削除]** を選択します。

![ポータルでアカウントを削除する](./media/how-to-manage-account-keys/account-delete-portal.png)

確認ページが表示されます。 その名前を入力し、アカウントの削除を確定できます。

## <a name="next-steps"></a>次の手順

Azure Maps アカウントの価格レベルを管理する方法について確認します。
> [!div class="nextstepaction"]
> [価格レベルを管理する](./how-to-manage-pricing-tier.md)

Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](./how-to-view-api-usage.md)
