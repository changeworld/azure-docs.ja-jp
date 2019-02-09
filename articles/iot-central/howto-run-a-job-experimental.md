---
title: Azure IoT Central アプリケーションでデバイス を作成して実行する | Microsoft Docs
description: Azure IoT Central のジョブでは、デバイス プロパティを更新、設定、またはコマンドの実行などの一括デバイス管理機能。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: e418ec7d22622c341abd972763d78ac2f0df46d9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772339"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションでのジョブの作成と実行

Microsoft Azure IoT Central を使用して、ジョブを使用して大規模に接続されているデバイスを管理します。 ジョブの機能では、デバイスのプロパティ、設定、およびコマンドへの一括更新を実行することができます。 この記事では、お使いのアプリケーションでジョブの使用を開始する方法を説明します。

## <a name="create-and-run-a-job"></a>ジョブの作成と実行

このセクションでは、ジョブを作成して実行する方法について説明します。 各ステップは、ファン速度を向上させる必要がある冷蔵機デバイス用のジョブを実行する方法を示す例について説明します。

1. ナビゲーション ウィンドウから、ジョブに移動します。

1. **[+ 新規]** をクリックして新しいジョブの作成を開始します。

    ![新しいジョブの作成](./media/howto-run-a-job-experimental/createnewjob.png)

1. 名前と、作成するジョブを識別する際に役立つ説明を入力します。

1. ジョブを適用するデバイス セットを選択します。 デバイス セットを選択すると、選択したデバイス セット内のデバイスが右側に表示されます。 壊れたデバイス セットを選択した場合、デバイスは表示されず、デバイス セットが壊れていることを説明するメッセージが表示されます。

1. 次に、定義するジョブの種類 (設定、プロパティ、またはコマンド) を選択します。選択したジョブの種類の次にある **[+]** をクリックして、目的の操作を追加します。

    ![ジョブを構成する](./media/howto-run-a-job-experimental/configurejob.png)

1. 右側で、ジョブを実行するデバイスを選択してください。 最上位のチェック ボックスをクリックすると、デバイス セット全体のすべてのデバイスが選択されます。 名前の近くのチェック ボックスをクリックすると、現在のページ上のすべてのデバイスが選択されます。

1. 必要なデバイスを選択したら、**[実行]** を選択します。 ジョブが **[ジョブ]** ページに表示されます。 このビューで、現在実行中のジョブと以前実行したジョブの履歴を表示することができます。 実行中のジョブは、一覧の上部に常に表示されます。

    ![ジョブの表示](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > 最大 30 日間の以前に実行したジョブの履歴を表示できます。

1. ジョブの概要を取得するには、一覧から表示するジョブの名前をクリックします。 この概要には、ジョブの詳細、デバイス、およびデバイスの状態が含まれています。

    ![デバイスの状態を表示する](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>実行中のジョブを停止する

現在実行中のジョブを停止したい場合は、停止したい実行中のジョブの名前をクリックします。 パネルの **[停止]** ボタンを選択します。 ジョブが停止していることを反映するようにジョブの状態が変化していることがわかります。

   ![ジョブを停止する](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>停止されたジョブを実行する

現在停止中のジョブを開始したい場合は、実行したい停止中のジョブの名前をクリックします。 パネルの **[実行]** ボタンを選択します。 ジョブが再び実行していることを反映するようにジョブの状態が変化していることがわかります。

   ![再開されたジョブ](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>ジョブの状態を表示する

ジョブが作成されたら、**[状態]** 列は、ジョブの最新のステータス メッセージで更新されます。 ステータス メッセージは、次を意味します。

| ステータス メッセージ       | ステータスの意味                                          |
| -------------------- | ------------------------------------------------------- |
| 完了            | このジョブはすべてのデバイスで実行されました。              |
| 失敗               | このジョブは失敗し、デバイスで完全には実行されませんでした。  |
| Pending              | このジョブはデバイスでまだ実行が開始されていません。        |
| 実行中              | このジョブはデバイスで実行中です。             |
| 停止済み              | このジョブはユーザーによって手動で停止されました。           |

ステータス メッセージには、ジョブ内のデバイスの概要が続きます。 これらのデバイスの状態は、次を意味します。

| ステータス メッセージ       | ステータスの意味                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | ジョブが正常に実行されたデバイスの数。  |
| 失敗               | ジョブが失敗したデバイスの数。      |

### <a name="view-the-device-status"></a>デバイスの状態を表示する

ジョブ内の各デバイスの状態を表示するには、ジョブの名前をクリックします。 ここに、ジョブの詳細と、この特定のジョブの一部であったすべてのデバイスが表示されます。 各デバイス名の隣に次のステータス メッセージのいずれかが表示されます。

| ステータス メッセージ       | ステータスの意味                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 完了            | このデバイスで、ジョブが実行されました。                                     |
| 失敗               | このデバイスで、ジョブが実行に失敗しました。 付随のエラー メッセージは詳細情報を表示します。  |
| Pending              | このデバイスで、ジョブがまだ実行されていません。                                  |

> [!NOTE]
> デバイスが削除された場合、デバイスを選択することはできず、デバイス ID を持つ削除済みとして表示されます。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションでジョブを作成する方法について説明しました。次の手順は以下のとおりです。

- [デバイス セットを使用する](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [デバイスの管理](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [バージョン、デバイス テンプレート](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
