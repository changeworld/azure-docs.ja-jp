---
title: Azure Percept Audio 音声アシスタント アプリケーションを削除する
description: この記事では、以前に作成した音声アシスタント アプリケーションを削除する方法について説明します。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b62ba69695606e04e7044109818c18d30ca21c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779400"
---
# <a name="delete-your-voice-assistant-application"></a>音声アシスタント アプリケーションを削除する

これらの手順では、Azure Percept Audio デバイスから音声アシスタント アプリケーションを削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- [以前に作成した音声アシスタント アプリケーション](./tutorial-no-code-speech.md)
- Azure Percept DK の電源がオンになっており、Azure Percept Audio アクセサリが USB ケーブル経由で接続されていること。

## <a name="remove-all-voice-assistant-resources-from-the-azure-portal"></a>すべての音声アシスタント リソースを Azure portal から削除する

音声アシスタント アプリケーションの作業が完了したら、こちらの手順に従って、このアプリケーションの作成時にデプロイした音声リソースをクリーンアップしてください。

1. [Azure portal](https://portal.azure.com) の左側のメニュー パネルから **[リソース グループ]** を選択するか、検索バーにそのように入力します。

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="左側のメニュー パネルとリソース グループが表示されている Azure portal ホームページのスクリーンショット。":::

1. リソース グループを選択します。

1. アプリケーションのプレフィックスが含まれている 6 つのリソースをすべて選択し、上部のメニュー パネルにある **[削除]** アイコンを選択します。

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="削除対象として選択された音声リソースのスクリーンショット。":::

1. 削除を確定するには、確認ボックスに「**yes**」と入力し、正しいリソースが選択されていることを確認して、 **[削除]** を選択します。

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="削除の確認ウィンドウのスクリーンショット。":::

> [!WARNING]
> 削除する音声リソースと共に作成されたカスタム キーワードがすべて削除され、音声アシスタントのデモは機能しなくなります。


## <a name="next-steps"></a>次のステップ
音声アシスタント アプリケーションを削除したら、こちらのチュートリアルに従って、Azure Percept DK で他のアプリケーションを作成してみてください。
- [コードなしのビジョン ソリューションを作成する](./tutorial-nocode-vision.md)
- [コードなしの音声アシスタント アプリケーションを作成する](./tutorial-no-code-speech.md)


