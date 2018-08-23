---
title: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: aad2b4c3edcdc488257940062e8861613ece25e8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142654"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング 

この記事は、次のデバイスにのみ適用されます。 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 または Windows Server 2016 については、「[Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)」を参照してください。

この記事は、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](hybrid-azuread-join-plan.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイス ベースの条件付きアクセス

- [設定のエンタープライズ ローミング](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 





この記事では、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。  

**知っておくべきこと** 

- ユーザーごとのデバイスの最大数は、デバイスを基準に決定されます。 たとえば 1 台のデバイスに *jdoe* と *jharnett* がサインインする場合、それぞれについて別個の登録 (DeviceID) が **[ユーザー]** 情報タブに作成されます。  

- デバイスの初回登録/参加は、サインインまたはロック/ロック解除のいずれかのタイミングで試行するように構成されています。 タスク スケジューラのタスクによってトリガーされる 5 分間の待ち時間が生じる場合があります。 

- オペレーティング システムの再インストールまたは手動の再登録なので、ユーザー情報タブでデバイスの複数のエントリを取得できます。 

- Windows 7 SP1 または Windows Server 2008 R2 SP1 の場合、[KB4284842](https://support.microsoft.com/help/4284842) がインストールされていることを確認してください。 この更新によって、今後、パスワードの変更後、保護されているキーにお客様がアクセスできなくなったことによる認証エラーを防止できます。

## <a name="step-1-retrieve-the-registration-status"></a>手順 1: 登録状態を取得する 

**登録状態を確認するには:**  

1. ハイブリッド Azure AD 参加を実行したユーザー アカウントでサインオンします。

2. 管理者としてコマンド プロンプトを開きます。 

3. 「`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`」と入力します。

このコマンドにより、参加状態の詳細を示すダイアログ ボックスが表示されます。

![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>手順 2: ハイブリッド Azure AD 参加状態を評価する 

ハイブリッド Azure AD 参加に失敗した場合、発生した問題の詳細がダイアログ ボックスに表示されます。

**最も一般的な問題:**

- AD FS または Azure AD が正しく構成されていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/02.png)

- ドメイン ユーザーとしてサインオンしていない

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    これが発生する理由はいくつかあります。
    
    - サインインしているユーザーがドメイン ユーザーでない場合 (ローカル ユーザーなど)。 ダウンレベルのデバイスでのハイブリッド Azure AD 参加は、ドメイン ユーザーに対してのみサポートされています。
    
    - Autoworkplace.exe が Azure AD または AD FS で自動的に認証できない場合。 これは、Azure AD URL への送信ネットワーク接続の問題が原因である可能性があります。 また、多要素認証 (MFA) がユーザーに対して有効化/構成され、WIAORMUTLIAUTHN がフェデレーション サーバーで構成されていない可能性があります。 また、ホーム領域検出 (HRD) ページがユーザーの操作を待っているため、**autoworkplace.exe** では、トークンのサイレント要求ができなくなっていることも考えられます。
    
    - 組織は Azure AD シームレス シングル サインオンを使用しており、デバイスの IE イントラネット設定には `https://autologon.microsoftazuread-sso.com` または `https://aadg.windows.net.nsatc.net` が存在せず、イントラネット ゾーンについて **[スクリプトを介したステータス バーの更新を許可する]** が有効にされていません。

- クォータに達している

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- サービスが応答していない 

    ![[Workplace Join for Windows (Windows の社内参加)]](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

状態に関する情報は、**Applications and Services Log\Microsoft-Workplace Join** のイベント ログで確認することもできます。
  
**ハイブリッド Azure AD 参加に失敗する最も一般的な原因:** 

- コンピューターが、組織の内部ネットワーク、またはオンプレミスの AD ドメイン コントローラーに接続している VPN と接続していない。

- ローカル コンピューター アカウントでコンピューターにログオンしている。 

- 次のようなサービス構成の問題がある。 

  - フェデレーション サーバーが **WIAORMULTIAUTHN** をサポートするように構成されている。 

  - コンピューターのフォレストに、Azure AD の検証済みドメイン名を指すサービス接続ポイント オブジェクトがない。 

  - ユーザーがデバイスの上限に達している。 

## <a name="next-steps"></a>次の手順

ご不明な点がある場合は、[デバイス管理の FAQ](faq.md) をご覧ください。  
