---
title: "P2S RADIUS 接続用の VPN クライアント構成ファイルを作成およびインストールする: PowerShell: Azure | Microsoft Docs"
description: "RADIUS 認証を使用する接続用の Windows、Mac OS X、および Linux の VPN クライアント構成ファイルを作成します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: ce914d2fd0472855ad7a17bf64ae43a76ceb5743
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>P2S RADIUS 認証用の VPN クライアント構成ファイルを作成およびインストールする

ポイント対サイトの仮想ネットワークを接続するには、接続するクライアント デバイスを構成する必要があります。 Windows、Mac OSX、Linux のクライアント デバイスから P2S VPN 接続を作成できます。 RADIUS 認証を使用する場合、複数の認証オプション (ユーザー名/パスワードの認証、証明書の認証、およびその他の認証の種類) があります。 VPN クライアント構成は、認証の種類ごとに異なります。 VPN クライアントを構成するには、必要な設定を含むクライアント構成ファイルを使用します。 この記事は、使用する RADIUS 認証の種類用の VPN クライアント構成を作成してインストールするのに役立ちます。

P2S RADIUS 認証の構成ワークフローは次のとおりです。

1. [P2S 接続用に Azure VPN ゲートウェイを設定する](point-to-site-how-to-radius-ps.md)。
2. [RADIUS サーバーを認証用に設定する](point-to-site-how-to-radius-ps.md#radius)。 
3. **選択した認証オプションの VPN クライアント構成を取得し、それを使用して VPN クライアントを設定する**。 (この記事)
4. [P2S 構成を完了し、接続する](point-to-site-how-to-radius-ps.md)。

>[!IMPORTANT]
>VPN プロトコルの種類や認証の種類など、VPN クライアント構成プロファイルの生成後にポイント対サイト VPN 構成に対する変更があった場合は、新しい VPN クライアント構成を生成してユーザー デバイスにインストールする必要があります。
>
>

この記事のセクションを使用するには、最初に使用する認証の種類 (ユーザー名/パスワード、証明書、またはその他の認証の種類) を決定します。 各セクション内に、Windows、Mac OS X、Linux の手順があります (この時点で利用できる手順に限られます)。

## <a name="adeap"></a>ユーザー名/パスワード認証

ユーザー名/パスワード認証の構成には 2 つの方法があります。 AD を使用する認証、または AD を使用しない認証のいずれかを構成できます。 いずれかのシナリオで、接続するすべてのユーザーに、RADIUS を使用して認証できるユーザー名とパスワードの資格情報があることを確認します。

* ユーザー名/パスワード認証を構成する場合、EAP-MSCHAPv2 のユーザー名/パスワード認証プロトコルの構成のみを作成できます。
* '-AuthenticationMethod' は 'EapMSChapv2' です。

### <a name="usernamefiles"></a> 1.VPN クライアント構成ファイルの生成

ユーザー名/パスワード認証で使用する VPN クライアント構成ファイルを生成します。 VPN クライアント構成ファイルの生成には、次のコマンドを使用します。

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
コマンドを実行すると、リンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、"VpnClientConfiguration.zip" をダウンロードします。 そのファイルを解凍して、次のフォルダーを表示します。 
 
* **WindowsAmd64** および **WindowsX86**。これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* **Generic**。このフォルダーには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 このフォルダーは、ユーザー名/パスワード認証の構成には不要です。
* **Mac**。仮想ネットワーク ゲートウェイの作成時に IKEv2 を構成した場合は、**mobileconfig** というファイルを含む "Mac" という名前のフォルダーが表示されます。 このファイルは、Mac クライアントの構成に使用されます。

クライアント構成ファイルを既に作成してある場合は、"Get-AzureRmVpnClientConfiguration" コマンドレットを使用して取得できます。 ただし、VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加えた場合、構成は自動的に更新されません。 "New-AzureRmVpnClientConfiguration" コマンドレットを実行して新しい構成ダウンロードを作成する必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2.VPN クライアントの構成

次の VPN クライアントを構成できます。

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [strongSwan を使用する Linux](#adlinuxcli)
 
#### <a name="adwincli"></a>Windows VPN クライアント設定

バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、[FAQ](vpn-gateway-vpn-faq.md#P2S) に関するページのポイント対サイトに関するセクションを参照してください。

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
2. パッケージをインストールするには、ダブルクリックします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。 

#### <a name="admaccli"></a>Mac (OS X) VPN クライアント設定

1. **VpnClientSetup mobileconfig** ファイルを選択し、各ユーザーに送信します。 電子メールや別の方法を使用できます。

2. Mac で **mobileconfig** ファイルを探します。

  ![mobilconfig ファイルの検索](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. プロファイルをダブルクリックしてインストールし、**[続ける]** をクリックします。 プロファイル名は VNet の名前と同じです。

  ![インストール](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. **[続ける]** をクリックしてプロファイルの送信者を信頼し、インストールを続行します。

  ![続ける](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. プロファイルのインストール中に、VPN 認証に使用されるユーザー名とパスワードを指定するオプションが表示されます。 この情報の入力は必須ではありません。 指定した場合は、その情報が保存され、接続の開始時に自動的に使用されます。 **[インストール]** をクリックして続行します。

  ![設定](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. コンピューターにプロファイルをインストールするために必要な特権のユーザー名とパスワードを入力します。 Click **OK**.

  ![ユーザー名とパスワード](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. インストールが完了すると、プロファイルは **[プロファイル]** ダイアログ ボックスに表示されます。 このダイアログは、後で **[システム環境設定]** から開くこともできます。

  ![システム環境設定](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. VPN 接続にアクセスするには、**[システム環境設定]** から **[ネットワーク]** ダイアログを開きます。

  ![ネットワーク](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 接続は、"**IkeV2-VPN**" として表示されます。 この名前は、**mobileconfig** ファイルを更新することで変更できます。

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. **[認証設定]**をクリックします。 ドロップダウンで **[ユーザー名]** を選択し、資格情報を入力します。 以前に資格情報を入力した場合は、ドロップダウンで **[ユーザー名]** が自動的に選択され、ユーザー名とパスワードがあらかじめ入力されています。 **[OK]** をクリックして設定を保存します。 [ネットワーク] ダイアログ ボックスに戻ります。

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. **[適用]** をクリックして変更を保存します。 接続を開始するには、**[接続]** をクリックします。

#### <a name="adlinuxcli"></a>strongSwan を使用した Linux VPN クライアント設定

次の手順は、Ubuntu 17.0.4 上で strongSwan 5.5.1 を使用して作成されました。 実際の画面は、使用している Linux と strongSwan のバージョンによって異なる場合があります。

1. **[端末]** を起動し、例のコマンドを実行して **strongSwan** とその Network Manager をインストールします。 "libcharon-extra-plugins" に関連するエラーが表示された場合は、"strongswan-plugin-eap-mschapv2" に置き換えます。

  ```Terminal
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. **[Network Manager]** アイコン (上矢印/下矢印) をクリックして、**[接続の編集]** を選択します。

  ![接続の編集](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. 新しい接続を作成するには、**[追加]** ボタンをクリックします。

  ![接続の追加](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. ドロップダウン メニューから **[IPsec/IKEv2 (strongswan)]** を選択して、**[作成]** をクリックします。 この手順で使用する接続の名前を変更できます。

  ![IKEv2 の追加](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. ダウンロード クライアント構成ファイルの **Generic** フォルダーから **VpnSettings.xml** ファイルを開きます。 **VpnServer** というタグを検索して、"azuregateway" で始まり ".cloudapp.net" で終わる名前をコピーします。

  ![VPN の設定](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. この名前を **[ゲートウェイ]** セクションの下の新しい VPN 接続の **[アドレス]** フィールドに貼り付けます。 次に、**[証明書]** フィールドの最後のフォルダー アイコンをクリックして、Generic フォルダーを見つけ、そこにある **VpnServerRoot** ファイルを選択します。
7. 接続の **[クライアント]** セクションで、**[認証]** の **[EAP]** を選び、ユーザー名とパスワードを入力します。 この情報を保存するには、右側の [ロック] アイコンを選択する必要がある場合があります。 その後、**[保存]** をクリックします。

  ![接続設定の編集](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. **[Network Manager]** アイコン (上矢印/下矢印) をクリックして、**[VPN 接続]** にポインターを合わせます。 作成した VPN 接続が表示されます。 接続を開始するには、接続を選択して接続します。

  ![RADIUS の接続](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>証明書認証
 
EAP-TLS プロトコルを使用する RADIUS 証明書認証用の VPN クライアント構成ファイルを作成できます。 通常、企業が発行した証明書を使用して、VPN のユーザーを認証します。 接続するすべてのユーザーでユーザーのデバイスに証明書がインストールされていることと、その証明書が RADIUS サーバーで検証できることを確認します。
 
* "-AuthenticationMethod" は "EapTls" です。
* 証明書認証時に、クライアントはその証明書を検証することで RADIUS サーバーを検証します。 -RadiusRootCert は、RADIUS サーバーの検証に使用されるルート証明書を含む .cer ファイルです。
* 各 VPN クライアント デバイスには、インストールされたクライアント証明書が必要です。
* 場合によっては、Windows デバイスに複数のクライアント証明書が存在することがあります。 そのため、認証中に、すべての証明書の一覧を示すポップアップ ダイアログが表示される場合があります。 その後、ユーザーは、使用する証明書を選択する必要があります。 適切な証明書は、クライアント証明書からチェーンする必要のあるルート証明書を指定することで除外できます。 "-ClientRootCert" は、ルート証明書を含む .cer ファイルです。これは、省略可能なパラメーターです。 接続元のデバイスにクライアント証明書が 1 つしかない場合、このパラメーターを指定する必要はありません。

### <a name="certfiles"></a>1.VPN クライアント構成ファイルの生成

証明書認証で使用する VPN クライアント構成ファイルを生成します。 VPN クライアント構成ファイルの生成には、次のコマンドを使用します。
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

コマンドを実行すると、リンクが返されます。 このリンクをコピーして Web ブラウザーに貼り付け、"VpnClientConfiguration.zip" をダウンロードします。 そのファイルを解凍して、次のフォルダーを表示します。

* **WindowsAmd64** および **WindowsX86**。これらのフォルダーにはそれぞれ、Windows の 64 ビットと 32 ビットのインストーラー パッケージが含まれています。 
* **GenericDevice**。このフォルダーには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。

クライアント構成ファイルを既に作成してある場合は、"Get-AzureRmVpnClientConfiguration" コマンドレットを使用して取得できます。 ただし、VPN プロトコルの種類や認証の種類など、P2S VPN 構成に変更を加えた場合、構成は自動的に更新されません。 "New-AzureRmVpnClientConfiguration" コマンドレットを実行して新しい構成ダウンロードを作成する必要があります。

以前に生成されたクライアント構成ファイルを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2.VPN クライアントの構成

次の VPN クライアントを構成できます。

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (サポート対象、まだ記事の手順はありません)

#### <a name="certwincli"></a>Windows VPN クライアント設定

1. 構成パッケージを選択し、クライアント デバイスにインストールします。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。
2. 各クライアントには、認証のためにクライアント証明書が必要です。 クライアント証明書をインストールします。 クライアント証明書については、[ポイント対サイトのクライアント証明書](vpn-gateway-certificates-point-to-site.md)に関するページを参照してください。 生成された証明書をインストールするには、「[Install a certificate on Windows clients](point-to-site-how-to-vpn-client-install-azure-cert.md)」 (Windows クライアントに証明書をインストールする) を参照してください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。

#### <a name="certmaccli"></a>Mac (OS X) VPN クライアント設定

Azure VNet に接続する Mac デバイスごとに、個別のプロファイルを作成する必要があります。 これは、これらのデバイスでは、認証用のユーザー証明書をプロファイルで指定する必要があるためです。 **Generic** フォルダーには、プロファイルの作成に必要な情報がすべて揃っています。

  * **VpnSettings.xml** には、サーバー アドレスやトンネルの種類など、重要な設定が含まれています。
  * **VpnServerRoot.cer** には、P2S 接続の設定中に VPN ゲートウェイを検証するために必要なルート証明書が含まれています。
  * **RadiusServerRoot.cer** には、認証時に RADIUS サーバーの検証に必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。

1. **VpnServerRoot** および **RadiusServerRoot** ルート証明書を Mac にインポートします。 これを行うには、ファイルを Mac にコピーしてダブルクリックします。  
**[追加]** をクリックしてインポートします。

  *VpnServerRoot の追加*

  ![証明書の追加](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  *RadiusServerRoot の追加*

  ![証明書の追加](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 各クライアントには、認証のためにクライアント証明書が必要です。 クライアント デバイスにクライアント証明書をインストールします。
3. **[ネットワーク]** ダイアログを開き、**[ネットワーク環境設定]** で **[+]** をクリックして、Azure VNet への P2S 接続用に新しい VPN クライアント接続プロファイルを作成します。

  **[インターフェイス]** の値は "VPN"、**[VPN タイプ]** の値は "IKEv2" です。 **[サービス名]** フィールドにプロファイルの名前を指定し、**[作成]** をクリックして VPN クライアント接続プロファイルを作成します。

  ![ネットワーク](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値をプロファイルの **[サーバー アドレス]** フィールドと **[リモート ID]** フィールドに貼り付けます。 **[ローカル ID]** フィールドを空白のままにします。

  ![サーバー情報](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. **[認証設定]** をクリックし、**[証明書]** を選択します。 

  ![認証設定](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. **[選択]** をクリックして、 認証に使用する証明書を選択します。

  ![証明書](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、**[続ける]** をクリックします。

  ![ID](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. **[ローカル ID]** フィールドに、(手順 6 の) 証明書の名前を指定します。 この例では、"ikev2Client.com" です。 次に、**[適用]** ボタンをクリックして変更を保存します。

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. **[ネットワーク]** ダイアログで、**[適用]** をクリックしてすべての変更を保存します。 次に、**[接続]** をクリックして、Azure VNet への P2S 接続を開始します。

## <a name="otherauth"></a>他の認証の種類またはプロトコルを使用する

ユーザー名/パスワードまたは証明書ではなく別の認証の種類 (OTP など) を使用するか、別の認証プロトコル (EAP-MSCHAPv2 の代わりに PEAP-MSCHAPv2 など) を使用するには、独自の VPN クライアント構成プロファイルを作成する必要があります。 プロファイルを作成するには、仮想ネットワーク ゲートウェイの IP アドレス、トンネルの種類、分割トンネルのルートなどの情報が必要です。 この情報は、次の手順を使用して取得できます。

1. "Get-AzureRmVpnClientConfiguration" コマンドレットを使用して、EapMSChapv2 用の VPN クライアント構成を生成します。 手順については、この記事の[こちらのセクション](#ccradius)を参照してください。

2. VpnClientConfiguration.zip ファイルを解凍し、GenenericDevice フォルダーを探します。 64 ビットおよび 32 ビットのアーキテクチャ用の Windows インストーラーが含まれているフォルダーは無視してください。
 
3. GenenericDevice フォルダーには、VpnSettings という XML ファイルが含まれています。 このファイルには、必要な情報がすべて含まれています。

  * VpnServer - Azure VPN ゲートウェイの FQDN。 これは、クライアントの接続先となるアドレスです。
  * VpnType - 接続に使用するトンネルの種類。
  * Routes - Azure VNet のバインドされたトラフィックのみが P2S トンネル経由で送信されるように、プロファイル内で構成する必要があるルート。
  * GenenericDevice フォルダーには、"VpnServerRoot" という .cer ファイルも含まれています。 このファイルには、P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。 Azure VNet に接続するすべてのデバイスに証明書をインストールします。

## <a name="next-steps"></a>次の手順

[P2S 構成を完了する](point-to-site-how-to-radius-ps.md)ための記事に戻ります。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。