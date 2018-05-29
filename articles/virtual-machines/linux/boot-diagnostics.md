---
title: Azure の Linux 仮想マシンのブート診断 | Microsoft Docs
description: Azure の Linux 仮想マシンの 2 つのデバッグ機能の概要
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941855"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>ブート診断を使用して、Azure の Linux 仮想マシンをトラブルシューティングする方法

Azure で新たに 2 つのデバッグ機能が利用できるようになりました。Azure Virtual Machines Resource Manager デプロイ モデルでコンソール出力とスクリーンショットがサポートされます。 

独自のイメージを Azure に導入した場合に限らず、いずれかのプラットフォーム イメージをブートするときでも、仮想マシンは、さまざまな理由で起動できない状態に陥ることがあります。 これらの機能を利用することで仮想マシンを簡単に診断し、起動エラーから復旧することができます。

Linux 仮想マシンについては、コンソール ログの出力をポータルから簡単に表示することができます。

![Azure ポータル](./media/boot-diagnostics/screenshot1.png)
 
一方、Windows 仮想マシンと Linux 仮想マシンの両方に関して、ハイパーバイザーからの VM のスクリーンショットを表示することもできます。

![エラー](./media/boot-diagnostics/screenshot2.png)

この 2 つの機能は、すべてのリージョンの Azure Virtual Machines でサポートされます。 スクリーンショットと出力結果がストレージ アカウントに表示されるまでに最大 10 分かかる場合があります。

## <a name="common-boot-errors"></a>一般的な起動エラー

- [ファイル システムの問題](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [カーネルの問題](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB エラー](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>新しい仮想マシンで診断を有効にする
1. Azure portal から新しい仮想マシンを作成するときに、デプロイ モデルのドロップダウンから **[Azure Resource Manager]** を選択します。
 
    ![リソース マネージャー](./media/boot-diagnostics/screenshot3.jpg)

2. **[設定]** で、**[ブート診断]** を有効にし、これらの診断ファイルを配置するストレージ アカウントを選択します。
 
    ![VM を作成する](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > ブート診断機能では、Premium ストレージ アカウントはサポートされていません。 ブート診断に Premium ストレージ アカウントを使用すると、VM の起動時に StorageAccountTypeNotSupported エラーが表示されることがあります。 
    >
    > 

3. Azure Resource Manager テンプレートからデプロイする場合は、ご利用の仮想マシン リソースに移動し、診断プロファイル セクションを追加します。 必ず "2015-06-15" という API バージョン ヘッダーを使用してください。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. これらのログの格納先となるストレージ アカウントは、診断プロファイルによって選択することができます。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

ブート診断が有効な状態でサンプル仮想マシンをデプロイするには、ここでリポジトリをチェックアウトします。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>既存の仮想マシンでブート診断を有効にする 

既存の仮想マシンでブート診断を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にログインし、仮想マシンを選択します。
2. **[サポート + トラブルシューティング]** で、**[ブート診断]** > **[設定]** を選択し、状態を **[オン]** に変更して、ストレージ アカウントを選びます。 
4. ブート診断オプションが選択されていることを確認し、変更を保存します。

    ![既存の VM の更新](./media/boot-diagnostics/enable-for-existing-vm.png)

3. VM を再起動すると変更が反映されます。

## <a name="next-steps"></a>次の手順

VM ブート診断の使用時に "ログのコンテンツを取得できなかった" ことを示すエラーが表示された場合は、[VM ブート診断のログのコンテンツを取得できなかったことを示すエラー](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur)に関するページを参照してください。