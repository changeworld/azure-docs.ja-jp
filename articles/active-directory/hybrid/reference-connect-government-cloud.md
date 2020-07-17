---
title: Azure AD Connect:Azure Government のハイブリッド ID に関する考慮事項
description: 政府のクラウドに Azure AD Connect をデプロイする場合の特別な考慮事項。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81377556"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Azure Government のハイブリッド ID に関する考慮事項 
次のドキュメントでは、Azure Government クラウドを使用してハイブリッド環境を実装する際の考慮事項について説明します。  この情報は、Azure Government クラウドを操作している管理者と設計者のための参考資料として提供されています。  
> [!NOTE] 
> オンプレミスの AD 環境を Azure Governemnt クラウドと統合するには、[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) の最新リリースにアップグレードする必要があります。 

> [!NOTE] 
> U.S. Government DoD エンドポイントの詳細な一覧については、[ドキュメント](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)を参照してください。 

## <a name="pass-through-authentication"></a>パススルー認証 
パススルー認証 (PTA) と Azure Government クラウドの実装については、次の情報が提供されています。

### <a name="allow-access-to-urls"></a>URL へのアクセスを許可する  
パススルー認証エージェントをデプロイする前に、お使いのサーバーと Azure AD の間にファイアウォールがあるかどうかを確認します。 ファイアウォールまたはプロキシによって DNS ホワイトリスト登録が許可されている場合は、次の接続を追加します。 
> [!NOTE]
> 次のガイダンスは、Azure Government 環境用に[アプリケーション プロキシ コネクタ](https://aka.ms/whyappproxy)をインストールする場合にも適用されます。

|URL |用途|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|エージェントと Azure AD クラウド サービス間の通信 |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| エージェントでは、これらの URL を使用して、証明書が検証されます。| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br>* .microsoftonline-p.us </br>*.msauth.net </br>* .msauthimages.net </br>*.msecnd.net</br>* .msftauth.net </br>*.msftauthimages.net</br>* .phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| エージェントでは、登録プロセスの間にこれらの URL が使用されます。| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure Government クラウド用にエージェントをインストールする 
Azure Government クラウド用にエージェントをインストールするには、次の特定の手順に従う必要があります。コマンド ライン ターミナルで、エージェントをインストールする実行可能ファイルが配置されているフォルダーに移動します。 Azure Government 用のインストールを指定する次のコマンドを実行します。 

パススルー認証の場合: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

アプリケーション プロキシの場合:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>シングル サインオン 
Azure AD Connect サーバーをセットアップする:サインイン方法としてパススルー認証を使用する場合、他に確認すべき前提条件はありません。 サインイン方法としてパスワード ハッシュ同期を使用する場合や、Azure AD Connect と Azure AD の間にファイアウォールがある場合は、次の点を確認してください。
- Azure AD Connect バージョン 1.1.644.0 以降を使用している。 
- ファイアウォールまたはプロキシによって DNS ホワイトリスト登録が許可されている場合は、*.msapproxy.us の URL に対するポート 443 での接続を追加します。 そうでない場合は、毎週更新される Azure データセンターの IP 範囲へのアクセスを許可します。 この前提条件は、その機能を有効にした場合にのみ適用されます。 実際のユーザー サインインに必要な条件ではありません。 

### <a name="rolling-out-seamless-sso"></a>シームレス SSO をロールアウトする 
以下の指示に従い、シームレス SSO をユーザーに徐々にロールアウトできます。 まず、Active Directory でグループ ポリシーを利用し、すべてのユーザー、または選択したユーザーのイントラネット ゾーン設定に Azure AD の URL https://autologon.microsoft.us を追加します。 

また、グループ ポリシーを使用して、[スクリプトを介したステータス バーの更新を許可する] というイントラネットのゾーン ポリシー設定を有効にする必要があります。 ブラウザーに関する考慮事項 Mozilla Firefox (すべてのプラットフォーム) Mozilla Firefox では、Kerberos 認証は自動的に使用されません。 各ユーザーが、次の手順に従って、Firefox の設定に Azure AD の URL を手動で追加する必要があります。 
1. Firefox を実行して、アドレス バーに「about:config」と入力します。 表示されているすべての通知を無視します。 
2. network.negotiate-auth.trusted-uris の設定を検索します。 この設定は、Kerberos 認証用の Firefox の信頼済みサイトを一覧表示します。 
3. 右クリックして [変更] を選択します。 
4. フィールドに「 https://autologon.microsoft.us  」と入力します。
5. [OK] を選択してから、ブラウザーをもう一度開きます。 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium に基づく Microsoft Edge (すべてのプラットフォーム) 
お使いの環境で  `AuthNegotiateDelegateAllowlist`  または  `AuthServerAllowlist`  ポリシー設定をオーバーライドした場合、それらの設定に Azure AD の URL (https://autologon.microsoft.us) ) も確実に追加してください。 

### <a name="google-chrome-all-platforms"></a>Google Chrome (すべてのプラットフォーム) 
お使いの環境で  `AuthNegotiateDelegateWhitelist`  または  `AuthServerWhitelist`  ポリシー設定をオーバーライドした場合は、それらの設定に Azure AD の URL (https://autologon.microsoft.us) ) も確実に追加してください。 

## <a name="next-steps"></a>次のステップ
[パススルー認証](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[シングル サインオン](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
