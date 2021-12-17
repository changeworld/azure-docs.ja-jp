---
title: Azure Stack Edge Pro GPU、Pro R、Mini R 証明書の概要
description: Azure Stack Edge Pro GPU デバイスで使用できるすべての証明書の概要について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/01/2021
ms.author: alkohli
ms.openlocfilehash: c5ea156773335ed31fe6c6e728f496f91c6de634
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215635"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU の証明書とは

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスにインストールできる証明書の種類について説明します。 この記事には、各証明書の種類の詳細も記載します。  

## <a name="about-certificates"></a>証明書について

証明書は、**公開キー** と、信頼されたサード パーティ (**証明機関** など) が **署名** (検証) したエンティティ (ドメイン名など) を結び付けるものです。  証明書は、信頼された公開暗号化キーを配布するための便利な方法を提供します。 証明書によって、通信が信頼できるものであること、暗号化された情報を正しいサーバーに送信していることが確認されます。 

## <a name="deploying-certificates-on-device"></a>デバイスへの証明書のデプロイ

Azure Stack Edge デバイスでは、自己署名証明書を使用するか、独自の証明書を取り込むことができます。

- **デバイスによって生成された証明書**: デバイスが最初に構成されたときに、自己署名証明書が自動的に生成されます。 必要に応じて、ローカル Web UI を使用してこれらの証明書を再生成することができます。 証明書が再生成されたら、デバイスへのアクセスに使用するクライアントに証明書をダウンロードしてインポートします。

- **独自の証明書の導入**: 必要に応じて、独自の証明書を導入することができます。 独自の証明書を導入する場合は、従わなければならないガイドラインがあります。

    - 最初に、この記事で、お使いの Azure Stack Edge デバイスで使用できる証明書の種類を理解します。
    - 次に、[証明書の種類ごとの証明書要件](azure-stack-edge-gpu-certificate-requirements.md)を確認します。  
    - その後、[Azure PowerShell で証明書を作成する](azure-stack-edge-gpu-create-certificates-powershell.md)か、[適合性チェッカー ツールで証明書を作成する](azure-stack-edge-gpu-create-certificates-tool.md)ことができます。
    - 最後に、[証明書を適切な形式に変換](azure-stack-edge-gpu-prepare-certificates-device-upload.md)して、デバイスにアップロードできるようにします。
    - デバイスに[証明書をアップロード](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device)します。
    - デバイスにアクセスする[クライアントに証明書をインポート](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)します。

## <a name="types-of-certificates"></a>証明書の種類

デバイスに導入できるさまざまな種類の証明書は、次のとおりです。 
- 署名証明書
    - ルート CA
    - 中級

- ノード証明書

- エンドポイント証明書   
   
    - Azure Resource Manager 証明書
    - BLOB ストレージ証明書

- ローカル UI 証明書
- IoT デバイス証明書
    
- Kubernetes 証明書

    - Edge コンテナー レジストリ証明書
    - Kubernetes ダッシュボード証明書
    
- Wi-Fi 証明書
- VPN 証明書  

- 暗号化証明書
    - サポート セッション証明書

これらの証明書それぞれについて、次のセクションで詳しく説明します。

## <a name="signing-chain-certificates"></a>署名チェーン証明書

証明書に署名する機関または署名証明機関の証明書です。 

### <a name="types"></a>種類

これに該当する証明書は、ルート証明書または中間証明書です。 ルート証明書は常に自己署名されています (つまり、それ自体によって署名されています)。 中間証明書は自己署名ではなく、署名機関によって署名されています。

#### <a name="caveats"></a>注意事項

- ルート証明書は、署名チェーン証明書である必要があります。
- ルート証明書は、次の形式でデバイスにアップロードできます。 
    - **DER** – `.cer` ファイル拡張子として使用できます。
    - **Base-64 エンコード** – `.cer` ファイル拡張子として使用できます。
    - **P7b** – この形式は、ルート証明書と中間証明書を含む署名チェーン証明書に対してのみ使用されます。
- 署名チェーン証明書は常に、他の証明書をアップロードする前にアップロードされます。


## <a name="node-certificates"></a>ノード証明書

<!--Your  device could be a 1-node device or a 4-node device.--> デバイス内のすべてのノードは常に相互に通信しているため、信頼関係が必要です。 ノード証明書は、その信頼を確立する手段を提供します。 https 経由のリモート PowerShell セッションを使用してデバイス ノードに接続するときにも、ノード証明書が関与します。

#### <a name="caveats"></a>注意事項

- ノード証明書は、エクスポート可能な秘密キーを含む `.pfx` 形式で提供する必要があります。 
- 1 つのワイルドカード ノード証明書または 4 つの個別ノード証明書を作成してアップロードできます。 
- DNS ドメインが変更されたが、デバイス名は変わらない場合は、ノード証明書を変更する必要があります。 独自のノード証明書を導入する場合、デバイスのシリアル番号は変更できず、ドメイン名のみ変更できます。
- ノード証明書を作成する場合、次の表を参考にしてください。
   
    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>エンドポイント証明書

デバイスが公開するエンドポイントでは、信頼された通信のために証明書が必要です。 エンドポイント証明書には、REST API を使用して Azure Resource Manager および BLOB ストレージにアクセスするときに必要なものが含まれています。 

独自の署名入り証明書を導入する場合は、証明書の対応する署名チェーンも必要です。 署名チェーン、Azure Resource Manager、デバイスの BLOB 証明書については、デバイスを認証して通信するために、クライアント マシンにも対応する証明書が必要になります。

#### <a name="caveats"></a>注意事項

- エンドポイント証明書は、秘密キーを使用した `.pfx` 形式である必要があります。 署名チェーンは DER 形式 (`.cer` ファイル拡張子) である必要があります。 
- 独自のエンドポイント証明書を導入する場合、個々の証明書またはマルチドメイン証明書にすることができます。 
- 署名チェーンを導入する場合、エンドポイント証明書をアップロードする前に署名チェーン証明書をアップロードする必要があります。
- デバイス名または DNS ドメイン名が変更された場合、これらの証明書を変更する必要があります。
- ワイルドカード エンドポイント証明書を使用できます。
- エンドポイント証明書のプロパティは、一般的な SSL 証明書のプロパティに似ています。 
- エンドポイント証明書を作成するときは、次の表を使用します。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |BLOB ストレージ|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |両方のエンドポイントに対する単一のマルチ SAN 証明書|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>ローカル UI 証明書

ブラウザーを使用して、デバイスのローカル Web UI にアクセスできます。 この通信をセキュリティで確実に保護するために、独自の証明書をアップロードできます。 

#### <a name="caveats"></a>注意事項

- ローカル UI 証明書も、エクスポート可能な秘密キーを含む `.pfx` 形式でアップロードします。
- ローカル UI 証明書をアップロードした後、ブラウザーを再起動してキャッシュをクリアする必要があります。 お使いのブラウザーの具体的な手順を参照してください。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |ローカル UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge デバイス証明書

デバイスも IoT デバイスの一種であり、IoT Edge デバイスが接続されていることにより、コンピューティングが有効になっています。 この IoT Edge デバイスと、それに接続可能なダウンストリーム デバイスの間でセキュリティで保護された通信を行うために、IoT Edge 証明書をアップロードすることもできます。 

このデバイスには、デバイスでコンピューティング シナリオのみ使用する場合に使用できる自己署名証明書が備わっています。 ただし、デバイスがダウンストリーム デバイスに接続されている場合は、独自の証明書を導入する必要があります。

この信頼関係を有効にするために、次の 3 つの IoT Edge 証明書をインストールする必要があります。

- **ルート証明機関または所有者証明機関**
- **デバイス証明機関** 
- **デバイス キー証明書**


#### <a name="caveats"></a>注意事項

- IoT Edge 証明書は `.pem` 形式でアップロードします。 

IoT Edge 証明書の詳細については、[Azure IoT Edge 証明書の詳細](../iot-edge/iot-edge-certs.md#iot-edge-certificates)および [IoT Edge 運用証明書の作成](../iot-edge/how-to-manage-device-certificates.md?preserve-view=true&view=iotedge-2020-11#create-production-certificates)に関するページを参照してください。

## <a name="kubernetes-certificates"></a>Kubernetes 証明書

次の Kubernetes 証明書を、お使いの Azure Stack Edge デバイスで使用できます。

- **Edge コンテナー レジストリ証明書**: デバイスに Edge コンテナー レジストリがある場合は、デバイス上のレジストリにアクセスしているクライアントとのセキュリティ保護された通信のために、Edge Container Registry 証明書が必要です。
- **ダッシュボード エンドポイント証明書**: デバイス上の Kubernetes ダッシュボードにアクセスするには、ダッシュボード エンドポイント証明書が必要です。


#### <a name="caveats"></a>注意事項

- Edge コンテナー レジストリ証明書は次である必要があります。 
    - PEM 形式の証明書である。
    - 型 (`*.<endpoint suffix>` または `ecr.<endpoint suffix>`) のサブジェクト代替名 (SAN) または CName (CN) のいずれかを含む。 例: `*.dbe-1d6phq2.microsoftdatabox.com OR ecr.dbe-1d6phq2.microsoftdatabox.com`


- ダッシュボード証明書は次である必要があります。
    - PEM 形式の証明書である。
    - 型 (`*.<endpoint-suffix>` または `kubernetes-dashboard.<endpoint-suffix>`) のサブジェクト代替名 (SAN) または CName (CN) のいずれかを含む。 たとえば、`*.dbe-1d6phq2.microsoftdatabox.com` や `kubernetes-dashboard.dbe-1d6phq2.microsoftdatabox.com` などです。 


## <a name="vpn-certificates"></a>VPN 証明書

デバイスで VPN (ポイント対サイト) が構成されている場合は、独自の VPN 証明書を導入して、通信が信頼されていることを確認できます。 ルート証明書は Azure VPN Gateway にインストールされ、クライアント証明書はポイント対サイトを使用して仮想ネットワークに接続する各クライアント コンピューターにインストールされます。

#### <a name="caveats"></a>注意事項

- VPN 証明書は、プライベート キーを使用して *.pfx* 形式としてアップロードする必要があります。
- VPN 証明書は、デバイス名、デバイス シリアル番号、またはデバイス構成に依存しません。 外部 FQDN だけが必要です。
- クライアント OID が設定されていることを確認します。

詳細については、「[PowerShell を使用したポイント対サイトの証明書の生成とエクスポート](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell)」を参照してください。

## <a name="wi-fi-certificates"></a>Wi-Fi 証明書

デバイスが WPA2-Enterprise ワイヤレス ネットワーク上で動作するように構成されている場合、ワイヤレス ネットワーク経由で行われるすべての通信に Wi-Fi 証明書も必要になります。 

#### <a name="caveats"></a>注意事項

- Wi-Fi 証明書は、プライベート キーを使用して *.pfx* 形式としてアップロードする必要があります。
- クライアント OID が設定されていることを確認します。

## <a name="support-session-certificates"></a>サポート セッション証明書

デバイスで問題が発生した場合、それらの問題のトラブルシューティングを行うために、デバイスでリモート PowerShell サポート セッションを開くことができます。 セキュリティで保護された暗号化通信をこのサポート セッションで有効にするために、証明書をアップロードできます。

#### <a name="caveats"></a>注意事項

- 暗号化解除ツールを使用して、対応する `.pfx` 証明書および秘密キーがクライアント コンピューターにインストールされていることを確認してください。
- 証明書の **[キー使用法]** フィールドが **[証明書の署名]** ではないことを確認します。 これを確認するには、証明書を右クリックし、 **[開く]** を選択し、 **[詳細]** タブで **[キー使用法]** を見つけます。 

- サポート セッション証明書は、`.cer` 拡張子を持つ DER 形式で用意する必要があります。


## <a name="next-steps"></a>次のステップ

[証明書の要件を確認](azure-stack-edge-gpu-certificate-requirements.md)します。