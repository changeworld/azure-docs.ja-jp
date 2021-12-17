---
title: Microsoft と Azure のセキュリティ リソースを使用して、体系的 ID 侵害からの回復を支援する | Microsoft Docs
description: Microsoft と Azure のセキュリティ リソース (Microsoft 365 Defender、Microsoft Sentinel、Azure Active Directory、Microsoft Defender for Cloud、Microsoft の推奨事項など) を使用して、2020 年 12 月の Nobelium 攻撃 (Solorigate) と同様の体系的 ID 侵害からシステムを保護する方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: b858cb8d2830d305a90dfb8ecbda9fe2154e11f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323199"
---
# <a name="recovering-from-systemic-identity-compromise"></a>システムの ID 侵害からの復旧

この記事では、2020 年 12 月の [Nobelium](https://aka.ms/solorigate) 攻撃など、組織に対する体系的 ID 侵害攻撃からの回復に関する Microsoft のリソースと推奨事項について説明します。

この記事の内容は、Microsoft Detection and Response Team (DART) によって提供されるガイダンスに基づいています。DART は、侵害に対処し、お客様がサイバー回復力を持つように支援します。 DART チームからのガイダンスについては、[Microsoft のセキュリティ ブログ シリーズ](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/)を参照してください。

多くの組織は、ID およびアクセス管理のセキュリティを強化するために、クラウドベースのアプローチに移行しました。 しかしながら、組織は、オンプレミスのシステムを保持して、さまざまなハイブリッド アーキテクチャの方法を使用することもあります。 この記事では、体系的 ID 攻撃がクラウド システム、オンプレミス システム、ハイブリッド システムに影響することを認識し、それらすべての環境に関する推奨事項および参照情報を提供します。

> [!IMPORTANT]
> この情報は現状のまま提供され、一般化されたガイダンスとなります。IT 環境およびテナントへのこのガイダンスの適用方法を最終的に決定するには、独自の環境とニーズを考慮する必要があります。これを判断する最適な立場にいるのはそれぞれの顧客です。
>

## <a name="about-systemic-identity-compromise"></a>体系的 ID 侵害について

組織に対する体系的 ID 侵害攻撃が発生するのは、攻撃者が組織の ID インフラストラクチャの管理に足場を得たときです。

これが組織で発生すると、攻撃者と競い合って、さらなる被害を受ける前に環境をセキュリティで保護することになります。

- **環境の ID インフラストラクチャの管理制御を得た攻撃者** は、その制御を利用して、その環境内の ID および ID のアクセス許可を作成、変更、または削除できます。

    オンプレミスの侵害で、信頼できる SAML トークン署名証明書が [HSM](../../key-vault/keys/hsm-protected-keys.md)に格納されて "*いない*" 場合、その信頼できる SAML トークン署名証明書に対するアクセスもその攻撃に含まれます。

- **攻撃者は、証明書を使用して SAML トークンを偽造** し、組織の既存のユーザーやアカウントを偽装することができます。アカウントの資格情報へのアクセス権は必要なく、痕跡を残すこともありません。

- また、**高い権限が与えられているアカウントのアクセス権** を使用すると、既存のアプリケーションに攻撃者が管理する資格情報を追加することもできます。したがって、攻撃者はそのようなアクセス許可を使用して、検出されることなくシステムにアクセスできるようになります(API を呼び出すなど)。

## <a name="responding-to-the-attack"></a>攻撃に対応する


体系的 ID 侵害への対応には、次の図と表に示す手順を含める必要があります。

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="ID 侵害から回復する手順。":::


|手順  |説明  |
|---------|---------|
|**セキュリティで保護された通信を確立する**     |  体系的 ID 侵害を経験している組織は、すべての通信が影響を受けていると想定する必要があります。 どのような回復処置を行うとしても事前に、調査および対応の取り組みにとって重要なチームのメンバーが[安全に通信できる](#establish-secure-communications)ようにする必要があります。 <br><br>"*通信をセキュリティで保護することを必ず最初の手順として行う必要があります。そうすることで、攻撃者の知識がなくても先に進むことができます。* "|
|**環境を調査する**   | コア調査チームについて通信をセキュリティで保護した後で、初期アクセス ポイントと永続化手法を探し始めることができます。 [侵害の兆候を特定](#identify-indications-of-compromise)してから、初期アクセス ポイントと永続化を探します。 同時に、回復への取り組みの中で、[継続的監視操作の確立](#establish-continuous-monitoring)を開始します。        |
|**セキュリティ体制を改善する**     | システム セキュリティを強化するためのベスト プラクティスの推奨事項に従って、[セキュリティの機能を有効にします](#improve-security-posture)。  <br><br>時間の経過に伴って、セキュリティ環境が変化する際も、[継続的監視](#establish-continuous-monitoring)の取り組みを必ず続けます。    |
|**制御を回復して維持する**     |  環境の管理制御を攻撃者から取り戻す必要があります。 制御を取り戻して、システムのセキュリティ体制を更新した後で、可能性のあるすべての永続化手法および新しい初期アクセス悪用を[修復またはブロック](#remediate-and-retain-administrative-control)してください。       |
|     |         |

## <a name="establish-secure-communications"></a>セキュリティで保護された通信を確立する

対応を開始する前に、攻撃者から盗聴されることなく安全に通信できることを確認する必要があります。 インシデントに関係したすべての通信を確実に分離することで、攻撃者は調査に気付かず、対応処置によって不意打ちされます。

次に例を示します。

1. 最初の 1 対 1 およびグループの通信では、PSTN 通話、企業インフラストラクチャに接続されていない会議ブリッジ、およびエンドツーエンドで暗号化されたメッセージング ソリューションを使用することをお勧めします。

    セキュリティで保護されたチャネルで検証されない限り、これらのフレームワークの外部にある通信は、侵害された信頼できないものとして扱う必要があります。

2. このような最初の会話の後で、組織の運用テナントから分離された、まったく新しい Microsoft 365 テナントを作成することができます。 対応に参加する必要がある主要な担当者に対してのみ、アカウントを作成します。

新しい Microsoft 365 テナントを作成する場合は、特に管理者アカウントと権限について、テナントに関するすべてのベスト プラクティスに従ってください。 外部のアプリケーションやベンダーを信頼しないように、管理者権限を制限します。

> [!IMPORTANT]
> 新しいテナントについて、侵害された可能性がある既存の電子メール アカウントでは通信しないようにします。 

詳細については、「[Microsoft 365 を安全に使用するためのベスト プラクティス](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/)」を参照してください。

## <a name="identify-indications-of-compromise"></a>侵害の兆候を特定する

お客様には、Microsoft およびすべてのパートナーの両方を含むシステム プロバイダーからの更新プログラムに従い、提供される新しい検出と保護を実装し、公開された侵害インシデント (IOC) を特定することをお勧めします。

次の Microsoft セキュリティ製品の更新プログラムを確認し、推奨される変更をすべて実装します。

- [Microsoft Sentinel](../../sentinel/index.yml)
- [Microsoft 365 のセキュリティ ソリューションおよびサービス](/microsoft-365/security/)
- [Windows 10 Enterprise のセキュリティ](/windows/security/)
- [Microsoft Defender for Cloud Apps](/cloud-app-security/)

新しい更新プログラムを実装すると、以前の攻撃行動を特定したり、システムに対する今後の攻撃行動を防いだりするのに役立ちます。 IOC のリストは完全ではなく、調査の継続に伴って充実する可能性があることに注意してください。

したがって、次の処置も行うことをお勧めします。

- [Azure セキュリティ ベンチマークのドキュメント](/security/benchmark/azure/)を適用済みであることと、[Microsoft Defender for Cloud](../../security-center/index.yml) でコンプライアンスを監視していることを確認します。

- [Microsoft Sentinel](../../sentinel/understand-threat-intelligence.md) で Microsoft 365 データ コネクタを構成するなどして、脅威インテリジェンス フィードを SIEM に組み込みます。

詳細については、Microsoft のセキュリティに関するドキュメントを参照してください。

- [Microsoft のセキュリティ ドキュメント](/security/)
- [Azure セキュリティに関するドキュメント](../index.yml)

## <a name="investigate-your-environment"></a>環境を調査する

インシデントの対応者と主要担当者が共同作業を行うための安全な場所を確保したら、侵害された環境の調査を開始できます。

すべての異常動作の本質を突き止めることと、攻撃者のさらなるアクティビティを止める迅速な処置を取ることを、バランスよく行う必要があります。 修復を成功させるには、攻撃者が使用した最初の侵入方法と永続化方法について、この時点で可能な限り理解しておく必要があります。 調査中に見逃がした永続化方法があると、攻撃者が継続的にアクセスでき、再侵害の可能性が生じます。

この時点で、リスク分析を実行して、処置に優先順位を付けることをお勧めします。 詳細については、次を参照してください。

- [データセンターの脅威、脆弱性、およびリスク評価](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [脅威分析による新たな脅威の追跡と対応](/microsoft-365/security/defender-endpoint/threat-analytics)
- [脅威と脆弱性の管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Microsoft のセキュリティ サービスでは、詳細な調査のために豊富なリソースが提供されます。 以下のセクションでは、特に推奨される処置について説明します。


> [!NOTE]
> 一覧で示すログ ソースの 1 つ以上が現在のセキュリティ プログラムに含まれていないことがわかった場合は、検出と今後のログ レビューを有効にするために、できるだけ早く構成することをお勧めします。
>
> 将来的に組織の調査目標に合うように、ログのリテンション期間を構成してください。 法律、規制、または保険の目的で必要に応じて証拠を保持します。
>

### <a name="investigate-and-review-cloud-environment-logs"></a>クラウド環境のログを調査して確認する

クラウド環境のログを調査し、疑わしい操作や攻撃者による侵害の兆候を確認します。 たとえば、次のログを確認します。

- [統合監査ログ (UAL)](/powershell/module/exchange/search-unifiedauditlog)
- [Azure Active Directory (Azure AD) ログ](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Microsoft Exchange オンプレミスのログ](/exchange/mail-flow/transport-logs/transport-logs)
- VPN ログ ([VPN Gateway](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) など)
- エンジニアリング システムのログ
- ウイルス対策およびエンドポイント検出のログ

### <a name="review-endpoint-audit-logs"></a>エンドポイント監査ログを確認する

エンドポイント監査ログで、オンプレミスの変更を確認します (たとえば次の種類の操作)。

- グループ メンバーシップの変更
- ユーザー アカウントの新規作成
- Active Directory 内の委任

特に、他の侵害やアクティビティの典型的な兆候と共に発生したこれらの変更について注意してください。

### <a name="review-administrative-rights-in-your-environments"></a>環境内の管理者権限を確認する

クラウド環境とオンプレミス環境の両方で管理者権限を確認します。 次に例を示します。

|環境  |説明  |
|---------|---------|
|**すべてのクラウド環境**    |       - クラウド内の特権アクセス権を確認し、不要なアクセス許可があれば削除します<br>    - Privileged Identity Management (PIM) を実装します<br>    - 強化中に管理アクセスを制限する条件付きアクセス ポリシーを設定します      |
|**すべてのオンプレミス環境**     |       - オンプレミスの特権アクセスを確認し、不要なアクセス許可を削除します<br>   - 組み込みグループのメンバーシップを減らします<br>    - Active Directory の委任を確認します<br>    - 階層 0 環境を強化し、階層 0 の資産にアクセスできるユーザーを制限します      |
|**すべてのエンタープライズ アプリケーション**     | 次の操作を許可する、委任されたアクセス許可および同意付与を確認します。 <br><br>  - 特権ユーザーおよびロールの変更 <br>- すべてのメールボックスの読み取りまたはアクセス <br>- 他のユーザーの代理としての電子メールの送信または転送 <br>- OneDrive または SharePoint サイトのすべてのコンテンツへのアクセス <br>- ディレクトリの読み取り/書き込みができるサービス プリンシパルの追加      |
|**Microsoft 365 環境**     |次を含む、Microsoft 365 環境のアクセスと構成の設定を確認します。 <br>- SharePoint Online の共有 <br>- Microsoft Teams <br>- Power Apps <br>- Microsoft OneDrive for Business          |
| **環境内のユーザー アカウントを確認する**   |- 不要になったゲスト ユーザー アカウントを確認して削除します。 <br>- 委任、メールボックス フォルダーのアクセス許可、ActiveSync モバイル デバイス登録、受信トレイ規則、Outlook on the Web のオプションについて、電子メールの構成を確認します。 <br>- アプリケーション偽装の権限を確認し、レガシ認証の使用をできるだけ減らします。 <br>- MFA が適用されていることと、すべてのユーザーについて MFA とセルフサービス パスワード リセット (SSPR) 両方の連絡先情報が正しいことを検証します。         |
|     |         |

## <a name="establish-continuous-monitoring"></a>継続的な監視を確立する

攻撃者の行動の検出には、いくつかの方法が含まれます。また、組織が攻撃に対応するために使用できるセキュリティ ツールによって異なります。

たとえば、Microsoft のセキュリティ サービスには、以下のセクションで説明するように、攻撃に関連する特定のリソースとガイダンスが用意されている場合があります。

> [!IMPORTANT]
> 調査によって、管理権限がシステムの侵害によって盗まれ、そのために組織のグローバル管理者アカウントまたは信頼できる SAML トークン署名証明書 (あるいは両方) へのアクセスが与えられたという証拠が見つかった場合は、[管理制御を修復して保持](#remediate-and-retain-administrative-control)する処置の実行をお勧めします。
>

### <a name="monitoring-with-microsoft-sentinel"></a>Microsoft Sentinel を使用して監視する

Microsoft Sentinel には、調査に役立つ多くの組み込みリソース (環境内の関連する領域での攻撃を検出するのに役立つハンティング ブックや分析ルールなど) があります。

詳細については、次を参照してください。

- [環境を視覚化して分析する](../../sentinel/get-visibility.md)
- [難しい設定なしで脅威を検出する](../../sentinel/detect-threats-built-in.md)。

### <a name="monitoring-with-microsoft-365-defender"></a>Microsoft 365 Defender を使用して監視する

攻撃に関する具体的なガイダンスについては、Microsoft 365 Defender for Endpoint および Microsoft Defender ウイルス対策を確認することをお勧めします。

Microsoft 365 Defender、Microsoft 365 Defender for Identity、Microsoft Defender for Cloud Apps など、Microsoft セキュリティ センターで、検出、ハンティング クエリ、脅威分析レポートのその他の例を確認します。 確実にカバーするためには、すべてのドメイン コントローラーに加えて ADFS サーバーに [Microsoft Defender for Identity エージェント](/defender-for-identity/install-step4)をインストールしてください。

詳細については、次を参照してください。

- [脅威分析による新たな脅威の追跡と対応](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [脅威分析のアナリスト レポートを理解する](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>Azure Active Directory を使用して監視する

Azure Active Directory サインイン ログによって、多要素認証が正しく使用されているかどうかがわかります。 Azure portal の Azure Active Directory 領域からサインイン ログにアクセスするには、**Get-AzureADAuditSignInLogs** コマンドレットを使用するか、Microsoft Sentinel の **[ログ]** 領域で表示します。

たとえば、 **[MFA results]\(MFA 結果\)** フィールドの値が **[MFA requirement satisfied by claim in the token]\(トークンの要求によって MFA 要件が満たされる\)** の場合について、結果を検索またはフィルターします。 組織が ADFS を使用しているとき、ログに記録された要求が ADFS 構成に含まれない場合、それらの要求が攻撃者のアクティビティを示している可能性があります。

結果をさらに検索またはフィルター処理して、余分なノイズを除外します。 たとえば、フェデレーション ドメインからの結果のみを含めたい場合があります。 疑わしいサインインを見つけた場合は、IP アドレスやユーザー アカウントなどに基づいてさらにドリルダウンします。

次の表では、調査で Azure Active Directory のログを使用するその他の方法について説明します。

|メソッド  |説明  |
|---------|---------|
|**危険なサインイン イベントを分析する**     |  Azure Active Directory および Identity Protection プラットフォームでは、攻撃者が生成した SAML トークンの使用に関連するリスク イベントが生成される可能性があります。 <br><br>このようなイベントには、 *[unfamiliar properties]\(見慣れないプロパティ\)* 、 *[匿名 IP アドレス]* 、 *[あり得ない移動]* などのラベルが付けられます。 <br><br>管理者特権を持つアカウントに関連付けられているすべてのリスク イベント (自動的に却下または修復された可能性があるものも含む) を詳しく分析することをお勧めします。 たとえば、ユーザーの MFA が成功したことにより、リスク イベントや匿名 IP アドレスが自動的に修復される場合があります。 <br><br>すべての認証イベントが Azure AD に表示されるように、[ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) を使用してください。 |
|**ドメイン認証プロパティを検出する**     |  攻撃者がドメイン認証ポリシーを操作しようとすると、Azure Active Directory 監査ログに記録され、統合監査ログに反映されます。 <br><br> 統合監査ログ、Azure AD 監査ログ、および SIEM 環境で、 **[ドメイン認証の設定]** によって関連付けられたイベントを確認し、一覧表示されるすべてのアクティビティが予期され計画されたものであることを確かめます。   |
|**OAuth アプリケーションの資格情報を検出する**     |  特権アカウントの制御を得た攻撃者は、組織内のユーザーの電子メールにアクセスできるアプリケーションを探し、攻撃者が管理する資格情報をそのアプリケーションに追加します。 <br><br>たとえば、攻撃者の行動と一致する次のいずれかのアクティビティを探すことをお勧めします。 <br>- サービス プリンシパル資格情報の追加または更新 <br>- アプリケーションの証明書とシークレットの更新 <br>- アプリ ロールの割り当て許可のユーザーへの追加 <br>- Oauth2PermissionGrant の追加 |
|**アプリケーションによる電子メール アクセスを検出する**     |  環境内のアプリケーションによる電子メールへのアクセスを探します。 たとえば、[Microsoft 365 高度な監査機能](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations)を使用して、侵害されたアカウントを調査します。 |
|**サービス プリンシパルへの非対話型サインインを検出する**     | Azure Active Directory サインイン レポートによって、サービス プリンシパルの資格情報を使用した非対話型サインインの詳細が提供されます。  たとえば、サインイン レポートを使用して、調査にとって貴重なデータ (攻撃者が電子メール アプリケーションにアクセスするために使用した IP アドレスなど) を見つけることができます。        |
|     |         |


## <a name="improve-security-posture"></a>セキュリティ体制を改善する

システムでセキュリティ イベントが発生した場合は、現在のセキュリティ戦略と優先順位について熟考することをお勧めします。

インシデント レスポンダーは、組織が新しい脅威に直面したとき、優先すべき投資に関して推奨事項を提供するように求められることがよくあります。

この記事に記載されている推奨事項に加え、この攻撃者によって悪用後に使用される手法に特に対応すべき分野と、それを許した一般的なセキュリティ体制の間隙を優先することを検討してください。

次のセクションでは、一般的なセキュリティ体制と ID セキュリティ体制の両方を改善するための推奨事項を示します。

### <a name="improve-general-security-posture"></a>一般的なセキュリティ体制を改善する

一般的なセキュリティ体制を確保するには、次の処置をお勧めします。

- **利用している Microsoft 製品およびサービスに対してカスタマイズされた、セキュリティの基本的な推奨事項について、「[Microsoft セキュア スコア](/microsoft-365/security/mtp/microsoft-secure-score)** 」を確認します。

- **組織に EDR および SIEM ソリューションが配置されていることを確認します** ([Microsoft 365 Defender for Endpoint](/microsoft-365/security/defender/microsoft-365-defender) や [Microsoft Sentinel](../../sentinel/overview.md) など)。

- **Microsoft の [エンタープライズ アクセス モデル](/security/compass/privileged-access-access-model)** を確認します。

### <a name="improve-identity-security-posture"></a>ID セキュリティ体制を改善する

ID 関連のセキュリティ体制を確保するには、次の処置をお勧めします。

- **Microsoft の「[ID インフラストラクチャをセキュリティ保護する 5 つのステップ](steps-secure-identity.md)** 」を確認し、ご使用の ID アーキテクチャに適した手順を優先します。

- **認証ポリシーについて [Azure AD のセキュリティの既定値群を検討します](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)** 。

- **組織でのレガシ認証の使用を排除します** (システムまたはアプリケーションで必要な場合でも)。 詳細については、「[条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする](../../active-directory/conditional-access/block-legacy-authentication.md)」を参照してください。

    > [!NOTE]
    > Exchange チームは、2021 年後半には [EAS、EWS、POP、IMAP、および RPS プロトコルの基本認証を無効にする](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/)予定です。
    >
    > 明確にしておきたいのは、セキュリティの既定値群と認証ポリシーは独立しているが、補完的な機能を提供しているということです。
    >
    > 認証ポリシーを使用して、Exchange Online プロトコルのサブセットに対して基本認証をオフにするか、大規模な組織全体では段階的に基本認証をオフにするようにお客様にお勧めします。
    >

- **ADFS インフラストラクチャおよび AD Connect インフラストラクチャを階層 0 資産として扱います**.

- ADFS サービスの実行に使用されるアカウントを含め、**システムへのローカル管理アクセスを制限します**。

    ADFS を実行するアカウントに必要な最小限の特権は、 *[サービスとしてログオン]* ユーザー権利割り当てです。

- リモート デスクトップの Windows ファイアウォール ポリシーを使用して、**限定されたユーザーと限定された IP アドレス範囲に管理アクセスを制限します**。

    階層 0 のジャンプ ボックスまたは同等のシステムを設定することをお勧めします。

- 環境内の任意の場所からシステムへの **すべてのインバウンド SMB アクセスをブロックします**。 詳細については、「[境界を越えて: Windows で SMB トラフィックをセキュリティ保護する方法](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159)」を参照してください。 また、監視履歴およびプロアクティブな監視のために Windows ファイアウォール ログを SIEM にストリーミングすることをお勧めします。

- サービス アカウントを使用しており、それが環境でサポートされている場合は、**サービス アカウントからグループ管理サービス アカウント (gMSA) に移行します**。 gMSA に移行できない場合は、サービス アカウントのパスワードを複雑なパスワードにローテーションします。

- **ADFS システムで詳細ログが有効になっていることを確認します**。 たとえば、次のコマンドを実行します。

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>管理制御を修復して保持する

調査によって、組織のクラウド環境またはオンプレミス環境で攻撃者が管理制御を持っていることが明らかになった場合は、攻撃者が永続的でないことが確認できる方法で、制御を取り戻す必要があります。

このセクションでは、管理制御の回復計画を作成するときに考慮する必要がある方法と手順について説明します。

> [!IMPORTANT]
> 組織で必要な正確な手順は、調査で検出された永続性がどのようなものか、および調査が完了して考えられるすべての侵入方法と永続化方法が明らかになったということにどれほど自信があるかによって異なります。
>
> 行うすべての処置は、[クリーン ソース](/security/compass/privileged-access-access-model)から構築された、信頼できるデバイスから実行してください。 たとえば、新しい[特権アクセス ワークステーション](/security/compass/privileged-access-deployment)を使用します。
>

次のセクションには、管理制御を修復して保持するための次の種類の推奨事項が含まれます。

- 現在のサーバーでの信頼の削除
- SAML トークン署名証明書のローテーション、または必要に応じた ADFS サーバーの置換
- クラウド環境またはオンプレミス環境の固有の修復アクティビティ

### <a name="remove-trust-on-your-current-servers"></a>現在のサーバーでの信頼を削除する

トークン署名証明書またはフェデレーション信頼の制御が組織から失われた場合、最も確実な方法は、信頼を削除し、オンプレミスの修復中にクラウドマスター ID に切り替える方法です。

信頼を削除し、クラウドマスター ID に切り替えるには、慎重な計画と、ID の分離による事業運営への影響についての深い理解が必要です。 詳細については、「[オンプレミスの攻撃から Microsoft 365 を保護する](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md)」を参照してください。

### <a name="rotate-your-saml-token-signing-certificate"></a>SAML トークン署名証明書をローテーションする

組織が、オンプレミスの管理制御を取り戻す際に [信頼を削除](#remove-trust-on-your-current-servers) "*しない*" と決めた場合は、オンプレミスの管理制御を取り戻して、攻撃者が署名証明書にアクセスできないようにした後で、SAML トークン署名証明書をローテーションする必要があります。

トークン署名証明書を 1 回ローテーションしても、まだ前のトークン署名証明書を使用できます。 前の証明書の動作を引き続き許可するのは、通常の証明書ローテーションに組み込まれた機能です。これにより、証明書の有効期限が切れる前に、証明書利用者の信頼を更新するための猶予期間が組織に与えられます。

攻撃があった場合は、攻撃者がまったくアクセス権を保持できないようにする必要があります。 次の手順を使用して、攻撃者がドメインのトークンを偽造する能力を確実に保持できないようにします。

> [!CAUTION]
> この手順の最後のステップでは、電話機、現在の Web メール セッション、および関連付けられたトークンと更新トークンを使用しているその他のアイテムからユーザーをログアウトさせます。
>

> [!TIP]
> ADFS 環境でこれらの手順を実行すると、プライマリ証明書とセカンダリ証明書の両方が作成され、既定期間の 5 日後にはセカンダリ証明書が自動的にプライマリに昇格されます。
>
> 証明書利用者信頼がある場合、これによって最初の ADFS 環境の変更から 5 日後に影響が生じる可能性があります。これは計画の際に考慮する必要があります。 これは、プライマリ証明書を 3 回置換し、**Urgent** フラグを再び使用し、セカンダリ証明書を削除するか、自動証明書ローテーションをオフにすることでも解決できます。
>

**トークン署名証明書を完全にローテーションし、攻撃者による新しいトークン偽造を防ぐには**

1. **AutoCertificateRollover** パラメーターが **True** に設定されていることを確認します。

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    **AutoCertificateRollover** が **True** に設定されていない場合は、次のように値を設定します。

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. Microsoft オンライン サービスに接続します。

    ``` powershell
    Connect-MsolService
    ```

1. 次のコマンドを実行し、オンプレミスおよびクラウドのトークン署名証明書の拇印と有効期限をメモします。

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    次に例を示します。

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. **Urgent** スイッチを使用してプライマリ トークン署名証明書を置換します。 このコマンドでは、ADFS によってプライマリ証明書が直ちに置き換えられます。セカンダリ証明書にすることはありません。

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. **Urgent** スイッチなしで、セカンダリ トークン署名証明書を作成します。 このコマンドによって、Azure Cloud と同期する前に、2 つのオンプレミス トークン署名証明書が許可されます。

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. オンプレミスのプライマリ証明書とセカンダリ証明書両方を使用してクラウド環境を更新し、クラウドで公開されたトークン署名証明書を直ちに削除します。

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > この方法を使用してこの手順をで実行しない場合でも、古いトークン署名証明書でユーザーを認証できる可能性があります。

1. これらの手順が正しく実行されたことを確認するために、前の手順 3 で表示された証明書が削除されていることを確かめます。

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. 古いトークンへのアクセスを防ぐために、PowerShell を使用して更新トークンを取り消します。 

    詳細については、次を参照してください。

    - [Azure Active Directory でユーザー アクセスを取り消す](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell のドキュメント](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>ADFS サーバーを置き換える

[SAML トークン署名証明書のローテーション](#rotate-your-saml-token-signing-certificate)の代わりに、クリーン システムで ADFS サーバーを置き換えることにした場合は、既存の ADFS を環境から削除して新しいものを構築する必要があります。 

詳細については、「[構成の削除](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration)」をご覧ください。 

### <a name="cloud-remediation-activities"></a>クラウド修復アクティビティ

この記事で前に示した推奨事項に加え、クラウド環境に対しては次のアクティビティもお勧めします。

|アクティビティ  |説明  |
|---------|---------|
|**パスワードをリセットする**     |   すべての[ブレークグラス アカウント](../../active-directory/roles/security-emergency-access.md)のパスワードをリセットし、ブレークグラス アカウントの数を必要最小限に減らします。    |
|**特権アクセス アカウントを制限する**     |    特権アクセス権を持つサービス アカウントとユーザー アカウントがクラウド専用アカウントであることを確認します。Azure Active Directory と同期またはフェデレーションされているオンプレミス アカウントは使用しないでください。  |
|**MFA を適用する**     | テナント内のすべての管理者特権ユーザーに対して多要素認証 (MFA) を適用します。 テナント内のすべてのユーザーに MFA を適用にすることをお勧めします。       |
|**管理アクセス権を制限する**     |    [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) (PIM) および条件付きアクセスを実装して、管理アクセス権を制限します。  <br><br>Microsoft 365 ユーザーの場合は、[Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751) (PAM) を実装して、電子情報開示、グローバル管理者、アカウント管理など、機密性の高い機能へのアクセスを制限します。    |
|**委任されたアクセス許可と同意付与を確認/削減する**     |  次のいずれかの機能を許可する、すべてのエンタープライズ アプリケーションの委任されたアクセス許可または[同意付与](/graph/auth-limit-mailbox-access)を確認して削減します。 <br><br>- 特権ユーザーとロールの変更 <br>- 電子メールの読み取り、送信、またはすべてのメールボックスへのアクセス <br>- OneDrive、Teams、または SharePoint コンテンツへのアクセス <br>- ディレクトリの読み取り/書き込みができるサービス プリンシパルの追加 <br>- アプリケーションのアクセス許可と委任されたアクセス       |
|     |         |

### <a name="on-premises-remediation-activities"></a>オンプレミスの修復アクティビティ

この記事で前に示した推奨事項に加え、オンプレミス環境に対しては次のアクティビティもお勧めします。

|アクティビティ  |説明  |
|---------|---------|
|**影響を受けたシステムを再構築する**     |   攻撃者によって侵害されたことが調査の際に特定されたシステムを再構築します。      |
|**不要な管理者ユーザーを削除する**     |   ドメイン管理者、バックアップ オペレーター、およびエンタープライズ管理者のグループから不要なメンバーを削除します。 詳細については、「[特権アクセスの保護](/security/compass/overview)」を参照してください。 |
|**特権アカウントのパスワードをリセットする**     |  環境内のすべての特権アカウントのパスワードをリセットします。 <br><br>**注**: 特権アカウントは、組み込みグループだけではありません。サーバー管理、ワークステーション管理、または環境内の他の領域へのアクセスを委任されたグループも該当します。      |
|**krbtgt アカウントをリセットする**     | [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1) スクリプトを使用して **krbtgt** アカウントを 2 回リセットします。 <br><br>**注**: 読み取り専用ドメイン コントローラーを使用している場合は、読み取り/書き込みドメイン コントローラーと読み取り専用ドメイン コントローラーに対してスクリプトを別に実行する必要があります。        |
|**システムの再起動をスケジュールする**     |   攻撃者によって作成された永続化メカニズムが存在しないか、システムに残っていないことを検証した後で、メモリ常駐マルウェアの削除に役立つようにシステムの再起動をスケジュールします。 |
|**DSRM パスワードをリセットする**     |  各ドメイン コントローラーの DSRM (ディレクトリ サービス復元モード) パスワードを一意の複雑なパスワードにリセットします。       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>調査中に検出された永続化を修復またはブロックする

調査は反復的なプロセスです。異常を特定したら修復するという組織の要望と、修復によって攻撃者に検出を警戒させ、対応するための時間を与えるという可能性の間でバランスを取る必要があります。

たとえば、検出に気付いた攻撃者が、手法を変更したり、さらに多くの永続化を作成したりする可能性があります。

調査の初期の段階で特定した永続化手法はすべて必ず修復してください。

### <a name="remediate-user-and-service-account-access"></a>ユーザー アカウントとサービス アカウントのアクセスを修復する

上記で示した推奨処置の他に、ユーザー アカウントを修復して復元するために次の手順を検討することをお勧めします。

- **信頼できるデバイスに基づいて条件付きアクセスを適用します**。 可能であれば、組織の要件に合う "*場所ベースの条件付きアクセス*" を適用することをお勧めします。

- 侵害された可能性があるすべてのユーザー アカウントについて、削除後に **パスワードをリセットします**。 また、ディレクトリ内のすべてのアカウントの資格情報をリセットするための中期計画も実装してください。

- 資格情報のローテーションの直後に **更新トークンを取り消します**。

    詳細については、次を参照してください。

    - [Azure Active Directory で緊急時にユーザー アクセスを取り消す](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell のドキュメント](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)

## <a name="next-steps"></a>次のステップ

- 一番上のナビゲーション バーにある **[ヘルプ]** ( **?** ) ボタンを選択して、**Microsoft 製品内部の支援を利用します** (Microsoft 365 Defender ポータル、Microsoft 365 コンプライアンス センター、Office 365 セキュリティ/コンプライアンス センターなど)。

- **デプロイの支援については**、[FastTrack](https://fasttrack.microsoft.com) に問い合わせてください

- **製品サポート関連のニーズがある場合は**、[Microsoft サポート ケース](https://support.microsoft.com/contactus)を送信します。

    > [!IMPORTANT]
    > 侵害されたと考えられ、インシデント対応による支援が必要な場合には、**Sev A** Microsoft サポート ケースを開きます。
    >
