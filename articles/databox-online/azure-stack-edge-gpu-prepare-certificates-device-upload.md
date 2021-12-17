---
title: Azure Stack Edge Pro GPU/Pro R/Mini R でアップロードするために証明書を準備する
description: Azure Stack Edge Pro GPU/Pro R/Mini R デバイスでアップロードするために証明書を準備する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/30/2021
ms.author: alkohli
ms.openlocfilehash: fa908a75a09beefe00babe0e710f43c4d74c3aaf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360529"
---
# <a name="prepare-certificates-to-upload-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU でアップロードするために証明書を準備する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge デバイスでアップロードできるよう、証明書を適切な書式に変換する方法について説明します。 この手順は通常、独自の証明書を持ち込むときに必要です。

これらの証明書の作成方法に関する詳細については、[Azure PowerShell で証明書を作成する](azure-stack-edge-gpu-create-certificates-powershell.md)方法に関するページを参照してください。


## <a name="prepare-certificates"></a>証明書の準備

独自の証明書を持ち込む場合、デバイス用に作成した証明書は、既定では、クライアントの **個人用ストア** に格納されます。 これらの証明書は、デバイスにアップロードできるように、クライアントで適切なフォーマット ファイルにエクスポートする必要があります。

- **ルート証明書の準備**: ルート証明書は、`.cer` 拡張子を使用した DER 形式としてエクスポートする必要があります。 詳細な手順については、[DER 形式での証明書のエクスポート](#export-certificates-as-der-format)に関するページを参照してください。

- **エンドポイント証明書の準備**: エンドポイント証明書は、秘密キーを含む *.pfx* ファイルとしてエクスポートする必要があります。 詳細な手順については、[秘密キーを含む *.pfx* ファイルとしての証明書のエクスポート](#export-certificates-as-pfx-format-with-private-key)に関するページを参照してください。 


## <a name="export-certificates-as-der-format"></a>DER 形式で証明書をエクスポートする

1. *certlm.msc* を実行して、ローカル コンピューターの証明書ストアを起動します。

1. 個人用証明書ストアで、ルート証明書を選択します。 右クリックし、 **[すべてのタスク] > [エクスポート...]** を選択します。

    ![証明書の DER をエクスポートする 1](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-1.png)

2. 証明書ウィザードが開きます。 形式として **[DER encoded binary X.509 (.cer)]** を選択します。 **[次へ]** を選択します。

    ![証明書の DER をエクスポートする 2](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-2.png)

3. .cer 形式のファイルをエクスポートする場所を参照して選択します。

    ![証明書の DER をエクスポートする 3](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-3.png)

4. **[完了]** を選択します。

    ![証明書の DER をエクスポートする 4](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-4.png)


## <a name="export-certificates-as-pfx-format-with-private-key"></a>秘密キーを含む .pfx 形式で証明書をエクスポートする

秘密キー付きの SSL 証明書を Windows コンピューターにエクスポートするには、次の手順を実行します。 

> [!IMPORTANT]
> これらの手順は、証明書の作成に使用したのと同じコンピューターで実行してください。 

1. *certlm.msc* を実行して、ローカル コンピューターの証明書ストアを起動します。

1. **[個人]** フォルダーをダブルクリックし、 **[証明書]** をダブルクリックします。

    ![証明書のエクスポート 1](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-1.png)
 
2. バックアップする証明書を右クリックし、 **[すべてのタスク] > [エクスポート...]** を選択します。

    ![証明書のエクスポート 2](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-2.png)

3. 証明書のエクスポート ウィザードに従い、証明書を .pfx ファイルにバックアップします。

    ![証明書のエクスポート 3](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-3.png)

4. **[はい、秘密キーをエクスポートします]** を選択します。

    ![証明書のエクスポート 4](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-4.png)

5. **[Include all certificates in certificate path if possible]\(可能な場合は証明書パスのすべての証明書を含める\)** 、 **[すべての拡張プロパティをエクスポートする]** 、 **[証明書のプライバシーを有効にする]** を選択します。 

    > [!IMPORTANT]
    > **[Delete Private Key option if export is successful]\(エクスポートが成功した場合に秘密キーを削除する (オプション)\)** は選択しないでください。

    ![証明書のエクスポート 5](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-5.png)

6. パスワードを入力し、これを憶えておきます。 パスワードを確認入力します。 パスワードによって秘密キーが保護されます。

    ![証明書のエクスポート 6](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-6.png)

7. 設定した場所にファイルを保存することを選択します。

    ![証明書のエクスポート 7](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-7.png)
  
8. **[完了]** を選択します。

    ![証明書のエクスポート 8](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-8.png)

9. エクスポートが成功したことを示すメッセージが表示されます。 **[OK]** を選択します。

    ![証明書のエクスポート 9](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-9.png)

.pfx ファイルのバックアップが選択した場所に保存され、安全に保存するために移動または保管する準備ができました。


## <a name="next-steps"></a>次のステップ

[お使いのデバイスで証明書をアップロードする](azure-stack-edge-gpu-manage-certificates.md)方法を学習します。
