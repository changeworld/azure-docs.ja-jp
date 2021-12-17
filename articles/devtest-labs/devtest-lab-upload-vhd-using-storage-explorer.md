---
title: Storage Explorer を使用して VHD ファイルをアップロードする
description: Microsoft Azure Storage Explorer を使用して、VHD ファイルを DevTest Labs ラボ ストレージ アカウントにアップロードします。
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 2531964c056ddbed38da435e16bde3e0f5e1eff9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054052"
---
# <a name="upload-a-vhd-file-to-a-labs-storage-account-by-using-storage-explorer"></a>Storage Explorer を使用して、VHD ファイルをラボのストレージ アカウントにアップロードします

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用して、仮想マシンをプロビジョニングするためのカスタム イメージを作成できます。 この記事では、[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示します。 VHD ファイルを DevTest Labs にアップロードすると、アップロードしたファイルから[カスタム イメージを作成](devtest-lab-create-custom-image-from-vhd-using-powershell.md)できます。 Azure のディスクと VHD の詳細については、「[マネージド ディスクの概要](../virtual-machines/managed-disks-overview.md)」を参照してください。

Storage Explorer では、複数の接続オプションがサポートされます。 この記事では、Azure サブスクリプションに関連付けられているストレージ アカウントへの接続を示します。 その他の Storage Explorer 接続オプションの詳細については、「[Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」を参照してください。

## <a name="prerequisites"></a>必須コンポーネント

- [最新バージョンの Microsoft Azure Storage Explorer をダウンロードしてインストールします](https://www.storageexplorer.com)。

- Azure portal を使用して、ラボのストレージ アカウントの名前を取得します。

  1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) で、 **[DevTest Labs]** を検索して選択し、一覧からラボを選択します。
  1. ラボのページで、左側のナビゲーションから **[構成とポリシー]** を選択します。 
  1. **[構成とポリシー]** ページの **[仮想マシンのベース]** の下で **[カスタム イメージ]** を選択します。
  1. **[カスタム イメージ]** ページで、 **[追加]** を選択します。 
  1. **[カスタム イメージ]** ページの **[VHD]** の下で、 **[PowerShell を使用して VHD ファイルをアップロードする]** を選択します。
     ![PowerShell リンクを使用した VHD のアップロードを示すスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png)
  1. **[PowerShell を使用したイメージのアップロード]** ページの、**Add-AzureVhd** コマンドレットへの呼び出しで、`Destination` パラメーターは、ラボのストレージ アカウント名を次の形式で表示します: `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/`。
  1. 次の手順で使用するストレージ アカウント名をコピーします。

## <a name="step-by-step-instructions"></a>詳細な手順

1. Storage Explorer を開くと、左側の **[Explorer]** ウィンドウに、サインインしているすべての Azure サブスクリプションが表示されます。

   別のアカウントを追加する必要がある場合は、 **[アカウント管理]** アイコンを選択し、 **[アカウント管理]** ウィンドウで **[アカウントの追加]** を選択します。

   ![[アカウント管理] ウィンドウに [アカウントの追加] と表示されているスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png)

   プロンプトに従って、Azure サブスクリプションに関連付けられている Microsoft アカウントでサインインします。

1. サインインすると、 **[Explorer]** ウィンドウに、アカウントに関連付けられている Azure サブスクリプションが表示されます。 使用する Azure サブスクリプションの横にあるドロップダウン矢印を選択します。 左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。

   ![選択した Azure サブスクリプションのストレージ アカウントを示すスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png)

1. 前の手順で保存したラボ ストレージ アカウント名の横にあるドロップダウン矢印を選択します。

1. **[BLOB コンテナー]** ノードを展開し、 **[アップロード]** を選択します。

   ![アップロード ディレクトリと共に展開された [BLOB コンテナー] ノードを示すスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png)

1. Storage Explorer の右ペインの BLOB エディター ツールバーで、 **[アップロード]** を選択し、 **[ファイルのアップロード]** を選択します。 

   ![[アップロード] ボタンと [ファイルのアップロード] を示すスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png)

1. **[ファイルのアップロード]** ダイアログ ボックスで、 **[選択したファイル]** フィールドの横にある **[...]** を選択し、マシン上の VHD ファイルを参照して選択し、 **[開く]** を選択します。

1. **[BLOB の種類]** で、 **[ブロック BLOB]** を **[ページ BLOB]** に変更します。

1. **[アップロード]** を選択します。

   ![[ファイルのアップロード] ダイアログボックスを示すスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png)

下部にある **[アクティビティ]** ウィンドウには、アップロードの状態が表示されます。 VHD ファイルのサイズと接続速度によっては、VHD ファイルのアップロードに時間がかかることがあります。

![[アクティビティ] ウィンドウにアップロード状態が表示されているスクリーンショット。](media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png)

## <a name="next-steps"></a>次のステップ

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

