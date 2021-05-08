---
title: サブスクリプションを管理する
description: サブスクリプションは、コミット済みのマネージド デバイスで構成され、必要に応じてオンボードまたはオフボードすることができます。
ms.date: 3/30/2021
ms.topic: how-to
ms.openlocfilehash: 10098ec66d15b4c894106cc1d37d7ac339d508cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387162"
---
# <a name="manage-a-subscription"></a>サブスクリプションを管理する

サブスクリプションは月単位で管理されます。 サブスクリプションをオンボードすると、そのサブスクリプションについては月末まで請求されます。 同様に、サブスクリプションをオフボードした場合、そのサブスクリプションをオフボードした月の残りの料金が請求されます。

## <a name="onboard-a-subscription"></a>サブスクリプションをオンボードする

Azure Defender for IoT の使用を開始するには、Microsoft Azure サブスクリプションを用意する必要があります。 サブスクリプションがない場合は、無料アカウントにサインアップできます。 Azure サブスクリプションへのアクセス権を既に持っているが、一覧に表示されていない場合は、アカウントの詳細を確認し、自分のアクセス許可とサブスクリプションをご確認ください。

サブスクリプションをオンボードするには:

1. Azure portal の [価格] ページに移動します。 

   :::image type="content" source="media/how-to-manage-subscriptions/no-subscription.png" alt-text="Azure portal の [価格] ページに移動します。":::

1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。

1. **[オンボード サブスクリプション]** ウィンドウで、サブスクリプションを選択し、ドロップダウン メニューからコミットされたデバイスの数を選択します。 

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="サブスクリプションと、コミットされたデバイスの数を選択する。":::

1. **[オンボード]** を選択します。

## <a name="offboard-a-subscription"></a>サブスクリプションをオフボードする

サブスクリプションは月単位で管理されます。 サブスクリプションをオフボードすると、そのサブスクリプションについては月末まで請求されます。

サブスクリプションをオフボードする前に、サブスクリプションに関連付けられているすべてのセンサーをアンインストールします。 センサーを削除する方法の詳細については、「[センサーを削除する](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)」を参照してください。 

サブスクリプションをオフボードするには:

1. **[価格]** ページに移動します。
1. サブスクリプションを選択してから、**削除** アイコン :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: を選択します。
1. 確認のポップアップで、サブスクリプションに関連付けられているすべてのセンサーを削除したことを確認するチェック ボックスをオンにします。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="チェック ボックスをオンにし、[オフボード] を選択してセンサーをオフボードする。":::

1. **[オフボード]** ボタンを選択します。 

## <a name="next-steps"></a>次の手順

[オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)
