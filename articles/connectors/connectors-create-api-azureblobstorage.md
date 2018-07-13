---
title: Azure Blob Storage に接続する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用して Azure ストレージ内に BLOB を作成して管理します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 49d08135dee4568d1a9d65ec2d22d17ee3bda2ea
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "35294681"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Azure Logic Apps を使用して Azure BLOB ストレージ内に BLOB を作成して管理する

この記事では、ロジック アプリ内から Azure Blob Storage コネクタを使用して Azure ストレージ アカウントに BLOB として格納されているファイルにアクセスして管理する方法を示します。 その方法で、ファイルを管理するためのタスクとワークフローを自動化するロジック アプリを作成できます。 たとえば、ストレージ アカウントに対してファイルを作成、取得、更新、および削除するロジック アプリをビルドできます。

更新されるツールが Azure Web サイト上にあるとします。 それはロジック アプリのトリガーとして機能します。 そのイベントが発生したときに、BLOB ストレージ コンテナー内のいくつかのファイルをロジック アプリに更新させることができます。更新はロジック アプリで実行されるアクションです。 

Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と「[クイックスタート: 初めてのロジック アプリ ワークフローの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)」を参照してください。
コネクタ固有の技術情報については、<a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">Azure Blob Storage コネクタ リファレンス</a>に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure ストレージ アカウントとストレージ コンテナー](../storage/blobs/storage-quickstart-blobs-portal.md)

* Azure Blob Storage にアクセスする必要があるロジック アプリ。 Azure Blob Storage トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Blob Storage トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、BLOB のプロパティがストレージ コンテナーに追加されるか BLOB のプロパティが更新された場合に **[Azure Blob Storage - BLOB が追加または変更されたとき (プロパティのみ)]** トリガーを使用して、ロジック アプリのワークフローを開始する方法を示します。 

1. Azure Portal または Visual Studio で、Logic Apps デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

2. 検索ボックスに、フィルターとして「azure blob」と入力します。 トリガーの一覧から、目的のトリガーを選択します。

   この例では、**[Azure Blob Storage -BLOB が追加または変更されたとき (プロパティのみ)]** トリガーを使用します。

   ![トリガーの選択](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. 接続の詳細の入力を求められたら、[BLOB ストレージ接続を今すぐ作成](#create-connection)します。 接続が既に存在する場合は、トリガーに必要な情報を指定します。

   この例では、監視するコンテナーとフォルダーを選択します。

   1. **[コンテナー]** ボックスで、フォルダー アイコンを選択します。

   2. フォルダーの一覧で、右山かっこ (**>**) を選択し、目的のフォルダーを参照して選択します。 

      ![フォルダーの選択](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. トリガーがフォルダーの変更をチェックする間隔と頻度を選択します。

4. 操作が完了したら、デザイナーのツールバーで、**[保存]** を選択します。

5. トリガーの結果を使用して実行するタスクの 1 つまたは複数のアクションをロジック アプリに追加する操作に進みます。

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB ストレージ アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例では、ロジック アプリは、[繰り返しトリガー](../connectors/connectors-native-recurrence.md)を使用して起動されます。

1. Azure Portal または Visual Studio で、ロジック アプリをロジック アプリ デザイナーで開きます。 この例では、Azure Portal を使用します。

2. Logic Apps デザイナーのトリガーまたはアクションで、**[新しいステップ]** > **[アクションの追加]** を選択します。

   ![アクションを追加する](./media/connectors-create-api-azureblobstorage/add-action.png) 

   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]** を選択します。

3. 検索ボックスに、フィルターとして「azure blob」と入力します。 アクションの一覧から、目的のアクションを選択します。

   この例では、**[Azure Blob Storage - BLOB コンテンツの取得]** というアクションを使用します。

   ![選択アクション](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. 接続の詳細の入力を求められたら、[Azure Blob Storage 接続を今すぐ作成](#create-connection)します。 接続が既に存在する場合は、アクションに必要な情報を指定します。 

   この例では、目的のファイルを選択します。

   1. **[BLOB]** ボックスで、フォルダー アイコンを選択します。
  
      ![フォルダーの選択](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. BLOB の **ID** 番号に基づいて目的のファイルを見つけて選択します。 この **ID** 番号は、前に説明した Blob Storage トリガーによって返される BLOB のメタデータ内で見つけることができます。

5. 操作が完了したら、デザイナーのツールバーで、**[保存]** を選択します。
ロジック アプリをテストするには、選択したフォルダーに BLOB が含まれていることを確認します。

この例では、BLOB の内容の取得のみが実行されます。 内容を表示するには、別のコネクタを使用して、BLOB のファイルを作成する別のアクションを追加します。 たとえば、BLOB の内容に基づいてファイルを作成する OneDrive アクションを追加します。

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>ストレージ アカウントに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/azureblobconnector/)を参照してください。 

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
