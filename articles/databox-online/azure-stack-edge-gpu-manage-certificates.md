---
title: Azure Stack Edge Pro GPU で証明書を使用する | Microsoft Docs
description: 証明書を使用する理由、証明書の種類、デバイスへの証明書のアップロード方法など、Azure Stack Edge Pro GPU デバイスでの証明書の使用について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: 15cfd2b7188f84e14aa12824f8d5fab06d226330
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363972"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 上で証明書をアップロード、インポート、およびエクスポートする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge デバイスとそれに接続するクライアント間のセキュリティで保護されて信頼できる通信を確保するために、自己署名証明書を使用することも、独自の証明書を持ち込むこともできます。 この記事では、これらの証明書をアップロード、インポート、エクスポートする方法、有効期限を表示する方法など、これらの証明書を管理する方法について説明します。

これらの証明書の作成方法に関する詳細については、[Azure PowerShell で証明書を作成する](azure-stack-edge-gpu-create-certificates-powershell.md)方法に関するページを参照してください。


## <a name="upload-certificates-on-your-device"></a>デバイスに証明書をアップロードする

独自の証明書を持ち込む場合、デバイス用に作成した証明書は、既定では、クライアントの **個人用ストア** に格納されます。 これらの証明書は、デバイスにアップロードできるように、クライアントで適切なフォーマット ファイルにエクスポートする必要があります。


### <a name="prerequisites"></a>前提条件

ルート証明書とエンドポイント証明書をデバイスにアップロードする前に、証明書が適切な形式でエクスポートされていることを確認してください。

- ルート証明書は、`.cer` 拡張子を使用した DER 形式としてエクスポートする必要があります。 詳細な手順については、[DER 形式での証明書のエクスポート](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format)に関するページを参照してください。
- エンドポイント証明書は、秘密キーを含む *.pfx* ファイルとしてエクスポートする必要があります。 詳細な手順については、[秘密キーを含む *.pfx* ファイルとしての証明書のエクスポート](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)に関するページを参照してください。 

### <a name="upload-certificates"></a>証明書をアップロードする 

ルート証明書とエンドポイント証明書をデバイスにアップロードするには、ローカル Web UI の **[証明書]** ページの **[+ 証明書の追加]** オプションを使用します。 これらの手順に従います。

1. 最初にルート証明書をアップロードします。 ローカル Web UI で、 **[証明書]** に移動します。
1. **[+ 証明書の追加]** を選択します。

    ![署名チェーン証明書を追加する 1](media/azure-stack-edge-gpu-manage-certificates/add-cert-1.png)

1. 証明書を保存します。

#### <a name="upload-endpoint-certificate"></a>エンドポイント証明書をアップロードする

1. 次に、エンドポイント証明書をアップロードします。 

    ![署名チェーン証明書を追加する 2](media/azure-stack-edge-gpu-manage-certificates/add-cert-2.png)

    *.pfx* 形式の証明書ファイルを選択し、証明書のエクスポート時に指定したパスワードを入力します。 Azure Resource Manager 証明書が適用されるまでに数分かかる場合があります。

    最初に署名チェーンが更新されていない場合、エンドポイント証明書をアップロードしようとすると、エラーが発生します。

    ![証明書の適用エラー](media/azure-stack-edge-gpu-manage-certificates/apply-cert-error-1.png)

    前に戻り、署名チェーン証明書をアップロードし、その後にエンドポイント証明書をアップロードし、適用します。

> [!IMPORTANT]
> デバイス名または DNS ドメインが変更された場合、新しい証明書を作成する必要があります。 次に、クライアント証明書とデバイス証明書を、新しいデバイス名と DNS ドメインを使用して更新する必要があります。 

#### <a name="upload-kubernetes-certificates"></a>Kubernetes 証明書をアップロードする

Kubernetes 証明書は、Edge Container Registry か Kubernetes ダッシュボードに使用できます。 いずれの場合も、証明書とキー ファイルをアップロードする必要があります。 Kubernetes 証明書を作成してアップロードするには、次の手順に従います。


1. `openssl` を使用し、Kubernetes ダッシュボード証明書または Edge Container Registry を作成します。 証明書の作成に使用するシステムに必ず openssl をインストールしてください。 Windows で、Chocolatey を使用して `openssl` をインストールできます。 Chocolatey をインストールしたら、PowerShell を開き、次のコマンドを入力します。
    
    ```powershell
    choco install openssl
    ```
1. これらの証明書を作成するには `openssl` を使用します。 `cert.pem` 証明書ファイルと `key.pem` キー ファイルが作成されます。  

    - Edge Container Registry の場合、次のコマンドを使用します。
    
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<ecr.endpoint-suffix>"
        ``` 
        出力例を次に示します。 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=ecr.dbe-1d6phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```    
    - Kubernetes ダッシュボード証明書の場合、次のコマンドを使用します。  
     
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<<kubernetes-dashboard.endpoint-suffix> OR <endpoint-suffix>>"
        ```
        出力例を次に示します。 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=kubernetes-dashboard.dbe-1d8phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```          
1. Kubernetes 証明書とそれに該当する、前に生成したキー ファイルをアップロードします。
    
    - Edge Container Registry の場合
    
        ![Edge Container Registry の証明書とキー ファイルを追加するスクリーンショット](media/azure-stack-edge-gpu-manage-certificates/add-cert-3.png)      

    - Kubernetes ダッシュボードの場合     

        ![Kubernetes ダッシュボードの証明書とキー ファイルを追加するスクリーンショット](media/azure-stack-edge-gpu-manage-certificates/add-cert-4.png) 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>デバイスにアクセスするクライアントに証明書をインポートする

デバイスで生成された証明書を使用するか、独自の証明書を持ち込みます。 デバイスで生成された証明書を使用する場合は、適切な証明書ストアに証明書をインポートする前に、クライアントに証明書をダウンロードする必要があります。 [デバイスにアクセスするクライアントに証明書をダウンロードする](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)方法に関するページを参照してください。

どちらの場合も、作成してデバイスにアップロードした証明書を、(デバイスにアクセスする) Windows クライアントの適切な証明書ストアにインポートする必要があります。 

- DER としてエクスポートしたルート証明書は、クライアント システムの **信頼されたルート証明機関** にインポートする必要があります。 詳細な手順については、[信頼されたルート証明機関ストアへの証明書のインポート](#import-certificates-as-der-format)に関するページを参照してください。

- `.pfx` としてエクスポートしたエンドポイント証明書は、`.cer` 拡張子を使用した DER としてエクスポートする必要があります。 この `.cer` は、システムの **個人用証明書ストア** にインポートします。 詳細な手順については、[個人用証明書ストアへの証明書のインポート](#import-certificates-as-der-format)に関するページを参照してください。

### <a name="import-certificates-as-der-format"></a>DER 形式で証明書をインポートする 

Windows クライアントに証明書をインポートするには、次の手順を実行します。

1. ファイルを右クリックし、 **[証明書のインストール]** を選択します。 このアクションにより、証明書のインポート ウィザードが開始されます。

    ![証明書のインポート 1](media/azure-stack-edge-gpu-manage-certificates/import-cert-1.png)

2. **[ストアの場所]** で **[ローカル マシン]** を選択し、 **[次へ]** を選択します。

    ![証明書のインポート 2](media/azure-stack-edge-gpu-manage-certificates/import-cert-2.png)

3. **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** を選択します。 

    - 個人用ストアにインポートするには、リモート ホストの個人用ストアに移動し、 **[次へ]** を選択します。

        ![証明書のインポート 4](media/azure-stack-edge-gpu-manage-certificates/import-cert-4.png)


    - 信頼されたストアにインポートするには、信頼されたルート証明機関に移動し、 **[次へ]** を選択します。

        ![証明書のインポート 3](media/azure-stack-edge-gpu-manage-certificates/import-cert-3.png)

 
4. **[完了]** を選択します。 インポートが成功したことを示すメッセージが表示されます。


## <a name="view-certificate-expiry"></a>証明書の有効期限を確認する

独自の証明書を導入する場合、証明書は通常 1 年または 6 か月で期限切れになります。 証明書の有効期限を確認するには、デバイスのローカル Web UI の **[証明書]** ページにアクセスします。 特定の証明書を選択すると、その証明書の有効期限を確認できます。


## <a name="next-steps"></a>次のステップ

[証明書の問題をトラブルシューティングする](azure-stack-edge-gpu-certificate-troubleshooting.md)方法について学習します
