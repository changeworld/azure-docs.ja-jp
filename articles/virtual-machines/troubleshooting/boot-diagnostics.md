---
title: Azure での VM のブート診断 | Microsoft Docs
description: Azure 内の仮想マシンに対する 2 つのデバッグ機能の概要
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 64641f8acfe7b58763756e2a0707fa799ee804b2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412594"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>ブート診断を使用して、Azure 内の仮想マシンをトラブルシューティングする方法

Azure で新たに 2 つのデバッグ機能が利用できるようになりました。Azure Virtual Machines Resource Manager デプロイ モデルでコンソール出力とスクリーンショットがサポートされます。 

独自のイメージを Azure に導入した場合に限らず、いずれかのプラットフォーム イメージをブートするときでも、仮想マシンは、さまざまな理由で起動できない状態に陥ることがあります。 これらの機能を利用することで仮想マシンを簡単に診断し、起動エラーから復旧することができます。

Linux 仮想マシンについては、コンソール ログの出力をポータルから簡単に表示できます。 Azure では、Windows 仮想マシンと Linux 仮想マシンの両方に関して、ハイパーバイザーからの VM のスクリーンショットを表示することもできます。 この 2 つの機能は、すべてのリージョンの Azure Virtual Machines でサポートされます。 スクリーンショットと出力結果がストレージ アカウントに表示されるまでに最大 10 分かかる場合があります。

## <a name="common-boot-errors"></a>一般的な起動エラー

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [An operating system wasn't found (オペレーティング システムが見つかりませんでした)](https://support.microsoft.com/help/4010142)
- [Boot failure or INACCESSIBLE_BOOT_DEVICE (起動エラーまたは INACCESSIBLE_BOOT_DEVICE)](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>新しい仮想マシンで診断を有効にする
1. Azure Portal から新しい仮想マシンを作成するときに、デプロイメント モデルのドロップダウンから **[Azure Resource Manager]** を選択します。
 
    ![リソース マネージャー](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. **[設定]** で、**[ブート診断]** を有効にし、これらの診断ファイルを配置するストレージ アカウントを選択します。
 
    ![VM を作成する](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

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

1. [Azure portal](https://portal.azure.com) にサインインし、仮想マシンを選択します。
2. **[サポート + トラブルシューティング]** で、**[ブート診断]** > **[設定]** を選択し、状態を **[オン]** に変更して、ストレージ アカウントを選びます。 
4. ブート診断オプションが選択されていることを確認し、変更を保存します。

    ![既存の VM の更新](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. VM を再起動すると変更が反映されます。


