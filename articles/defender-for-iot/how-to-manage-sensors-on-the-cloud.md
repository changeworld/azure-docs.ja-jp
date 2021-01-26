---
title: Defender for IoT ポータルでセンサーをオンボードして管理する
description: Defender for IoT ポータルでセンサーのオンボード、表示、管理を行う方法について説明します。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 427ea3884a3db6ba33405014435cf1f962670064
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562711"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Defender for IoT ポータルでセンサーをオンボードして管理する

この記事では、[Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)でセンサーのオンボード、表示、管理を行う方法について説明します。

## <a name="onboard-sensors"></a>センサーをオンボードする

センサーを Azure Defender for IoT に登録してセンサー アクティブ化ファイルをダウンロードし、センサーをオンボードします。

### <a name="register-the-sensor"></a>センサーを登録する

登録するには、次の手順を実行します。

1. [Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)の **[ようこそ]** ページに移動します。
1. **[センサーのオンボード]** を選択します。
1. センサー名を作成します。 インストールしたセンサーの IP アドレスを名前の一部として含めるか、識別しやすい名前を使用することをお勧めします。 そうすることで追跡が容易になるほか、Azure [Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)での登録名と、センサー コンソールに表示されるデプロイ済みセンサーの IP との間で名前の一貫性が確保されます。
1. センサーを Azure サブスクリプションと関連付けます。
1. **[クラウド接続]** トグルを使用してセンサーの管理モードを選択します。 このトグルがオンの場合、センサーはクラウド接続になります。 このトグルがオフの場合、センサーはローカル管理になります。

   - **クラウド接続のセンサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 アラート情報は、IoT ハブを通じて配信され、Azure Sentinel などの他の Azure サービスと共有できます。

   - **ローカル管理センサー**:センサーによって検出された情報は、センサー コンソールに表示されます。 エアギャップ ネットワークで作業していて、複数のローカル管理センサーによって検出されるすべての情報を一元的に表示したい場合は、オンプレミスの管理コンソールを使用します。

   クラウド接続のセンサーの場合、オンボード時に定義された名前が、センサー コンソールに表示される名前です。 この名前は、コンソールから直接変更することはできません。 ローカル管理センサーの場合、オンボード時に指定された名前は、Azure に保存されて、センサー コンソールで更新することができます。

1. このセンサーと Azure Defender for IoT の間のゲートウェイとして機能する IoT hub を選択します。
1. センサーがクラウド接続の場合は、それを IoT hub に関連付けてから、サイト名とゾーンを定義します。 説明タグを追加することもできます。 サイト名、ゾーン、タグは、[[サイトとセンサー] ページ](#view-onboarded-sensors)にある説明用のエントリです。

### <a name="download-the-sensor-activation-file"></a>センサー アクティブ化ファイルをダウンロードする

センサー アクティブ化ファイルには、センサーの管理モードに関する説明が含まれています。 デプロイするセンサーごとに、一意のアクティブ化ファイルをダウンロードします。 センサー コンソールに初めてサインインするユーザーは、アクティブ化ファイルをセンサーにアップロードします。

アクティブ化ファイルをダウンロードするには:

1. **[センサーのオンボード]** ページで、 **[アクティブ化ファイルのダウンロード]** を選択します。
1. 初めてセンサー コンソールにサインインしようとしているユーザーが、ファイルにアクセスできるようにします。

## <a name="view-onboarded-sensors"></a>オンボードされたセンサーを表示する

[Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)で、オンボード センサーに関する基本的な情報を表示できます。 

1. **[サイトとセンサー]** を選択します。
1. **[サイトとセンサー]** ページで、フィルターと検索ツールを使用して、必要なセンサーの情報を見つけます。

以下を含む情報が提供されます。

- オンボードされたセンサーの数
- クラウドに接続されていて、ローカルで管理されているセンサーの数
- オンボードされたセンサーに関連付けられているハブ
- オンボード時にセンサーに割り当てられた名前、センサーに関連付けられているゾーン、タグによって追加されたその他の説明情報などの、センサーについて追加された詳細

## <a name="manage-onboarded-sensors"></a>オンボード センサーを管理する

センサーに関連する管理タスクでは、[Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)を使用します。

### <a name="export"></a>エクスポート

オンボードされているセンサーの情報をエクスポートするには、 **[サイトとセンサー]** ページの上部にある **[エクスポート]** アイコンを選択します。

### <a name="edit"></a>[編集]

**[サイトとセンサー]** の編集ツールを使用して、サイト名、ゾーン、タグを追加および編集します。

### <a name="delete"></a>削除

クラウド接続のセンサーを削除すると、IoT ハブに情報が送信されなくなります。 ローカル接続のセンサーを使用しなくなった場合は削除します。

センサーを削除するには:

1. 削除するセンサーの省略記号 **[...]** を選択します。 
1. 削除を確認します。

### <a name="reactivate"></a>再度有効にする

センサーが管理されているモードを更新することをお勧めします。 例:

- **ローカル管理モードではなく、クラウド接続モードで作業する**:これを行うには、クラウド接続センサーのアクティブ化ファイルを使用して、ローカル接続センサーのアクティブ化ファイルを更新します。 再アクティブ化後は、センサーと [Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)の両方にセンサー検出が表示されます。 再アクティブ化ファイルが正常にアップロードされると、新しく検出されたアラート情報が Azure に送信されます。

- **クラウド接続モードではなく、ローカル接続モードで作業する**:これを行うには、ローカル管理センサーのアクティブ化ファイルを使用して、クラウド接続センサーのアクティブ化ファイルを更新します。 再アクティブ化後は、そのセンサーにのみセンサーの検出情報が表示されます。

- **センサーを新しい IoT ハブに関連付ける**:これを行うには、センサーを再登録して、新しいアクティブ化ファイルをアップロードします。

センサーを再アクティブ化するには

1. [Defender for IoT ポータル](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)の **[サイトとセンサー]** ページに移動します。

2. 新しいアクティブ化ファイルをアップロードするセンサーを選択します。

3. センサーを削除します。

4. **[オンボード]** ページから、新しいモードで、または新しい IoT ハブを使用して、センサーを再度オンボードします。

5. **[アクティブ化ファイルのダウンロード]** ページからアクティブ化ファイルをダウンロードします。

6. Defender for IoT センサー コンソールにサインインします。

7. センサー コンソールで、 **[システム設定]** を選択し、 **[再アクティブ化]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="アクティブ化ファイルをアップロードしてセンサーを再アクティブ化します。":::

8. **[アップロード]** を選択し、保存したファイルを選択します。

9. **[アクティブ化]** を選びます。 

## <a name="see-also"></a>関連項目

[センサーをアクティブにしてセットアップする](how-to-activate-and-set-up-your-sensor.md)
