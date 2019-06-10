---
title: Azure マネージド ワークステーションをデプロイする- Azure Active Directory
description: セキュリティで保護された Azure マネージド ワークステーションをデプロイし、間違った構成やセキュリティ侵害による違反のリスクを減らす方法について学習します
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307556"
---
# <a name="deploy-a-secure-workstation"></a>セキュリティで保護されたワークステーションをデプロイする

[セキュリティで保護されたワークステーションへのアクセスが重要である理由](concept-azure-managed-workstation.md)を理解できました。次は、利用可能なツールを使用するデプロイのプロセスを開始します。 このガイドでは定義済みのプロファイルを使用して、最初からより安全なワークステーションを作成します。

![セキュリティで保護されたワークステーションのデプロイ](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

ソリューションをデプロイする前に、使用するプロファイルを選択する必要があります。 選択されたプロファイルのいずれかを適用し、要件に基づいて Intune でプロファイルを割り当てることで、別のプロファイルに移動できることに注意することが重要です。 複数のプロファイルをデプロイで同時に利用し、タグまたはグループの割り当てを使用して割り当てることができます。

| プロファイル | 低 | 拡張 | 高 | 専用イメージ | セキュリティで保護 | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD のユーザー | はい | はい | はい | はい | はい | はい |
| Intune マネージド | はい | はい | はい | はい | はい | はい |
| Azure AD 登録済みデバイス | はい |  |  |  |  | |   |
| Azure AD 参加済みデバイス |   | はい | はい | はい | はい | はい |
| Intune セキュリティ ベースライン適用 |   | はい <br> (拡張) | はい <br> (高セキュリティ) | はい <br> (NCSC) | はい <br> (セキュリティで保護) |  NA |
| ハードウェアがセキュリティで保護された Windows 10 の標準を満たしている |   | はい | はい | はい | はい | はい |
| Microsoft Defender ATP が有効になっている |   | はい  | はい | はい | はい | はい |
| 管理者権限の削除 |   |   | はい  | はい | はい | はい |
| Microsoft Autopilot を使用するデプロイ |   |   | はい  | はい | はい | はい |
| Intune でのみインストールされるアプリ |   |   |   | はい | はい |はい |
| 承認済みリストのみに制限される URL |   |   |   | はい | はい |はい |
| インターネット (ブロックされた受信/送信) |   |   |   |  |  |はい |

## <a name="license-requirements"></a>ライセンスの要件

このガイドで説明する概念では、Microsoft 365 Enterprise E5 または同等の SKU を前提としています。 このガイドの一部の推奨事項は、以前の SKU で実装することができます。 追加情報については、[Microsoft 365 Enterprise のライセンス](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)に関するページを参照してください。

ライセンスのプロビジョニングを自動化するために、ユーザーに対して[グループベースのライセンス](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)を構成することができます。

## <a name="azure-active-directory-configuration"></a>Azure Active Directory の構成

管理者ワークステーションのユーザー、グループ、およびデバイスを管理する、Azure Active Directory (Azure AD) ディレクトリを構成するには、[管理者アカウント](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)で ID サービスおよび機能を有効にする必要があります。

セキュリティで保護されたワークステーションの管理者アカウントを作成するときに、現在のワークステーションにアカウントを公開します。 この初期構成とすべてのグローバル構成は、既知の安全なデバイスから行うことをお勧めします。 初めて使用する際に最初にさらされる攻撃からのリスクを減らすために、[マルウェア感染を防ぐ](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)ガイダンスを検討することができます。

組織では、少なくとも管理者には多要素認証を要求する必要があります。 実装のガイダンスについては、[クラウドベースの MFA のデプロイ](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)に関するページを参照してください。

### <a name="azure-ad-users-and-groups"></a>Azure AD のユーザーとグループ

Azure portal から、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[新しいユーザー]** の順に移動します。 デバイスの管理者となる、[セキュリティで保護されたワークステーションのユーザーを作成](https://docs.microsoft.com/Intune/quickstart-create-user)します。

* **名前** - セキュリティで保護されたワークステーションの管理者
* **ユーザー名** - secure-ws-admin@identityitpro.com
* **ディレクトリ ロール** - **制限付き管理者**。以下の管理ロールを選択します
   * **Intune 管理者**
* **作成**

2 つのグループを作成します。1 つはワークステーションのユーザーのもので、もう 1 つはワークステーション自体のものです。 Azure portal から、 **[Azure Active Directory]**  >  **[グループ]**  >  **[新しいグループ]** の順に移動します

ワークステーション ユーザーの最初のグループ。 ユーザーへのライセンスのプロビジョニングを自動化するために、このグループ内のユーザーに対して[グループベースのライセンス](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign)を構成することができます。

* **グループの種類** - セキュリティ
* **グループの名前** - セキュリティで保護されたワークステーションのユーザー
* **メンバーシップの種類**: 割り当て済み
* セキュリティで保護されたワークステーションの管理者ユーザーをグループに追加します
   * secure-ws-admin@identityitpro.com
* セキュリティで保護されたワークステーションを管理する他のすべてのユーザーを、グループに追加することができます
* **作成**

ワークステーション デバイスの 2 つ目のグループ。

* **グループの種類** - セキュリティ
* **グループ名** - セキュリティで保護されたワークステーション
* **メンバーシップの種類**: 割り当て済み
* **作成**

### <a name="azure-ad-device-configuration"></a>Azure AD デバイスの構成

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>デバイスを Azure AD に参加させることができるユーザーを指定する

管理セキュリティ グループがデバイスをドメインに参加させられるように、Active Directory でデバイスの設定を構成します。 Azure portal からこの設定を構成するには、 **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** の順に移動します。 **[ユーザーはデバイスを Azure AD に参加させることができます]** で **[選択済み]** を選び、"セキュリティで保護されたワークステーションのユーザー" グループを選択します。

#### <a name="removal-of-local-admin-rights"></a>ローカル管理者権限の削除

ワークステーションのロールアウトの一環として、VIP、DevOps、およびセキュア レベルのワークステーションのユーザーには、自分のコンピューターに対する管理者権限は付与されません。 Azure portal からこの設定を構成するには、 **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** の順に移動します。 **[Azure AD 参加済みデバイス上の追加のローカル管理者]** で、 **[なし]** を選択します。

#### <a name="require-multi-factor-authentication-to-join-devices"></a>デバイスを参加させるには多要素認証が必要

Azure AD にデバイスを参加させるプロセスをさらに強化するには、 **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイスの設定]** の順に移動し、 **[デバイスを参加させるには多要素認証が必要]** で **[はい]** を選んでから、 **[保存]** を選択します。

#### <a name="configure-mdm"></a>MDM を構成する

Azure portal から、 **[Azure Active Directory]**  >  **[モビリティ (MDM および MAM)]**  >  **[Microsoft Intune]** の順に移動します。 このシナリオではデバイスを Intune で管理できるようにするため、 **[MDM ユーザー スコープ]** の設定を **[すべて]** に変更し、 **[保存]** を選択します。 詳細については、[Intune の「クイック スタート: Windows 10 デバイスの自動登録を設定する](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)」を参照してください。 Intune の構成とコンプライアンス ポリシーは以降の手順で作成します。

#### <a name="azure-ad-conditional-access"></a>Azure AD の条件付きアクセス

Azure AD の条件付きアクセスは、準拠デバイスでこれらの特権管理タスクを保持するのに役立つ場合があります。 **セキュリティで保護されたワークステーションのユーザー** グループのメンバーとして定義されたユーザーは、クラウド アプリケーションにサインインするときに多要素認証を行う必要があります。 ベスト プラクティス ガイダンスに従い、ポリシーから緊急アクセス アカウントを除外します。 追加情報については、「[Azure AD で緊急アクセス アカウントを管理する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)」を参照してください

Azure portal から条件付きアクセスを構成するには、 **[Azure Active Directory]**  >  **[条件付きアクセス]**  >  **[新しいポリシー]** の順に移動します。

* **名前** - セキュリティで保護されたデバイスにはポリシーが必要
* 割り当て
   * **ユーザーとグループ**
      * 含める - **ユーザーとグループ** - 先ほど作成された**セキュリティで保護されたワークステーションのユーザー** グループを選択します
      * 除外する - **ユーザーとグループ** - 組織の緊急アクセス アカウントを選択します
   * **クラウド アプリ**
      * 含める - **すべてのクラウド アプリ**
* アクセス制御
   * **許可** - **[アクセス権の付与]** ラジオ ボタンを選択します
      * **多要素認証が必要です**
      * **デバイスは準拠としてマーク済みである必要があります**
      * 複数のコントロールの場合 - **選択したコントロールすべてが必要**
* ポリシーを有効にする - **オン**

組織では任意で、ユーザーが会社のリソースにアクセスしない国をブロックするようにポリシーを作成することができます。 IP の場所ベースの条件付きアクセス ポリシーの詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)」を参照してください

## <a name="intune-configuration"></a>Intune の構成

### <a name="configure-enrollment-status"></a>登録ステータスを構成する

セキュリティで保護されたワークステーションが信頼できるクリーンなデバイスであることを確認する、サプライ チェーン管理で概説されているように、新しいデバイスの購入時に、出荷時の [S モードの Windows 10 Pro](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode) の設定にすることをお勧めします。これにより、サプライ チェーン管理時の漏えいと脆弱性を最小限に抑えられます。 デバイスは、サプライヤーから受け取ると、Autopilot を使用して S モードから解除されます。 以下のガイダンスでは、変換プロセスの適用に関する詳細を提供します。

使用する前に、デバイスが完全に構成されていることを確認する必要があります。 Intune では、**すべてのアプリとプロファイルがインストールされるまでデバイスの使用をブロックする**方法が提供されます。 

1. **Azure portal** から、以下の順に移動します。
1. **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[登録ステータス ページ (プレビュー)]**  >  **[既定]**  >  **[設定]** 。
1. **[アプリとプロファイルのインストール進行状況を表示する]** を **[はい]** に設定します。
1. **[すべてのアプリとプロファイルがインストールされるまでデバイスの使用をブロックする]** を **[はい]** に設定します。

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot デプロイ プロファイルを作成する

デバイス グループを作成した後、デプロイ プロファイルを作成し、Autopilot デバイスを構成できるようにする必要があります。

1. Azure portal の Intune で、 **[デバイスの登録]**  >  **[Windows の登録]**  >  **[デプロイ プロファイル]**  >  **[プロファイルの作成]** の順に選択します。
1. 名前として、「**セキュリティで保護されたワークステーションのデプロイ プロファイル**」と入力します。 説明として、「**セキュリティで保護されたワークステーションのデプロイ**」と入力します。
1. [すべての対象デバイスを Autopilot に変換する] を [はい] に設定します。 この設定により、リスト内のすべてのデバイスが確実に Autopilot デプロイ サービスに登録されます。  登録の処理を 48 時間できるようにします。
1. デプロイ モードについては、 **[自己展開 (プレビュー)]** を選択します。 このプロファイルを持つデバイスは、デバイスを登録するユーザーに関連付けられます。 デバイスを登録するには、ユーザーの資格情報が必要です。
1. [Azure AD への参加の種類] ボックスでは、 **[Azure AD 参加済み]** を選択し、グレー表示にする必要があります。
1. [Out-of-box experience (OOBE)] を選び、以下のオプションを構成し、他の設定は既定のままにして、 **[OK]** を選択します。
   1. ユーザー アカウントの種類:**Standard**
1. **[作成]** を選択してプロファイルを作成します。 これで、Autopilot デプロイ プロファイルをデバイスに割り当てられるようになりました。
1. **[割り当て]**  >  **[割当先]**  >  **[選択したグループ]** の順に選択します
   1. **含めるグループを選択** - セキュリティで保護されたワークステーションのユーザー

### <a name="configure-windows-update"></a>Windows Update を構成する

組織で実行できる最も重要なことの 1 つは、Windows 10 を最新の状態に保つことです。 Windows 10 を安全な状態に保つために、更新プログラムがワークステーションに適用されるペースを管理する更新プログラム リングをデプロイします。 この構成については、**Azure portal** >  **[Microsoft Intune]**  >  **[ソフトウェア更新プログラム]**  >  **[Windows 10 更新プログラムのリング]** で見つけることができます。

既定値から変更された以下の設定で、新しい更新プログラム リングを**作成**します。

* 名前 - **Azure マネージド ワークステーションの更新プログラム**
* サービス チャネル - **Windows Insider - 高速**
* Quality update deferral (days)\(品質更新プログラムの延期 (日数)\) - **3**
* 機能更新プログラムの延期期間 (日数) - **3**
* 自動更新の動作 - **エンド ユーザーによる制御なしで自動的にインストールおよび再起動する**
* ユーザーによる Windows Update の一時停止をブロックする - **ブロック**
* 作業時間外に再起動するにはユーザーの承認が必要です - **必要**
* ユーザーに再起動を許可する (再起動猶予期間) - **必要**
   * 自動再起動後にユーザーを再起動猶予期間に切り替え (日数) - **3**
   * 再起動猶予期間リマインダーの一時停止 (日数) - **3**
   * 保留中の再起動の期限を設定する (日数) - **3**

**[作成]**  をクリックしてから、 **[割り当て]** タブで、含まれているグループとして**セキュリティで保護されたワークステーション** グループを追加します。

Windows Update のポリシーに関する追加情報については、「[Policy CSP - Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)」 (ポリシー CSP - 更新) を参照してください

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 統合

Windows Defender ATP と Microsoft Intune は連動しており、セキュリティ侵害を防ぐの役立ち、また、侵害の影響を抑えるのに役立ちます。 この機能によって、リアルタイムの検出が提供されます。 ATP では、エンドポイント デバイスのデプロイの広範な監査およびログ記録も提供されます。

Azure portal で Windows Defender ATP Intune 統合を構成するには、 **[Microsoft Intune]**  >  **[デバイスのポリシー準拠]**  >  **[Windows Defender ATP]** の順に移動します。

1. **[Configuring Windows Defender ATP]\(Windows Defender ATP の構成\)** の手順 1 で、 **[Connect Windows Defender ATP to Microsoft Intune in the Windows Defender Security Center​]\(Windows Defender セキュリティ センターで Windows Defender ATP を Microsoft Intune に接続する\)** をクリックします。
1. Windows Defender セキュリティ センターで、次のようにします。
   1. **[設定]**  >  **[高度な機能]** の順に選択します
   1. **[Microsoft Intune connection]\(Microsoft Intune の接続\)** では、 **[オン]** を選択します
   1. **[Save preferences]\(ユーザー設定の保存\)** を選択します
1. 接続が確立された後、Intune に戻り、上部にある **[更新]** をクリックします。
1. **[Connect Windows devices version 10.0.15063 and above to Windows Defender ATP]\(バージョン 10.0.15063 以上の Windows デバイスを Windows Defender ATP に接続します\)** を **[オン]** に設定します。
1. **[[Save]]**

追加情報については、[Windows Defender Advanced Threat Protection](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) に関する記事を参照してください。

### <a name="completing-hardening-of-the-workstation-profile"></a>ワークステーション プロファイルのセキュリティ強化の完了

ソリューションのセキュリティ強化を正常に完了するには、以下のグラフの必要な**プロファイル レベル**に基づいてスクリプトをダウンロードして実行します。

| プロファイル | ダウンロード場所 | ファイル名 |
| --- | --- | --- |
| 低セキュリティ | 該当なし |  該当なし |
| 強化されたセキュリティ | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| 高セキュリティ  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| 専用イメージ | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| 特殊なコンプライアンス* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| セキュリティで保護 | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

特殊なコンプライアンス* は、NCSC Windows 10 セキュリティ ベースラインで提供される特殊な構成を適用するスクリプトです。

選択されたスクリプトが正常に実行されたら、Microsoft Intune でプロファイルとポリシーを更新できます。 強化されたプロファイルとセキュリティで保護されたプロファイルのスクリプトによって、ポリシーとプロファイルが自動的に作成されますが、**セキュリティで保護されたワークステーション** グループにはご自分でポリシーを割り当てる必要があります。

* スクリプトによって作成された Intune デバイス構成プロファイルについては、**Azure portal** >  **[Microsoft Intune]**  >  **[デバイスの構成]**  >  **[プロファイル]** を参照してください。
* スクリプトによって作成された Intune デバイス コンプライアンス ポリシーについては、**Azure portal** >  **[Microsoft Intune]**  >  **[デバイスのポリシー準拠]**  >  **[ポリシー]** を参照してください。

基になる SECCON ドキュメントと必要な追加のセキュリティ強化で概説されているように、変更内容を確認するためにプロファイルをエクスポートし、エクスポート ファイルに変更内容を適用することもできます。

[DeviceConfiguration GiuHub リポジトリ](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration)の Intune データ エクスポート スクリプト `DeviceConfiguration_Export.ps1` を実行することで、既存のすべての Intune プロファイルの現在のエクスポートが提供されます。

## <a name="additional-configurations-and-hardening-to-consider"></a>考慮すべき追加の構成とセキュリティ強化

提供されているガイダンスによりセキュリティで保護されたワークステーションが有効になっており、代替のブラウザー アクセス、送信 HTTP で許可およびブロックされた Web サイト、およびカスタム PowerShell スクリプトを実行する機能など、追加の制御も考慮する必要があります。

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>ファイアウォールの構成サービス プロバイダー (CSP) での厳しい受信および送信規則

許可されたエンドポイントとブロックされたエンドポイントを反映するように、受信と送信の両方の規則の追加管理を更新することができます。 セキュリティで保護されたワークステーションのセキュリティを引き続き強化するため、既定として受信と送信をすべて拒否に制限を移行し、送信に対して許可されたサイトを追加して共通の信頼できる Web サイトを反映させます。 ファイアウォールの構成サービス プロバイダーに関する追加の構成情報については、「[Firewall CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp)」 (ファイアウォール CSP) の記事を参照してください。

既定の制限されている推奨事項は次のとおりです。

* 受信をすべて拒否
* 送信をすべて拒否

Spamhaus Project では、組織でサイトをブロックするための開始点として使用できる、優れたリストが保持されています。これは[ドメイン禁止一覧 (DBL)](https://www.spamhaus.org/dbl/) と呼ばれます。

### <a name="managing-local-applications"></a>ローカル アプリケーションの管理

生産性向上アプリケーションの削除を含め、ローカル アプリケーションを削除すると、セキュリティで保護されたワークステーションが完全にセキュリティ強化された状態に移ります。 この例では、既定のブラウザーとして Chrome を追加し、Intune を使用してプラグインを含むブラウザーを変更する機能を制限します。

#### <a name="deploy-applications-using-intune"></a>Intune を使用してアプリケーションをデプロイする

場合によっては、セキュリティで保護されたワークステーションで Google Chrome ブラウザーのようなアプリケーションが必要になります。 次の例では、先ほど作成された**セキュリティで保護されたワークステーション** セキュリティ グループ内のデバイスに Chrome をインストールする手順を提供します。

1. オフライン インストーラーである [Windows 64 ビット版 Chrome バンドル](https://cloud.google.com/chrome-enterprise/browser/download/)をダウンロードします
1. ファイルを抽出し、Intune を使用してインストールするために `GoogleChromeStandaloneEnterprise64.msi` の場所をメモしておきます
1. **Azure portal** で、 **[Microsoft Intune]**  >  **[クライアント アプリ]**  >  **[アプリ]**  >  **[追加]** の順に移動します
1. **[アプリの種類]** で、 **[基幹業務]** を選択します
1. **[アプリのパッケージ ファイル]** では、抽出された場所から `GoogleChromeStandaloneEnterprise64.msi` を選択し、 **[OK]** をクリックします
1. **[アプリ情報]** では、説明と発行元を入力して **[OK]** を選択します
1. **[追加]** をクリックします。
1. **[割り当て]** では、 **[割り当ての種類]** で **[登録済みデバイスで使用可能]** を選択します
1. **[組み込まれたグループ]** では、先ほど作成された**セキュリティで保護されたワークステーション** グループを追加します
1. **[OK]** 、 **[保存]** の順にクリックします

Chrome 設定の構成に関する追加のガイダンスについては、サポート記事の「[Manage Chrome Browser with Microsoft Intune](https://support.google.com/chrome/a/answer/9102677)」 (Microsoft Intune で Chrome ブラウザーを管理する) を参照してください。

#### <a name="configuring-the-company-portal-for-custom-apps"></a>カスタム アプリ用のポータル サイトの構成

セキュリティで保護されたモードでは、アプリケーションのインストールが Intune ポータル サイトに制限されます。 しかし、ポータルをインストールするには、Microsoft Store へのアクセスが必要です。 セキュリティで保護されたソリューションでは、ポータル サイトのオフライン モードを使用して、すべてのデバイスでポータルを利用できるようにします。

[ポータル サイト](https://docs.microsoft.com/Intune/store-apps-company-portal-app)の Intune で管理されているコピーをインストールすると、セキュリティで保護されたワークステーションのユーザーにオンデマンドで追加のツールをプッシュ ダウンできるようになります。

一部の組織では、Windows 32 ビット アプリ、またはデプロイするために他の準備を必要とするアプリをインストールする必要がある場合があります。 これらのアプリケーションの場合、[Microsoft win32 コンテンツ準備ツール](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)で、インストール用の `.intunewin` 形式のファイルを使用するための準備を行うことができます。

### <a name="setting-up-custom-settings-using-powershell"></a>PowerShell を使用するカスタム設定の実行

ホスト管理の拡張性を提供するための PowerShell の使用例も利用します。 スクリプトによって、ホストに既定の背景が設定されます。 この機能はプロファイルでも利用でき、機能を説明するためにのみ使用されます。

ソリューションでは、いくつかのカスタム コントロールと、セキュリティで保護されたワークステーションの設定を行う必要がある場合があります。 この例では、Powershell を使ってワークステーションの背景を変更し、使用する準備ができているセキュリティで保護されたワークステーションとしてデバイスを簡単に特定できるようにします。 この例では PowerShell を使用してこのタスクを完了しますが、Azure portal で完了することもできます。

この例では、以下の[無料の汎用背景イメージ](https://i.imgur.com/OAJ28zO.png)と、Microsoft スクリプト センターの [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) を使用して、Windows で起動時に背景を読み込めるようにします。

1. ローカル デバイスにスクリプトをダウンロードする
1. customerXXXX、背景ファイルを反映するためにスクリプトで使用する予定の背景のダウンロード場所、およびデプロイで使用するフォルダーを更新します。 この例では、customerXXXX を背景に置き換えます。  
1. **Azure portal** >  **[Microsoft Intune]**  >  **[デバイスの構成]**  >  **[PowerShell スクリプト]**  >  **[追加]** の順に移動します
1. スクリプトの**名前**を入力し、それをダウンロードした**スクリプトの場所**を指定します
1. **[構成]** を選択します。
   1. **[このスクリプトをログオンしたユーザーの資格情報を使用して実行する]** を **[はい]** に設定します
   1. **[OK]**
1. **[作成]**
1. **[割り当て]**  >  **[グループの選択]** の順に選択します
   1. 先ほど作成されたセキュリティ グループの**セキュリティで保護されたワークステーション**を追加して、 **[保存]** をクリックします

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>プレビューの使用: 2018 年 10 月の MDM セキュリティ ベースライン

Microsoft Intune では、一般的なベースライン セキュリティ ポスチャを適用する簡単な方法を管理者に提供するセキュリティ ベースライン管理機能が導入されています。 ベースラインでは、ロックダウンされている強化されたプロファイル ワークステーションを実現するための同様の方法が提供されます。

セキュリティで保護されたワークステーションの実装では、このベースラインは使用されません。セキュリティで保護された構成デプロイと競合するためです。

|   |   |   |
| :---: | :---: | :---: |
| ロック中 | デバイスのインストール | リモート デスクトップ サービス |
| アプリ実行時 | デバイスのロック | リモート管理 |
| アプリケーション管理 | イベント ログ サービス | リモート プロシージャ コール |
| 自動再生 | エクスペリエンス | Search |
| BitLocker | Exploit Guard | スマート スクリーン |
| ブラウザー | エクスプローラー | システム要件|
| 接続 | Internet Explorer | Wi-Fi |
| 資格情報の委任 | ローカル ポリシー セキュリティ オプション | Windows 接続マネージャー |
| 資格情報 UI | MS セキュリティ ガイド | Windows Defender|
| データ保護 | MSS レガシ | Windows Ink ワークスペース |
| Device Guard | 累乗 | Windows PowerShell |

このプレビュー機能の詳細については、「[Intune 用の Windows セキュリティ ベースラインの設定](https://docs.microsoft.com/Intune/security-baseline-settings-windows)」の記事を参照してください。

## <a name="enroll-and-validate-your-first-device"></a>最初のデバイスを登録して確認する

1. デバイスを登録するには、次の情報が必要です。
   * **シリアル番号** - デバイス シャーシにあります
   * **Windows 製品 ID** - Windows 設定メニューの **[システム]**  >  **[バージョン情報]** にあります。
   * [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) を実行すると、デバイス登録用の CSV ハッシュ ファイルに必要なすべての情報が提供されます。 
      * `Get-WindowsAutoPilotInfo – outputfile device1.csv` を実行し、Intune にインポートできる CSV ファイルとして情報を出力します。

   > [!NOTE]
   > スクリプトでは管理者特権が必要になり、リモートの署名済みとして実行されます。 次のコマンドを使用して、スクリプトを正しく実行できるようにすることができます。 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  この情報は、Windows 10 バージョン 1809 以降のデバイスにサインインして収集することができます。または、ハードウェア リセラーでは、新しいデバイスの注文時にこの情報を提供できます。
1. 必要な情報を収集して、**Azure portal** に戻ります。 **[Microsoft Intune]**  >  **[デバイスの登録]**  >  **[Windows の登録]**  >  **[デバイス - Windows AutoPilot デバイスを管理する]** の順に移動し、 **[インポート]** を選択して、自分で作成した、または提供された CSV ファイルを選びます。
1. 現在登録されているデバイスを、先ほど作成されたセキュリティ グループの**セキュリティで保護されたワークステーション**に追加します。
1. 構成する Windows 10 デバイスから、 **[Windows の設定]**  >  **[更新とセキュリティ]**  >  **[回復]** の順に移動します。 **[この PC を初期状態に戻す]** で **[開始する]** を選択し、プロンプトに従い、構成されたプロファイルおよびコンプライアンス ポリシーを使用してデバイスを初期状態に戻し、再構成します。

デバイスが構成された後、確認を完了し、構成をチェックします。 デプロイを続行する前に、最初のデバイスが正しく構成されていることを確認します。

## <a name="assignment-and-monitoring"></a>割り当てと監視

デバイスとユーザーを割り当てるには、[選択されたプロファイル](https://docs.microsoft.com/intune/device-profile-assign)のセキュリティ グループへのマッピングが必要になり、サービスへのアクセス許可が付与される新しいユーザーをすべてセキュリティ グループにも追加する必要があります。

プロファイルの監視は、監視用の [Microsoft Intune プロファイル](https://docs.microsoft.com/intune/device-profile-monitor)を使用して行うことができます。

## <a name="next-steps"></a>次の手順

[Microsoft Intune](https://docs.microsoft.com/intune/index) のドキュメント

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) のドキュメント