---
title: Azure Logic Apps 内で IoT Central コネクタを使用してワークフローを作成する | Microsoft Docs
description: Azure Logic Apps 内で IoT Central コネクタを使用して、ワークフローのトリガーや、ワークフロー内のデバイスの作成、更新、および削除を行います。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2aced85828e5563b838af327151b2b94bcafb931
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772190"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Azure Logic Apps 内で IoT Central コネクタを使用してワークフローを作成する

*このトピックはビルダーおよび管理者向けです。*

Azure Logic Apps を使用して、クラウド サービスとオンプレミス システムにまたがったアプリとデータを統合する、自動化されたスケーラブルなワークフローを構築します。 Azure Logic Apps には、多数の Azure サービス、Microsoft サービス、他のサービスとしてのソフトウェア (SaaS) 製品にまたがる多くのコネクタがあります。 Azure Logic Apps 内で IoT Central コネクタを使用すると、IoT Central 内で規則がトリガーされたときにワークフローをトリガーできます。 また、IoT Central コネクタ内のアクションを使用して、デバイスの作成、デバイスのプロパティと設定の更新、デバイスの削除を行うこともできます。 

Microsoft Flow 内で IoT Central コネクタを使用できます。 Azure Logic Apps と Microsoft Flow はどちらも設計者優先の統合サービスですが、対象となる利用者が少し異なります。 Flow では、すべてのオフィス ワーカーが必要なビジネス ワークフローを構築できるように支援します。 Logic Apps では、データの接続に必要な統合を作成できるように IT プロフェッショナルを支援します。 Flow と Logic Apps の比較は[こちら](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)をご覧ください。 Microsoft Flow で IoT Central コネクタを使用するワークフローを構築する方法は[こちら](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)をご覧ください。 

## <a name="prerequisites"></a>前提条件

- 従量課金制アプリケーション
- ロジック アプリを作成して管理する Azure アカウントおよびサブスクリプション

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>規則がトリガーされたときに、ワークフローをトリガーする

このセクションでは、規則がトリガーされたときに Microsoft Teams にメッセージを送信する方法を説明します。 イベントのイベント ハブへの送信、新しい Azure DevOps 作業項目の作成、SQL Server への新しい行の挿入などを他のコネクタを使用して実行するように、ワークフローを構成できます。

1. 最初に、[IoT Central 内で規則を作成](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)します。 規則の条件を保存したら、新しいアクションとして **[Azure Logic Apps]** タイルをクリックします。 **[Create in Azure portal]\(Azure portal で作成\)** をクリックします。 新しいロジック アプリを作成できる Azure portal が表示されます。 場合によっては Azure アカウントにサインインする必要があります。

1. 新しいロジック アプリを作成するために必要な情報を入力します。 新しいロジック アプリをプロビジョニングする Azure サブスクリプションを選択できます。 IoT Central アプリを作成したサブスクリプションと同じにする必要はありません。 **Create** をクリックしてください。

    ![Azure portal でロジック アプリを作成する](./media/howto-build-azure-logic-apps-experimental/createinazureportal.png)

1. ロジック アプリが正常に作成されたら、Logic Apps デザイナーに自動的に移動します。 **[空のロジック アプリ]** をクリックします。 

    ![空のロジック アプリを作成する](./media/howto-build-azure-logic-apps-experimental/blanklogicapp.png)

1. デザイナーで "iot central" を検索して、**[When a rule is fired]\(規則が実行されるとき\)** トリガーを選択します。 IoT Central アプリにサインインしたときのアカウントでコネクタにサインインします。 

    ![IoT Central コネクタにサインインする](./media/howto-build-azure-logic-apps-experimental/addtrigger.png)

1. サインインが成功したら、フィールドが表示されます。 ドロップダウンから **[アプリケーション]** と **[規則]** を選択します。

    ![アプリケーションと規則を選択する](./media/howto-build-azure-logic-apps-experimental/pickappandrule.png)

1. 新しいアクションを追加します。 「**投稿 メッセージ チーム**」を検索し、Microsoft Teams コネクタから **[Post a message]\(メッセージを投稿\)** を選択します。 Microsoft Teams で使用するアカウントでコネクタにサインインします。 

1. アクションで、**[チーム]** と **[チャネル]** を選択します。 **[メッセージ]** フィールドに、各メッセージの内容を入力します。 IoT Central 規則からの"*動的なコンテンツ*"を含め、デバイス名やタイムスタンプなどの重要な情報を通知に渡すことができます。
    > [!NOTE]
    > [動的なコンテンツ] ウィンドウ内の [See more]\(もっと見る\) のテキストをクリックし、規則をトリガーした測定値とプロパティ値を取得します。

    ![動的なウィンドウを開いた状態でのロジック アプリ編集アクション](./media/howto-build-azure-logic-apps-experimental/buildworkflow.png)

1. アクションの編集が完了したら、**[保存]** をクリックします。

1. IoT Central アプリに戻ると、この規則の [Actions]\(アクション\) 区分の下に Azure Logic Apps アクションが表示されます。

ワークフローの作成は、Azure Portal 内の Logic Apps で IoT Central コネクタを使用して、いつでも始めることができます。 ワークフローの作成を開始したら、接続先の IoT Central アプリと規則を選択すると、同じように作動します。 毎回 IoT Central の規則のページから開始する必要はありません。

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>ワークフロー内でデバイスを作成、更新、および削除する

ロジック アプリにワークフローを構築するとき、IoT Central コネクタを使用してアクションを追加できます。 **[Create a device]\(デバイスを作成\)**, **[Update a device]\(デバイスを更新\)**、および **[Delete a device]\(デバイスを削除\)** が表示されます。

> [!NOTE]
> **[Update a device]\(デバイスを更新\)** と **[Delete a device]\(デバイスを削除\)** では、更新または削除する既存のデバイスの ID が必要です。 **Device Explorer** で IoT Central のデバイスの ID を取得できます。

![IoT Central の Device Explorer のデバイス ID](./media/howto-build-azure-logic-apps-experimental/iotcdeviceid.png)


## <a name="next-steps"></a>次の手順
ここでは、Microsoft Flow を使用して、ワークフローを作成する方法を学びました。推奨される次のステップは、[デバイスの管理](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)です。
