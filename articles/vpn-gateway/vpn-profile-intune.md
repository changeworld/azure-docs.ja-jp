---
title: Azure VPN クライアントの Intune プロファイルを作成する
titleSuffix: Azure VPN Gateway
description: Intune のカスタム プロファイルを作成して Azure VPN クライアント プロファイルを展開する方法について説明します
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/15/2020
ms.author: cherylmc
ms.openlocfilehash: 7105597ec34e804c2f2b85b01feb4824d63005c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578123"
---
# <a name="create-an-intune-profile-to-deploy-vpn-client-profiles"></a>Intune プロファイルを作成して VPN クライアント プロファイルを展開する

Microsoft Intune を使用して、Azure VPN クライアント (Windows 10) のプロファイルを展開できます。 この記事では、カスタム設定を使用して Intune プロファイルを作成する方法について説明します。

> [!NOTE]
> この方法は、クライアント認証に Azure Active Directory または共通の証明書を使用するプロファイルを展開する場合にのみ機能します。 固有のクライアント証明書を使用する場合、各ユーザーは Azure VPN クライアント内で正しい証明書を手動で選択する必要があります。
>

## <a name="prerequisites"></a>前提条件

* デバイスは既に Intune MDM に登録されている。
* Windows 10 用 Azure VPN クライアントがクライアント マシンに既に展開されている。
* サポート対象は Windows バージョン 19H2 以降のみ。

## <a name="modify-xml"></a><a name="xml"></a>XML を変更する

以下の手順では、次の設定が指定された Intune 用カスタム OMA-URI プロファイルのサンプル XML を使用します。

* 自動接続オン
* 信頼されたネットワークの検出が有効。

サポートされているその他のオプションについては、[VPNv2 CSP](https://docs.microsoft.com/windows/client-management/mdm/vpnv2-csp) の記事を参照してください。

1. Azure portal から VPN プロファイルをダウンロードし、パッケージから *azurevpnconfig.xml* ファイルを抽出します。
1. 次のテキストをコピーし、新しいテキスト エディター ファイルに貼り付けます。

   ```xml-interactive
    <VPNProfile>
      <!--<EdpModeId>corp.contoso.com</EdpModeId>-->
      <RememberCredentials>true</RememberCredentials>
      <AlwaysOn>true</AlwaysOn>
      <TrustedNetworkDetection>contoso.com,test.corp.contoso.com</TrustedNetworkDetection>
      <DeviceTunnel>false</DeviceTunnel>
      <RegisterDNS>false</RegisterDNS>
      <PluginProfile>
        <ServerUrlList>azuregateway-7cee0077-d553-4323-87df-069c331f58cb-053dd0f6af02.vpn.azure.com</ServerUrlList> 
        <CustomConfiguration>

        </CustomConfiguration>
        <PluginPackageFamilyName>Microsoft.AzureVpn_8wekyb3d8bbwe</PluginPackageFamilyName>
      </PluginProfile>
    </VPNProfile>
   ```
1. ダウンロードしたプロファイル (azurevpnconfig.xml) のエントリを使用して、```<ServerUrlList>``` から ```</ServerUrlList>``` の間のエントリを変更します。 環境に合わせて "TrustedNetworkDetection" FQDN を変更します。
1. Azure でダウンロードしたプロファイル (azurevpnconfig.xml) を開き、テキストを強調表示し、Ctrl + C キーを押して内容全体をクリップボードにコピーします。 
1. 前の手順でコピーしたテキストを、手順 2 で作成したファイルの ```<CustomConfiguration>  </CustomConfiguration>``` タグの間に貼り付けます。 xml という拡張子でファイルを保存します。
1. ```<name>  </name>``` タグ内の値を書き留めます。 これがプロファイルの名前です。 Intune でプロファイルを作成するときに、この名前が必要になります。 ファイルを閉じ、その保存場所を覚えておきます。

## <a name="create-intune-profile"></a>Intune プロファイルを作成する

このセクションでは、カスタム設定を使用して Microsoft Intune プロファイルを作成します。

1. Intune にサインインし、 **[デバイス] -> [構成プロファイル]** の順に移動します。 **[+ プロファイルの作成]** を選択します。

   :::image type="content" source="./media/create-profile-intune/configuration-profile.png" alt-text="構成プロファイル":::
1. **[プラットフォーム]** には、 **[Windows 10 以降]** を選択します。 **[プロファイル]** には **[カスタム]** を選択します。 そのうえで **[Create]\(作成\)** を選択します。
1. プロファイルの名前と説明を入力し、 **[次へ]** を選択します。
1. **[構成設定]** タブで **[追加]** を選択します。

    * **[名前]:** 構成の名前を入力します。
    * **説明:** 省略可能な説明です。
    * **OMA-URI:** ```./User/Vendor/MSFT/VPNv2/<name of your connection>/ProfileXML``` (この情報は、azurevpnconfig.xml の <name> </name> タグ内にあります)。
    * **データ型**: 文字列 (XML ファイル)。

   フォルダー アイコンを選択し、[XML](#xml) 手順の手順 6 で保存したファイルを選択します。 **[追加]** を選択します。

   :::image type="content" source="./media/create-profile-intune/configuration-settings.png" alt-text="構成プロファイル" lightbox="./media/create-profile-intune/configuration-settings.png":::
1. **[次へ]** を選択します。
1. **[割り当て]** で、構成をプッシュするグループを選択します。 次に、 **[次へ]** を選択します。
1. [適用性ルール] は省略可能です。 必要に応じてルールを定義し、 **[次へ]** を選択します。
1. **[確認および作成]** ページで、 **[作成]** を選択します。

    :::image type="content" source="./media/create-profile-intune/create-profile.png" alt-text="構成プロファイル":::
1. これでカスタム プロファイルが作成されました。 このプロファイルを展開する Microsoft Intune 手順については、[ユーザーとデバイスのプロファイルの割り当て](https://docs.microsoft.com/mem/intune/configuration/device-profile-assign)に関するページを参照してください。
 
## <a name="next-steps"></a>次のステップ

ポイント対サイトの詳細については、[ポイント対サイト](point-to-site-about.md)に関する記事をご覧ください。
