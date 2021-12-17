---
title: Azure portal で Defender for IoT を使用してセンサーを管理する
description: Azure portal ポータルで Defender for IoT を利用し、センサーをオンボード、表示、管理する方法について説明します。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: c01f5826238a5e4091fbaef58b42205c0bb2d9d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339899"
---
# <a name="manage-sensors-with-defender-for-iot-in-the-azure-portal"></a>Azure portal で Defender for IoT を使用してセンサーを管理する

この記事では、[Azure portal ポータルで Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) を利用し、センサーをオンボード、表示、管理する方法について説明します。

## <a name="onboard-sensors"></a>センサーをオンボードする

センサーは、それを Microsoft Defender for IoT に登録してセンサー アクティブ化ファイルをダウンロードすることでオンボードします。

### <a name="register-the-sensor"></a>センサーを登録する

**登録するには、次の手順を実行します。**

1. Azure portal で [Defender for IoT の概要ページ](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)に移動します。

1. **[センサーのオンボード]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/onboard-a-sensor.png" alt-text="センサーのオンボード を選択して、センサーのオンボード プロセスを開始します。":::

1. センサー名を作成します。 

    インストールしたセンサーの IP アドレスを名前の一部として含めるか、識別しやすい名前を使用することをお勧めします。 それにより追跡が容易になるほか、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) での登録名と、センサー コンソールに表示されるデプロイ済みセンサーの IP との間で一貫性が与えられます。

1. センサーを Azure サブスクリプションと関連付けます。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/name-subscription.png" alt-text="わかりやすい名前を入力し、センサーをサブスクリプションに接続します。":::

1. **[クラウド接続]** トグルを使用してセンサーの接続モードを選択します。 このトグルがオンの場合、センサーはクラウド接続になります。 このトグルがオフの場合、センサーはローカル管理になります。

   - **クラウド接続のセンサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 アラート情報は IoT ハブ経由で配信され、Microsoft Sentinel など、他の Azure サービスと共有できます。 また、脅威インテリジェンス パッケージを Defender for IoT からセンサーにプッシュできます。 逆に、センサーがクラウドに接続されていない場合は、脅威インテリジェンス パッケージをダウンロードしてから、エンタープライズ センサーにアップロードする必要があります。 Defender for IoT からセンサーにパッケージをプッシュできるようにするには、 **[脅威インテリジェンスの自動更新]** トグルをオンにします。 詳細については、「[脅威インテリジェンスの調査とパッケージ](how-to-work-with-threat-intelligence-packages.md)」を参照してください。
   
   クラウド接続のセンサーの場合、オンボード時に定義された名前が、センサー コンソールに表示される名前です。 この名前は、コンソールから直接変更することはできません。 ローカル管理センサーの場合、オンボード時に指定された名前は、Azure に保存されますが、センサー コンソールで更新することができます。

   - **ローカル管理センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 エアギャップ ネットワークで作業していて、複数のローカル管理センサーによって検出されるすべての情報を一元的に表示したい場合は、オンプレミスの管理コンソールを使用します。

1. センサーを関連付けるサイトを IoT Hub 内で選択します。 IoT Hub は、このセンサーと Microsoft Defender for IoT の間のゲートウェイとして機能します。 表示名とゾーンを定義します。 説明タグを追加することもできます。 表示名、ゾーン、タグは、[[サイトとセンサー] ページ](#view-onboarded-sensors)にある説明用のエントリです。

1. **[登録]** を選択します。 

### <a name="download-the-sensor-activation-file"></a>センサー アクティブ化ファイルをダウンロードする

センサーを登録すると、アクティブ化ファイルをダウンロードできるようになります。 センサー アクティブ化ファイルには、センサーの管理モードに関する説明が含まれています。 デプロイするセンサーごとに、一意のアクティブ化ファイルをダウンロードします。 センサー コンソールに初めてサインインするユーザーは、アクティブ化ファイルをセンサーにアップロードします。

**アクティブ化ファイルをダウンロードするには:**

1. **[センサーのオンボード]** ページで、 **[登録]** を選択します。

1. **[アクティブ化ファイルのダウンロード]** を選択します。

1. 初めてセンサー コンソールにサインインしようとしているユーザーが、ファイルにアクセスできるようにします。

## <a name="view-onboarded-sensors"></a>オンボードされたセンサーを表示する

オンボードされたセンサーに関する重要な操作情報を表示するには:

1. Azure portal で [Defender for IoT の概要](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)ページに移動します。

1. **[サイトとセンサー]** を選択します。 このページには、オンボードされたセンサーの数、クラウド接続およびローカル管理のセンサーの数、および以下が表示されます。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/sites-and-sensors.png" alt-text="[サイトとセンサー] ページを選択すると、関連付けられているすべてのセンサーが表示されます。":::

    - オンボード時にセンサーに割り当てられた名前。
    - 接続の種類 (クラウド接続またはローカル管理)。
    - センサーに関連付けられているゾーン。
    - インストールされているセンサーのバージョン。
    - クラウドへのセンサーの接続状態。
    - クラウドへのセンサーの接続が最後に検出された時刻。

## <a name="manage-onboarded-sensors"></a>オンボード センサーを管理する

センサーに関連する管理タスクには [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) を使用します。

オンボードされたセンサーは、 **[サイトとセンサー]** ページに表示されます。 また、このページからは、センサー情報を編集することもできます。

### <a name="export-sensor-details"></a>センサーの詳細をエクスポートする

オンボードされているセンサーの情報をエクスポートするには、 **[サイトとセンサー]** ページの上部にある **[エクスポート]** アイコンを選択します。

:::image type="content" source="media/how-to-manage-sensors-on-the-cloud/export-sensors.png" alt-text="エクスポート ボタンを選択してセンサー情報をエクスポートします。":::

### <a name="edit-sensor-zone-details"></a>センサー ゾーンの詳細を編集する

センサーの名前とゾーンを編集するには、 **[サイトとセンサー]** 編集オプションを使用します。

**編集するには、次の操作を行います。**

1. 編集するセンサーの **省略記号** **[...]** を選択します。

1. **[Edit]\(編集\)** を選択します。

1. センサー ゾーンを更新するか、新しいゾーンを作成します。

### <a name="delete-a-sensor"></a>センサーを削除する

クラウド接続のセンサーを削除すると、IoT ハブに情報が送信されなくなります。 ローカル接続のセンサーを使用しなくなった場合は削除します。

**センサーを削除するには:**

1. 削除するセンサーの省略記号 **[...]** を選択します。

1. **[センサーの削除]** を選択します。

### <a name="reactivate-a-sensor"></a>センサーを再アクティブ化する 

次のような場合は、センサーの再アクティブ化が必要になることがあります。

- **ローカル管理モードではなく、クラウド接続モードで作業する**:再アクティブ化した後は、センサーにセンサー検出が表示され、新たに検出されたアラート情報が IoT ハブを通じて配信されます。 この情報は、Microsoft Sentinel など、他の Azure サービスと共有できます。

- **クラウド接続モードではなく、ローカル管理モードで作業する**: 再アクティブ化後は、そのセンサーにのみセンサーの検出情報が表示されます。

- **センサーを新しい IoT ハブに関連付ける**:これを行うには、新しいハブにセンサーを再登録して、新しいアクティブ化ファイルをダウンロードします。

**センサーを再アクティブ化するには**

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) で **[サイトとセンサー]** ページに移動します。

1. 新しいアクティブ化ファイルをアップロードするセンサーを選択します。

1. **省略記号** ( **...** ) を選択し、 **[センサーの削除]** を選択します。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/delete-a-sensor.png" alt-text="省略記号 (...) を選択し、[センサーの削除] を選択します。":::

1. 新しいモードで、または [はじめに] ページの **[センサーのオンボード]** を選択して新しい IoT ハブで、[センサーを再度オンボード](#onboard-sensors)します。

1. アクティブ化ファイルをダウンロードします。

1. Defender for IoT センサー コンソールにサインインします。

1. センサー コンソールで、 **[システム設定]** を選択し、 **[再アクティブ化]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="アクティブ化ファイルをアップロードしてセンサーを再アクティブ化します。":::

1. **[アップロード]** を選択し、[センサーのオンボード] ページで保存したファイルを選択します。

1. **[アクティブ化]** を選びます。

## <a name="next-steps"></a>次の手順

[センサーをアクティブにしてセットアップする](how-to-activate-and-set-up-your-sensor.md)
