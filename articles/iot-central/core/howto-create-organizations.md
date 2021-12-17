---
title: IoT Central 組織を管理する | Microsoft Docs
description: この記事では、組織階層を作成および管理して、IoT Central 組織内のどのユーザーがどのデバイスにアクセスできるかを制御する方法について説明します。 組織を使用して、マルチテナント IoT Central アプリケーションを作成します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656761"
---
# <a name="manage-iot-central-organizations"></a>IoT Central 組織を管理する

組織では、IoT Central アプリケーション内でどのユーザーがどのデバイスを表示できるかを管理するために使用する階層を定義できます。 ユーザーのロールによって、表示されるデバイスに対するアクセス許可と、ユーザーがアクセスできるエクスペリエンスが決まります。

組織は階層化されています。

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="組織のサンプル階層を示す図。" border="false":::

最初の組織階層を作成すると、アプリケーションの名前がルートにあることがわかります。 追加する各組織は、子またはリーフ組織になります。 組織階層では継承が使用されます。 たとえば、前の図に示した階層では、次のようになります。

- *Adatum Solar* のユーザーは、*米国*、*カナダ*、*ヨーロッパ*、および *ラテン アメリカ* にあるデバイスにアクセスできます。
- *Contoso* ユーザーは、階層内のすべての組織のすべてのデバイスにアクセスできます。

兄弟組織内のユーザーは、お互いのデバイスを表示できません。

ユーザーにアプリケーションへのアクセス権を付与する場合、階層内の上位にアクセス許可を割り当てるほど、表示および管理できる範囲が広がります。 組織が、ユーザーが表示できるデバイスを制御します。 ロールによって、デバイスの作成、読み取り、削除など、ユーザーが実行できる操作が定義されます。

次のスクリーンショットは、IoT Central での組織階層の定義を示しています。

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="組織階層定義のスクリーンショット。":::

## <a name="create-a-hierarchy"></a>階層の作成

組織の使用を開始するには、組織の階層を定義する必要があります。 階層内の各組織は、デバイスの配置、ダッシュボードとデバイスグループの保存、ユーザーの招待を行う論理コンテナーとして機能します。 組織を作成するには、IoT Central アプリケーションの **[管理]** セクションに移動し、 **[組織]** タブを選択してから、 **[+ 新規]** を選択するか、既存組織のコンテキスト メニューを使用します。 一度に 1 つまたは多数の組織を作成するには、 **[+ Add another organization (+ 別の組織の追加)]** を選択します。

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="組織階層を作成するためのオプションを示すスクリーンショット。":::

> [!TIP]
> 組織の初期設定は、**アプリの管理者** ロールのメンバーが行う必要があります。

新しい親に組織を再割り当てするには、 **[編集]** を選択して、新しい親を選択します。

組織を削除するには、ダッシュボード、デバイス、ユーザー、デバイス グループ、ジョブなど、関連付けられている項目を削除するか、それらの項目を別の組織に移動する必要があります。

> [!TIP]
> REST API を使用して[組織の作成と管理](/rest/api/iotcentral/1.1-previewdataplane/organizations)を行うこともできます。

## <a name="assign-devices"></a>デバイスの割り当て

組織の階層を定義したら、デバイスを組織に割り当てます。 各デバイスは 1 つの組織にのみ属しているため、階層内の適切な組織を選択します。

アプリケーションで新しいデバイスを作成する場合は、階層内の組織にそのデバイスを割り当てます。

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="新しい組織にデバイスを割り当てる方法を示すスクリーンショット。":::

既存のデバイスを組織に割り当て、または再割り当てするには、デバイス リストからデバイスを選択し、 **[組織]** を選択します。

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="デバイスが関連付けられる組織を変更する方法を示すスクリーンショット。":::

> [!TIP]
> デバイスが属している組織は、デバイス リストで確認できます。 特定の組織内のデバイスを表示するには、デバイス リストのフィルター ツールを使用します。

デバイスを別の組織に再割り当てすると、データ インジェストに基づいて、デバイスのデータが組織に保持されます。 次に例を示します。

- デバイスは、1 日目から 7 日目まで **Contoso/Customer1** 組織に属しており、8 日目に **Contoso/Customer4** 組織に移動されます。
- 9 日目に **Contoso/Customer4** ユーザーが表示できるのは、8 日目と 9 日目のデータです。
- 9 日目に、**Contoso/Customer1** ユーザーが表示できるのは、1 日目から 7 日目までのデータです。

### <a name="device-first-registration"></a>デバイス - 最初の登録

デバイスは、最初にデバイス リストに追加しなくても、IoT Central アプリケーションに自己登録できます。 この場合、IoT Central によって、階層内のルート組織にデバイスが追加されます。 その後、デバイスを別の組織に再割り当てできます。

また、CSV インポート機能を使用して、アプリケーションにデバイスを一括で登録し、それらを組織に割り当てることもできます。 詳細については、「[デバイスのインポート](howto-manage-devices.md#import-devices)」をご覧ください。

### <a name="gateways"></a>ゲートウェイ

ゲートウェイとリーフ デバイスを組織に割り当てます。 ゲートウェイとそれに関連付けられているリーフ デバイスを同じ組織に割り当てる必要はありません。 別の組織に割り当てると、ユーザーにはゲートウェイは表示されるがリーフ デバイスは表示されなかったり、リーフ デバイスは表示されるがゲートウェイは表示されなかったりする可能性があります。

## <a name="roles"></a>ロール

アプリケーションで最初の組織を作成すると、IoT Central によってアプリケーションに **組織管理者**、**組織オペレーター**、および **組織ビューアー** という 3 つの新しいロールが追加されます。 価格プラン、ブランド化とカラー設定、API トークン、およびアプリケーション全体の登録グループ情報など、特定のアプリケーション全体にわたる機能にアクセスできない組織ユーザーもいるため、これらのロールが必要になります。

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="3 つの組織ロールを示すスクリーンショット。":::

これらのロールは、アプリケーションで組織にユーザーを招待するときに使用できます。

### <a name="custom-organization-roles"></a>カスタム組織ロール

組織のユーザーのカスタム ロールを作成するには、新しいロールを作成し、ロールの種類として **[組織]** を選択します。

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="カスタム組織ロールを作成する方法を示すスクリーンショット。":::

次に、ロールのアクセス許可を選択します。

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="組織ロールのカスタム アクセス許可を選択する方法を示すスクリーンショット。":::

## <a name="invite-users"></a>ユーザーの招待

組織の階層を作成し、デバイスを組織に割り当てたら、ユーザーをアプリケーションに招待して、組織に割り当てます。

ユーザーを招待するには、 **[管理] > [ユーザー]** に移動します。 ユーザーの電子メール アドレス、ユーザーが割り当てられている組織、ユーザーがメンバーになっているロールを入力します。 選択した組織によって、使用可能なロールのリストが絞り込まれ、有効なロールにユーザーを割り当てられるようになります。

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="組織およびロールにユーザーを割り当てる方法を示すスクリーンショット。":::

同じユーザーを複数の組織に割り当てることができます。 ユーザーには、割り当てられている各組織で異なるロールを設定できます。

| 名前 | Role | Organization |
| ---- | ---- | ------------ |
| user1@contoso.com | 組織管理者 | Contoso Inc/Lamna Health |
| user1@contoso.com | 組織ビューアー | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>組織の使用

組織階層を作成した後、次のようなアプリケーションの領域で組織を使用できます。

- 組織内のデバイスに関する情報をユーザーに表示する[組織のダッシュボード](howto-manage-dashboards.md)。
- 特定の組織内のデバイスの[デバイス グループ](tutorial-use-device-groups.md)。
- 特定の組織内のデバイスの[分析](howto-create-analytics.md)。
- 特定の組織内のデバイスを一括管理する[ジョブ](howto-manage-devices-in-bulk.md#create-and-run-a-job)。

## <a name="default-organization"></a>既定の組織

アプリケーションで使用する既定の組織として組織を設定できます。 既定の組織は、IoT Central アプリケーションに新しいユーザーを追加するときなど、組織を選択するたびに既定のオプションになります。

既定の組織を設定するには、上部のメニュー バーで **[設定]** を選択します。

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="既定の組織を設定する方法を示すスクリーンショット。":::

> [!TIP]
> これは、自分のみに適用される個人設定です。

## <a name="add-organizations-to-an-existing-application"></a>既存のアプリケーションへの組織の追加

アプリケーションには、組織階層を追加する前に、デバイス、ユーザー、およびエクスペリエンス (ダッシュボード、デバイス グループ、ジョブなど) が含まれている場合があります。

組織の追加を開始しても、すべての既存のデバイス、ユーザー、およびエクスペリエンスは、アプリケーションのルート組織に関連付けられたままになります。

- 新しい組織にデバイスを再割り当てすることができます。
- 新しい組織にユーザーを割り当てて、ルートから割り当てを解除することができます。
- ダッシュボード、デバイス グループ、ジョブなどのエクスペリエンスを再作成し、階層内の組織に関連付けることができます。

## <a name="limits"></a>制限

組織には次の制限が適用されます。

- 階層に指定できる深さは、最大 5 レベルです。
- 組織の合計数は、最大 200 です。 階層内の各ノードは、1 つの組織としてカウントされます。

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central 組織の管理方法を学習しました。推奨される次のステップは、[データ エクスポートを使用して IoT データをクラウドの宛先にエクスポートする](howto-export-data.md)方法を学習することです。
