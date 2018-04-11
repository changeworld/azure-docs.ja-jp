---
title: Azure の Linux 仮想マシンのブート診断 | Microsoft Docs
description: Azure の Linux 仮想マシンの 2 つのデバッグ機能の概要
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: timlt
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
ms.openlocfilehash: bf8e1b338012898ed3de3f443cf492b6890af796
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>ブート診断を使用して、Azure の Linux 仮想マシンをトラブルシューティングする方法

Azure で新たに 2 つのデバッグ機能が利用できるようになりました。Azure Virtual Machines Resource Manager デプロイメント モデルでコンソール出力とスクリーンショットがサポートされます。 

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
1. プレビュー ポータルから新しい仮想マシンを作成するとき、デプロイメント モデルのドロップダウンから **[Azure Resource Manager]** を選択します。
 
    ![リソース マネージャー](./media/boot-diagnostics/screenshot3.jpg)

2. [監視] のオプションを構成し、これらの診断ファイルの格納先となるストレージ アカウントを選択します。
 
    ![VM を作成する](./media/boot-diagnostics/screenshot4.jpg)

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

## <a name="update-an-existing-virtual-machine"></a>既存の仮想マシンを更新する

ポータルからブート診断を有効にするために、既存の仮想マシンをポータルで更新することもできます。 [ブート診断] オプションを選択し、[保存] を選択します。 VM を再起動すると変更が反映されます。

![既存の VM の更新](./media/boot-diagnostics/screenshot5.png)

## <a name="next-steps"></a>次の手順

VM ブート診断の使用時に "ログのコンテンツを取得できなかった" ことを示すエラーが表示された場合は、[VM ブート診断のログのコンテンツを取得できなかったことを示すエラー](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur)に関するページを参照してください。