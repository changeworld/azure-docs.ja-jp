---
title: Azure でご自分のアプリをテストする方法
description: デスクトップ/Web アプリケーションをファイル共有にデプロイしてテストする方法について説明します。
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: af9fc320c6f08e0ef4141aac8076e121a2e6292d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575943"
---
# <a name="test-your-app-in-azure"></a>Azure でアプリをテストする 

このガイドでは、DevTest Labs を使用して Azure でアプリケーションをテストする方法について説明します。 Visual Studio を使用してアプリを Azure ファイル共有にデプロイします。 次に、ラボの仮想マシン (VM) から共有にアクセスします。  

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [Visual Studio](https://visualstudio.microsoft.com/free-developer-offers/) がインストールされているワークステーション。

- [DevTest Labs](devtest-lab-overview.md) 内のラボ。

- ラボ内で Windows を実行している [Azure 仮想マシン](devtest-lab-add-vm.md)。

- ラボの既存の Azure ストレージ アカウント内の[ファイル共有](../storage/files/storage-how-to-create-file-share.md)。 ストレージ アカウントはラボで自動的に作成されます。

- ローカル ワークステーションとラボの VM に[マウントされた Azure ファイル共有](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)。

## <a name="publish-your-app-from-visual-studio"></a>Visual Studio から アプリを発行する

このセクションでは、Visual Studio から Azure ファイル共有にアプリを発行します。

1. Visual Studio を開き、[開始] ウィンドウで **[新しいプロジェクトの作成]** を選択します。

    :::image type="content" source="./media/test-app-in-azure/launch-visual-studio.png" alt-text="Visual Studio の開始ページのスクリーンショット。":::

1. **[コンソール アプリケーション]** 、 **[次へ]** の順に選択します。

    :::image type="content" source="./media/test-app-in-azure/select-console-application.png" alt-text="[コンソール アプリケーション] を選択するオプションのスクリーンショット。":::

1. **[新しいプロジェクトを構成します]** ページで、既定値をそのまま使用し、 **[次へ]** を選択します。

1. **[追加情報]** ページで、既定値をそのまま使用し、 **[作成]** を選択します。

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[ビルド]** を選択します。

1. **[ソリューション エクスプローラー]** で、プロジェクトを右クリックして **[発行]** を選択します。

    :::image type="content" source="./media/test-app-in-azure/publish-application.png" alt-text="アプリケーションを発行するオプションのスクリーンショット。":::

1. **[発行]** ページで、 **[フォルダー]** 、 **[次へ]** の順に選択します。

    :::image type="content" source="./media/test-app-in-azure/publish-to-folder.png" alt-text="フォルダーに発行するオプションのスクリーンショット。":::

1. **[特定のターゲット]** オプションで、 **[フォルダー]** 、 **[次へ]** の順に選択します。

1. **[場所]** オプションで、 **[参照]** を選択し、前にマウントしたファイル共有を選択します。 次に、 **[OK]** 、 **[完了]** の順に選択します。 

    :::image type="content" source="./media/test-app-in-azure/selecting-file-share.png" alt-text="ファイル共有を選択するオプションのスクリーンショット。":::

1. **[発行]** を選択します。 Visual Studio によってアプリケーションがビルドされ、ファイル共有に発行されます。

    :::image type="content" source="./media/test-app-in-azure/final-publish.png" alt-text="[発行] ボタンのスクリーンショット。":::

## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>ラボのテスト VM でアプリをテストする

1. ラボの仮想マシンに接続します。

1. 仮想マシン内で **ファイル エクスプローラー** を起動し、 **[This PC]\(この PC\)** を選択して前にマウントしたファイル共有を検索します。

    :::image type="content" source="./media/test-app-in-azure/find-share-on-vm.png" alt-text="ファイル エクスプローラーのスクリーンショット。":::

1. ファイル共有を開き、Visual Studio からデプロイされたアプリが表示されることを確認します。 

    :::image type="content" source="./media/test-app-in-azure/open-file-share.png" alt-text="ファイル共有の内容のスクリーンショット。":::

    Azure で作成した テスト VM 内でアプリにアクセスしテストできるようになりました。

## <a name="next-steps"></a>次のステップ

ラボで VM を使用する方法については次のアーティクルを参照してください。 

- [VM をラボに追加する](devtest-lab-add-vm.md)
- [ラボ VM を再起動する](devtest-lab-restart-vm.md)
- [ラボ VM のサイズを変更する](devtest-lab-resize-vm.md)
