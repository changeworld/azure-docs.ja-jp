---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2021
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f2dcb92d0a600c57d96348413704863285cea3f0
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987195"
---
## <a name="allow-azure-to-call-your-local-function"></a>Azure でローカル関数を呼び出せるようにする

マシン上でデバッグ中の関数を中断するには、Azure でクラウドからローカル関数と通信する方法を有効にする必要があります。

[ngrok](https://ngrok.com/) ユーティリティでは、マシンで実行されている関数を Azure で呼び出すことができます。 次のコマンドを使用して *ngrok* を起動します。

```bash
ngrok http -host-header=localhost 7071
```
ユーティリティがセットアップされると、次のスクリーンショットのようなコマンド ウィンドウが表示されます。

!["ngrok" ユーティリティを起動した後のコマンド プロンプトを示すスクリーンショット。](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-ngrok.png)

*ngrok* が実行されたときに生成される **HTTPS** URL をコピーします。 この値は、Event Grid イベント エンドポイントを構成するときに使用されます。

## <a name="add-a-storage-event"></a>ストレージ イベントを追加する

1. Azure portal を開き、ストレージ アカウントに移動して、左側のメニューから **[イベント]** オプションを選択します。

    ![ストレージ アカウント イベントを追加する](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-add-event.png)

1. *[イベント]* ウィンドウで、 **[イベント サブスクリプション]** ボタンを選択します。 

1. *[イベント サブスクリプション]* ウィンドウで、 *[エンドポイントの種類]* ドロップダウンを選択し、 **[webhook]** を選択します。

    ![サブスクリプションの種類を選択する](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-type.png)

1. エンドポイントの種類が構成されたら、 **[エンドポイントを選択する]** をクリックしてエンドポイント値を構成します。

    ![エンドポイントの種類を選択する](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint.png)

    *[サブスクライバー エンドポイント]* の値は 3 つの異なる値から構成されます。 プレフィックスは、*ngrok* によって生成される HTTPS URL です。 URL の残りの部分は、ガイド内で事前にコピーされた localhost URL から取得され、末尾に関数名が付加されます。 localhost URL 以降では、*ngrok* URL が `http://localhost:7071` に置き換えられ、関数名が `{functionname}` に置き換えられます。

1. 次のスクリーンショットは、`Event Grid` トリガーの種類を使用する場合の最終的な URL の表示例を示して います。

    ![エンドポイントの選択](./media/functions-event-grid-local-dev/functions-event-grid-local-dev-event-subscription-endpoint-selection.png)

1. 適切な値を入力したら、 **[選択内容の確認]** をクリックします。

> [!IMPORTANT]
> *ngrok* を起動するたびに、HTTPS URL が再生成され、値が変更されます。 そのため、*ngrok* を使用して Azure に関数を公開するたびに、新しいイベント サブスクリプションを作成する必要があります。

## <a name="upload-a-file"></a>ファイルをアップロードする

これで、ストレージ アカウントにファイルをアップロードして、ローカル関数で処理する Event Grid イベントをトリガーすることができます。 

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を開いて、ストレージ アカウントに接続します。 

- **BLOB コンテナー** を展開します 
- 右クリックし、 **[BLOB コンテナーの作成]** を選択します。
- コンテナー **samples-workitems** 名
- *samples-workitems* コンテナーを作成します
- **[アップロード]** ボタンをクリックします
- **[ファイルのアップロード]** をクリックします
- ファイルを選択して、BLOB コンテナーにアップロードしま

