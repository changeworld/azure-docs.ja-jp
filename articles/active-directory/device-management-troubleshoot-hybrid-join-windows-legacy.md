---
title: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0d21a8848222c4b09723e22d2d51ec43b2154553
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング 

このトピックは、次のデバイスにのみ適用されます。 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 または Windows Server 2016 については、「[Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング](device-management-troubleshoot-hybrid-join-windows-current.md)」を参照してください。

このトピックは、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](device-management-hybrid-azuread-joined-devices-setup.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイス ベースの条件付きアクセス

- [設定のエンタープライズ ローミング](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) 





このトピックでは、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。  

**知っておくべきこと** 

- ユーザーごとのデバイスの最大数は、デバイスを基準に決定されます。 たとえば 1 台のデバイスに *jdoe* と *jharnett* がサインインする場合、それぞれについて別個の登録 (DeviceID) が **[ユーザー]** 情報タブに作成されます。  

- デバイスの初回登録/参加は、ログオンまたはロック/ロック解除のいずれかのタイミングで試行するように構成されています。 タスク スケジューラのタスクによってトリガーされる 5 分間の待ち時間が生じる場合があります。 

- オペレーティング システムの再インストールまたは手動での登録解除と再登録により、Azure AD に新しい登録が作成されることがあります。この場合、Azure Portal の [ユーザー情報] タブに複数のエントリが表示されます。 

## <a name="step-1-retrieve-the-registration-status"></a>手順 1: 登録状態を取得する 

**登録状態を確認するには:**  

1. 管理者としてコマンド プロンプトを開きます。 

2. 「`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`」と入力します。

このコマンドにより、参加状態の詳細を示すダイアログ ボックスが表示されます。

![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>手順 2: ハイブリッド Azure AD 参加状態を評価する 

ハイブリッド Azure AD 参加に失敗した場合、発生した問題の詳細がダイアログ ボックスに表示されます。

**最も一般的な問題:**

- AD FS または Azure AD が正しく構成されていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- ドメイン ユーザーとしてサインオンしていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    これが発生する理由はいくつかあります。
    
    1. サインインしているユーザーがドメイン ユーザーでない場合 (ローカル ユーザーなど)。 ダウンレベルのデバイスでのハイブリッド Azure AD 参加は、ドメイン ユーザーに対してのみサポートされています。
    
    2. 何らかの理由で Autoworkplace.exe が Azure AD または AD FS で自動的に認証できない場合。 原因としては、Azure AD URL への送信ネットワーク接続の問題 (前提条件を確認してください) や、ユーザーに対して MFA が有効化/構成されていてもフェデレーション サーバーで WIAORMUTLIAUTHN が構成されていないこと (構成手順を確認してください) などが考えられます。 また、ホーム領域検出 (HRD) ページがユーザーの操作を待っているため、Autoworkplace.exe では、自動的にトークンを取得できなくなっていることも考えられます。
    
    3. 組織で Azure AD シームレス シングル サインオンを使用している場合、次の URL はデバイスの IE イントラネット設定に表示されません。
    
       - https://autologon.microsoftazuread-sso.com

    
       また、イントラネット ゾーンの [スクリプトを介したステータス バーの更新を許可する] 設定を有効にする必要があります。

- クォータに達している

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- サービスが応答していない 

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

状態に関する情報は、**[Applications and Services Log (アプリケーションおよびサービス ログ)] の [Microsoft-Workplace Join (Microsoft-社内参加)]** のイベント ログで確認することもできます。
  
**ハイブリッド Azure AD 参加に失敗する最も一般的な原因:** 

- コンピューターが、組織の内部ネットワーク上、またはオンプレミスの AD ドメイン コントローラーに接続していない VPN 上に存在しない。

- ローカル コンピューター アカウントでコンピューターにログオンしている。 

- 次のようなサービス構成の問題がある。 

  - フェデレーション サーバーが **WIAORMULTIAUTHN** をサポートするように構成されている。 

  - Azure AD のコンピューターが属する AD フォレスト内の検証済みのドメイン名を参照するサービス接続ポイント オブジェクトがない。

  - ユーザーがデバイスの上限に達している。 

## <a name="next-steps"></a>次の手順

ご不明な点がある場合は、[デバイス管理の FAQ](device-management-faq.md) をご覧ください。  
