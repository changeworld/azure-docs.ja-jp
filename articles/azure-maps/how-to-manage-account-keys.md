---
title: Azure Maps のアカウントとキーを管理する方法 | Microsoft Docs
description: Azure Portal を使用して、Azure Maps アカウントを管理し、アクセス キーを管理することができます。
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 38926cc42b44e61bd83f6b1e33946a9026cda06d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989403"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Azure Maps のアカウントとキーを管理する方法

Azure Maps アカウントとキーは、Azure Portal で管理することができます。 アカウントとキーを取得したら、Web サイトまたはモバイル アプリケーションで API を実装できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-new-account"></a>新しいアカウントを作成する

1. [Azure ポータル](http://portal.azure.com)にサインインします。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

2. **[Maps]** を探して選択し、**[作成]** をクリックします。

3. 新しいアカウントの情報を入力します。 

![ポータルでアカウント情報を入力する](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>アカウント ページでキーを管理する

アカウントを作成すると、ランダムに生成された 2 つのキーを取得します。 マップ データを取得する場合や、新しい JavaScript マップ インスタンスを作成する場合に、このキーを使用して Maps API に対して認証します。 

キーは、Azure Portal で確認することができます。 アカウントに移動し、メニューから **[キー]** を選択します。

![ポータルでアカウント キーを管理する](./media/how-to-manage-account-keys/account-keys-portal.png)

このページから、キーをコピーしたり、新しいキーを生成したりすることができます。 

## <a name="delete-an-account"></a>アカウントの削除

Azure Portal からアカウントを削除できます。 [アカウントの概要] ページに移動し、**[削除]** を選択します。

![ポータルでアカウントを削除する](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>次の手順

[Maps 管理 API](https://docs.microsoft.com/rest/api/maps-management/accounts) を使用して Maps アカウントを作成、更新、削除する方法を学びます。 