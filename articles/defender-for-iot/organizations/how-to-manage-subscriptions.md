---
title: サブスクリプションを管理する
description: サブスクリプションは、コミット済みのマネージド デバイスで構成され、必要に応じてオンボードまたはオフボードすることができます。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dbe749f5189b568e9dee288c484f680cb2a4f426
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339765"
---
# <a name="manage-defender-for-iot-subscriptions"></a>Defender for IoT のサブスクリプションを管理する

## <a name="about-subscriptions"></a>サブスクリプションについて

Defender for IoT のデプロイは、Microsoft Defender for IoT アカウントのサブスクリプションによって管理されます。 お使いのサブスクリプションは、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) で Defender for IoT にオンボードし、編集し、オフボードできます。

各サブスクリプションに対して、"*コミットされたデバイス*" の数を定義するように求められます。 コミットされたデバイスとは、企業内で監視されるデバイスのおおよその数です。 

### <a name="subscription-billing"></a>サブスクリプションの課金

各サブスクリプションに関連付けられているコミットされたデバイスの数に基づいて課金されます。

Microsoft Defender for IoT の請求期間は暦月に従います。 ある月の途中で、コミットされたデバイスに加えた変更は、更新を確定してから 1 時間後に実装され、毎月の請求書に反映されます。 サブスクリプションの "*オフボード*" も、オフボードを確定した 1 時間後に有効になります。

支払いエンティティが企業内に複数ある場合もあります。 この場合、複数のサブスクリプションをオンボードすることができます。

サブスクライブする前に、サブスクリプションに含めるデバイスの数をある程度把握しておく必要があります。

ユーザーは、限られた数のデバイスの監視を 30 日間サポートする試用版サブスクリプションを使用することもできます。 コミットされたデバイスの価格については、[Microsoft Defender の価格](https://azure.microsoft.com/pricing/details/azure-defender/)情報を参照してください。

## <a name="requirements"></a>必要条件

サブスクリプションをオンボードする前に、次のことを確認してください。

- Azure アカウントが設定されている。
- 必要な Azure ユーザーのアクセス許可を持っている。

### <a name="azure-account-subscription-requirements"></a>Azure アカウントのサブスクリプションの要件

Microsoft Defender for IoT の使用を開始するには、Microsoft Azure アカウントのサブスクリプションを用意する必要があります。

サブスクリプションがない場合は、無料アカウントにサインアップできます。 詳細については、「https://azure.microsoft.com/free/」を参照してください。

Azure サブスクリプションへのアクセス権を既に持っているが、Defender for IoT をサブスクライブするときに一覧に表示されていない場合は、アカウントの詳細を確認し、自分のアクセス許可をサブスクリプションの所有者に確認してください。 「https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade」を参照してください。

### <a name="user-permission-requirements"></a>ユーザー権限の要件

Azure の **サブスクリプションの所有者** と **サブスクリプションの共同作成者** は、Microsoft Defender for IoT サブスクリプションをオンボード、更新、オフボードできます。

## <a name="onboard-a-trial-subscription"></a>試用版サブスクリプションをオンボードする

Defender for IoT を評価する場合は、試用版のサブスクリプションをご利用いただけます。 試用版は 30 日間有効で、コミットされたデバイスを 1000 個サポートします。 試用版を使用すると、1 つ以上の Defender for IoT センサーをネットワークにデプロイできます。 センサーを使用して、トラフィックの監視、データの分析、アラートの生成、ネットワークのリスクと脆弱性の把握などを行うことができます。 試用版では、オンプレミスの管理コンソールをダウンロードして、センサーによって生成された集計情報を表示することもできます。

このセクションでは、センサー用に試用版サブスクリプションを作成する方法について説明します。

**試用版サブスクリプションを作成するには:**

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。
1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。
1. [Pricing]\(価格\) ページで、 **[Start with a Trial]\(試用版を開始\)** を選択します。
1. [Onboard trial subscription]\(試用版サブスクリプションをオンボードする\) ウィンドウからサブスクリプションを選択し、 **[Evaluate]\(評価\)** を選択します。
1. 評価を確認します。
1. センサーをオンボードするか、必要に応じてセンサーを設定します。

## <a name="onboard-a-subscription"></a>サブスクリプションをオンボードする

このセクションでは、サブスクリプションをオンボードする方法について説明します。

**サブスクリプションをオンボードするには:**

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。
1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。
1. [Pricing]\(価格\) ページで、 **[Subscribe]\(サブスクライブ\)** を選択します。
1. **[Onboard subscription]\(サブスクリプションをオンボードする\)** ウィンドウで、ドロップダウン メニューからサブスクリプションとコミットされたデバイスの数を選択します。

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="サブスクリプションと、コミットされたデバイスの数を選択する。":::

1. **[サブスクライブ]** を選択します。
1. サブスクリプションを確認します。
1. まだ行っていない場合、センサーをオンボードするか、センサーを設定します。

## <a name="update-committed-devices-in-a-subscription"></a>サブスクリプション内のコミットされたデバイスを更新する

コミットされたデバイスを増やしたり減らしたりして、サブスクリプションを更新する必要がある場合があります。 たとえば、既存のサイトの範囲を増やしたり、予想よりも多くのデバイスが検出されたり、スイッチの追加などのネットワークの変更があったりする場合は、より多くのデバイスの監視が必要になることがあります。

**サブスクリプションを更新するには:**
1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。
1. **[Onboard subscription]\(サブスクリプションのオンボード\)** を選択します。
1. サブスクリプションを選択し、3 つのドットを選択します。 (...).
1. **[編集]** を選択します。
1. コミットされたデバイスを更新して、 **[Save]\(保存\)** を選択します。
2. 確認のダイアログ ボックスが表示されたら、 **[Confirm]\(確認\)** を選択します。

デバイスのコミットメントの変更は、変更を確定してから 1 時間後に有効になります。 これらの変更の課金は、変更を確定した翌月の月初に反映されます。

オンプレミスの管理コンソールに新しいアクティブ化ファイルをアップロードする必要があります。 アクティブ化ファイルには、コミットされたデバイスの新しい数が反映されます。 「[アクティブ化ファイルをアップロードする](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file)」を参照してください。
## <a name="offboard-a-subscription"></a>サブスクリプションをオフボードする

たとえば新しい支払いエンティティを使用する必要がある場合に、サブスクリプションをオフボードすることが必要になる場合があります。 サブスクリプションのオフボードは、オフボードを確定した 1 時間後に有効になります。 次回からの毎月の請求書に、この変更が反映されます。

オフボードする前に、サブスクリプションに関連付けられているすべてのセンサーを削除してください。 センサーを削除する方法の詳細については、「[センサーを削除する](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)」を参照してください。

**サブスクリプションをオフボードするには:**

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。
1. サブスクリプションを選択し、3 つのドットを選択します。 (...).

1. **[Offboard subscription]\(サブスクリプションのオフボード\)** を選択します。

1. 確認のポップアップで、サブスクリプションに関連付けられているすべてのセンサーを削除したことを確認するチェック ボックスをオンにします。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="チェック ボックスをオンにし、[オフボード] を選択してセンサーをオフボードする。":::

1. **[Offboard]\(オフボード\)** を選択します。

## <a name="apply-a-new-subscription"></a>新しいサブスクリプションを適用する

ビジネス上の検討により、現在使用されているものとは別のサブスクリプションをデプロイに適用することが必要になる場合があります。 サブスクリプションを変更する場合、新しいセンサー アクティブ化ファイルをアップロードする必要があります。 ファイルには、サブスクリプションの有効期限に関する情報が含まれています。

**新しいサブスクリプションを適用するには:**

1. 現在使用されているサブスクリプションを削除します。
1. 新しいサブスクリプションを選択します。
1. サブスクリプションに関連付けられているセンサー用のアクティブ化ファイルをダウンロードします。
1. アクティブ化ファイルをセンサーにアップロードします。

## <a name="next-steps"></a>次の手順

- [Azure portal で Defender for IoT を使用してセンサーを管理する](how-to-manage-sensors-on-the-cloud.md)

- [オンプレミス管理コンソールをアクティブにしてセットアップする](how-to-activate-and-set-up-your-on-premises-management-console.md)
