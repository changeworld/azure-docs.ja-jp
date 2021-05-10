---
title: Azure Stack Edge Mini R デバイスで Wi-Fi プロファイルを使用する
description: 高セキュリティのエンタープライズ ネットワークとパーソナル ネットワークで Azure Stack Edge Mini R デバイスの Wi-Fi プロファイルを作成する方法について説明します。
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050331"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Azure Stack Edge Mini R デバイスで Wi-Fi プロファイルを使用する

この記事では、Azure Stack Edge Mini R デバイスでワイヤレス ネットワーク (Wi-Fi) プロファイルを使用する方法について説明します。

Wi-Fi プロファイルを準備する方法は、ワイヤレス ネットワークの種類によって異なります。

- Wi-Fi Protected Access 2 (WPA2) - ホームネットワークや Wi-Fi ホットスポットなどのパーソナル ネットワークでは、他のデバイスと同じパスワードを使用して既存のワイヤレス プロファイルをダウンロードして使用することができます。

- 高セキュリティのエンタープライズ環境では、WPA2 - エンタープライズ ネットワーク経由でデバイスにアクセスします。 この種類のネットワークでは、各クライアント コンピューターに個別の Wi-Fi プロファイルがあり、証明書によって認証されます。 必要な構成を判断するには、ネットワーク管理者と協力する必要があります。

両方の種類のネットワークのプロファイル要件については、後で詳しく説明します。

どちらの場合でも、デバイスでプロファイルをテストまたは使用する前に、プロファイルが組織のセキュリティ要件を満たしていることを確認することが非常に重要です。

## <a name="about-wi-fi-profiles"></a>Wi-Fi プロファイルについて

Wi-Fi プロファイルには、Azure Stack Edge Mini R デバイスをワイヤレス ネットワークに接続するために必要な SSID (サービス セット識別子、 **ネットワーク名**)、パスワード キー、およびセキュリティ情報が含まれています。

次のコード例は、一般的なワイヤレス ネットワークで使用するプロファイルの基本設定を示しています。

* `SSID` はネットワーク名です。

* `name` は Wi-Fi 接続のユーザー フレンドリ名です。 これは、ユーザーが自分のデバイスで利用可能な接続の一覧を参照しているときに表示される名前です。

* このプロファイルでは、コンピューターがネットワークの範囲内にあるときにワイヤレス ネットワークに自動的に接続するように構成されています (`connectionMode` = `auto`)。

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Wi-Fi プロファイル設定の詳細については、「[Windows 10 以降のデバイス向けの Wi-Fi 設定の追加](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)」の「**エンタープライズ プロファイル**」を参照してください。また、「[Cisco Wi-Fi プロファイルを構成する](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile)」を参照してください。

Azure Stack Edge Mini R デバイスでワイヤレス接続を有効にするには、デバイスで Wi-Fi ポートを構成し、Wi-Fi プロファイルをデバイスに追加します。 エンタープライズ ネットワークでは、デバイスに証明書をアップロードすることもできます。 その後、デバイスのローカル Web UI からワイヤレス ネットワークに接続できます。 詳細については、「[Azure Stack Edge Mini R でのワイヤレス接続を管理する](./azure-stack-edge-mini-r-manage-wifi.md)」を参照してください。

## <a name="profile-for-wpa2---personal-network"></a>WPA2 - パーソナル ネットワークのプロファイル

Wi-Fi Protected Access 2 (WPA2) - ホームネットワークや Wi-Fi のホットスポットなどのパーソナル ネットワークでは、複数のデバイスが同じプロファイルと同じパスワードを使用する場合があります。 ホーム ネットワークでは、携帯電話とノート パソコンは同じワイヤレス プロファイルとパスワードを使用してネットワークに接続します。

たとえば、Windows 10 クライアントはランタイム プロファイルを生成できます。 ワイヤレス ネットワークにサインインすると、Wi-Fi パスワードを入力するように求められます。パスワードを入力すると、接続されます。 この環境では証明書は必要ありません。

この種類のネットワークでは、ノート パソコンから Wi-Fi プロファイルをエクスポートし、Azure Stack Edge Mini R デバイスに追加することができます。 手順については、以下の「[Wi-Fi プロファイルのエクスポート](#export-a-wi-fi-profile)」を参照してください。

> [!IMPORTANT]
> Azure Stack Edge Mini R デバイス用の Wi-Fi プロファイルを作成する前に、ネットワーク管理者に連絡して、ワイヤレス ネットワークの組織のセキュリティ要件を確認してください。 ワイヤレス ネットワークが要件を満たしていることを確認するまで、デバイスで Wi-Fi プロファイルをテストしたり使用したりしないでください。

## <a name="profiles-for-wpa2---enterprise-network"></a>WPA2 - エンタープライズ ネットワークのプロファイル

Wireless Protected Access 2 (WPA2) -エンタープライズ ネットワークでは、ネットワーク管理者と協力して、Azure Stack Edge Mini R デバイスをネットワークに接続するために必要な Wi-Fi プロファイルと証明書を取得する必要があります。

高度にセキュリティ保護されたネットワークの場合、Azure デバイスでは、Extensible Authentication Protocol-Transport Layer Security (EAP-TLS) を Protected Extensible Authentication Protocol (PEAP) とともに使用できます。 EAP-TLS を使用する PEAP-TLS ではコンピューター認証が使用されます。クライアントとサーバーは、証明書を使用して、相互に ID を検証します。

> [!NOTE]
> * PEAP Microsoft チャレンジ ハンドシェイク認証プロトコル バージョン 2 (PEAP MSCHAPv2) を使用したユーザー認証は、Azure Stack Edge Mini R デバイスではサポートされていません。
> * Azure Stack Edge Mini R 機能にアクセスするには、EAP-TLS 認証が必要です。 Active Directory を使用して設定したワイヤレス接続は機能しません。

ネットワーク管理者は、一意の Wi-Fi プロファイルと、各コンピューターのクライアント証明書を生成します。 ネットワーク管理者は、デバイスごとに個別の証明書を使用するか、共有証明書を使用するかを決定します。

社内で複数の物理的な場所で作業している場合、ネットワーク管理者は、ワイヤレス接続用に複数のサイト固有の Wi-Fi プロファイルと証明書を提供しなければならない場合があります。

エンタープライズ ネットワークでは、ネットワーク管理者が提供する Wi-Fi プロファイルの設定を変更しないことをお勧めします。 調整することができるのは、自動接続の設定のみです。 詳細については、「Windows 10 およびそれ以降のデバイスの Wi-Fi 設定」の「[基本プロファイル](/mem/intune/configuration/wi-fi-settings-windows#basic-profile)」を参照してください。

高セキュリティのエンタープライズ環境では、既存のワイヤレス ネットワーク プロファイルをテンプレートとして使用できます。

* 社内のワイヤレス ネットワーク プロファイルは、職場のコンピューターからダウンロードできます。 手順については、以下の「[Wi-Fi プロファイルのエクスポート](#export-a-wi-fi-profile)」を参照してください。

* 組織内の他のユーザーがワイヤレス ネットワーク経由で Azure Stack Edge Mini R デバイスに接続している場合は、そのデバイスから Wi-Fi プロファイルをダウンロードできます。 手順については、「[Wi-Fi プロファイルをダウンロードする](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile)」を参照してください。

## <a name="export-a-wi-fi-profile"></a>Wi-Fi プロファイルのエクスポート

コンピューター上の Wi-Fi インターフェイスのプロファイルをエクスポートするには、次の手順を実行します。

1. コンピューターのワイヤレス プロファイルを表示するには、 **[スタート]** メニューで **[コマンド プロンプト]** (cmd.exe) を開き、次のコマンドを入力します。

   `netsh wlan show profiles`

   出力は次のようになります。

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. プロファイルをエクスポートするには、次のコマンドを入力します。

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   たとえば、次のコマンドは、ContosoFTINET プロファイルを XML 形式で、`gusp` という名前のユーザーのダウンロード フォルダーに保存します。

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>デバイスへの証明書、 Wi-Fi プロファイルの追加

必要な Wi-Fi プロファイルと証明書を持っている場合は、次の手順を実行して、ワイヤレス接続用に Azure Stack Edge Mini R デバイスを構成します。

1. WPA2 - エンタープライズ ネットワークの場合は、 「[証明書のアップロード](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates)」にあるガイダンスに従って、必要な証明書をデバイスにアップロードします。

1. Wi-Fi プロファイルを Mini R デバイスにアップロードし、「[Wi-Fi プロファイルの追加と接続](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile)」にあるガイダンスに従って接続します。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Mini R のネットワークを構成する](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)方法について説明します。
- [Azure Stack Edge Mini R で Wi-Fi を管理する](azure-stack-edge-mini-r-manage-wifi.md)方法について説明します。
