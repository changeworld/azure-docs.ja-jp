---
title: マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: マネージド ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: efa653ecf306f5ac5eefaddd61d98e81f919876d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513302"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>チュートリアル:マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成

デバイスはユーザーと同様、保護の対象であると同時に、時と場所を選ばずにリソースを保護するために使用したいもう 1 つの重要な ID です。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定して管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

このチュートリアルでは、AD ドメイン参加済みコンピューター デバイスのハイブリッド Azure AD 参加をマネージド環境で構成する方法について説明します。 

マネージド環境は、[パスワード ハッシュ同期 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) または[パススルー認証 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) の[シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)を使用してデプロイできます。
これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 参加済みデバイスの確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の事項を熟知していることを前提としています。

- [Azure Active Directory でのデバイス ID 管理の概要](../device-management-introduction.md)
- [ハイブリッド Azure Active Directory Join の実装を計画する方法](hybrid-azuread-join-plan.md)
- [Hybrid Azure AD Join の制御された検証を実行する方法](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD は、マネージド ドメインでのスマートカードや証明書をサポートしていません。

この記事のシナリオを構成するには、[Azure AD Connect の最新バージョン](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 以降) がインストールされている必要があります。

Azure AD Connect が、Azure AD に参加するハイブリッド Azure AD にするデバイスのコンピューター オブジェクトを同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU を Azure AD Connect についても構成する必要があります。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、[Azure AD Connect を使用したフィルタリングの構成](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering)に関する記事を参照してください。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 関連するウィザードは、デバイス登録のためのサービス接続ポイント (SCP) を構成します。

この記事の構成手順は、このウィザードに基づいています。

ハイブリッド Azure AD 参加を使用するには、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (シームレス SSO を使用している場合、または使用する予定の場合)

組織が送信プロキシを介してインターネットにアクセスする必要がある場合、Microsoft は、Windows 10 コンピューターを Azure AD にデバイス登録できるように [Web プロキシ自動発見 (WPAD) を実装](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))することをお勧めします。 WPAD の構成と管理に関する問題が発生する場合、「[troubleshooting automatic detection (自動検出のトラブルシューティング)](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)」をご覧ください。 

Windows 10 1709 以降では、WPAD を使用しておらず、自分のコンピューター上でプロキシ設定を構成する必要がある場合、[グループ ポリシー オブジェクト (GPO) を使用して WinHTTP 設定を構成する](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)ことでそれを行えます。

> [!NOTE]
> WinHTTP 設定を使用してコンピューター上でプロキシ設定を構成すると、構成されるプロキシに接続できないコンピューターでインターネットへの接続が失敗します。

組織が認証された送信プロキシ経由でインターネットにアクセスする必要がある場合は、Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。

## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次のものが必要です。

- Azure AD テナントの全体管理者の資格情報。  
- 各フォレストのエンタープライズ管理者の資格情報。

**Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには:**

1. Azure AD Connect を起動し、 **[構成]** をクリックします。

   ![ようこそ](./media/hybrid-azuread-join-managed-domains/11.png)

1. **[追加のタスク]** ページで、 **[デバイス オプションの構成]** を選択し、 **[次へ]** をクリックします。

   ![追加のタスク](./media/hybrid-azuread-join-managed-domains/12.png)

1. **[概要]** ページで、 **[次へ]** をクリックします。

   ![概要](./media/hybrid-azuread-join-managed-domains/13.png)

1. **[Azure AD に接続]** ページで、Azure AD テナントの全体管理者の資格情報を入力します。  

   ![Azure への接続](./media/hybrid-azuread-join-managed-domains/14.png)

1. **[デバイス オプション]** ページで、 **[ハイブリッド Azure AD 参加の構成]** を選択し、 **[次へ]** をクリックします。

   ![デバイス オプション](./media/hybrid-azuread-join-managed-domains/15.png)

1. **[SCP]** ページで、Azure AD Connect で SCP を構成するフォレストごとに次の手順を実行し、 **[次へ]** をクリックします。

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. フォレストを選択します。
   1. 認証サービスを選択します。
   1. **[追加]** をクリックして、エンタープライズ管理者の資格情報を入力します。

1. **[デバイスのオペレーティング システム]** ページで、Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、 **[次へ]** をクリックします。

   ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-managed-domains/17.png)

1. **[構成の準備完了]** ページで、 **[構成]** をクリックします。

   ![構成の準備完了](./media/hybrid-azuread-join-managed-domains/19.png)

1. **[構成が完了しました]** ページで、 **[終了]** をクリックします。

   ![構成の完了](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイスの登録用のローカル イントラネット設定の構成
- シームレス シングル サインオン (SSO) の構成
- ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、ドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

さらに、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="configure-seamless-sso"></a>シームレス SSO の構成

Azure AD クラウド認証方法として[パスワード ハッシュ同期 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) または[パススルー認証 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) を使用しているマネージド ドメインで、ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するには、[シームレス SSO を構成](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature)する必要もあります。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、Microsoft ダウンロード センターで入手できる [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) を組織がインストールする必要があります。

 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) などのソフトウェア配布システムを使用して、パッケージをデプロイできます。 パッケージは、quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 Azure AD によって認証が行われた後、ユーザーの資格情報を使ってサイレントにデバイスが Azure AD に登録されます。

## <a name="verify-the-registration"></a>登録の確認

Azure テナントのデバイス登録状態を確認するには、 **[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** の **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** コマンドレットを使用できます。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を備えたオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** でなければなりません。 これは、Azure AD ポータルの [デバイス] ページの **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
- 条件付きアクセスで使用されるデバイスの **Enabled** の値は **True**、**DeviceTrustLevel**の値は **Managed** でなければなりません。

**サービスの詳細を確認するには:**

1. **Windows PowerShell** を管理者として開きます。
1. 「`Connect-MsolService`」と入力して Azure テナントに接続します。  
1. 「 `get-msoldevice -deviceId <deviceId>`」と入力します。
1. **[有効]** が **[True]** に設定されていることを確認します。

## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、次のトピックを参照してください。

- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次の手順

- Azure AD ポータルでのデバイス ID の管理について詳しくは、[Azure portal を使用してデバイス ID を管理する方法](device-management-azure-portal.md)に関するページを参照してください。
