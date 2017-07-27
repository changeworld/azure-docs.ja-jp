---
title: "Azure AD 電子メール保護の EMS と Office 365 サービスの説明 | Microsoft Docs"
description: "電子メール ポリシーと構成の適用に関する EMS および Office 365 Microsoft での推奨事項について説明します。"
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 05/24/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 26c708608cab54f2a97173bcaf38f7a2c6d6d6a0
ms.contentlocale: ja-jp
ms.lasthandoff: 06/07/2017

---

# <a name="ems-and-office-365-service-descriptions"></a>EMS と Office 365 のサービスの説明

この記事では、明示された推奨設定での EMS と Office 365 サービスの活用についての概要と、Azure の電子メール保護機能を理解するために必要となる主な概念について説明します。 これらの機能では、Microsoft クラウド エンタープライズ管理者が会社の従業員の ID とデバイスを保護できるようにすると共に、会社データ自体へのアクセス (転送時および保存された状態の両方) も制御します。

## <a name="services"></a>サービス 

このドキュメントで参照するサービスを以下の表に示します。
 
|サービス|Description|
|-------|-----------|
|[Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)|Azure AD には、多要素認証、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、ロール ベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査とセキュリティの監視、アラートなど、一連の ID 管理機能が用意されています。|
|[Azure AD Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection)|このサービスを使用すると、組織の ID に影響する潜在的な脆弱性を検出し、サインイン リスクとユーザー リスクを低、中、高で示す条件付きアクセス ポリシーを使用して、自動応答を構成できます。|
|[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|このサービスにより、組織は、特権が必要な処理に対して永続アクセス権を持つユーザー数を最小限に抑えることができます。Azure AD Privileged Identity Management では、管理者候補の概念を導入しています。 管理者候補とは、常にではなく時折特権アクセスを必要とするユーザーのことです。 このロールは、このユーザーがアクセス権を必要とするまで非アクティブ化されています。そして、ユーザーがアクティブ化プロセスを完了すると、所定の時間の間だけ有効な管理者になります。|
|[Azure Information Protection](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)| Azure Information Protection は EMS E5 サービス内容の一部として配信されているクラウド ベース ソリューションであり、組織によるドキュメントや電子メールの分類、ラベル付け、および保護を支援します。 ルールおよび条件を定義する管理者は自動で、ユーザーまたはユーザーが推奨設定を指定されている組み合わせの下では手動で、これを実行できます。 ドキュメントや電子メールに分類を適用するには、Azure Information Protection のラベルを使用します。 これを行うと、データを保存する場所や共有する相手に関係なく、分類は常に識別可能です。 <br><br>Azure Information Protection のポリシー設定は、[Azure Rights Management](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms) によって保護されます。 ラベルの適用と同様に、Rights Management を使って適用された保護は、場所 (組織、ネットワーク、ファイル サーバー、およびアプリケーションの内外) に関係なくドキュメントや文書で保持されます。|
|[Microsoft Intune](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune は、会社データの保護を維持しながら生産性を向上できるクラウド ベースの Enterprise Mobility Management (EMM) サービスです。 Intune は ID およびアクセス制御のために Azure AD と緊密に統合されており、デバイスとアプリケーションの管理に使用されます。 [Intune のデバイス管理](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies)機能は、Windows PC を含め、ユーザーのデバイスを構成して保護するために使用されます。 <br><br>Intune のデバイス管理機能では、ユーザーが個人用の電話、タブレット、または PC を登録できる [Bring Your Own Device (BYOD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/enable-byod) 登録と、自動登録、共有デバイス、または事前承認済みの登録の要件設定などの管理シナリオを可能にする[会社所有デバイス (COD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/issue-corp-devices) 登録の両方をサポートしています。 追加のセキュリティの場合、デバイスを登録するために MFA が必要になります。 管理に登録されると、会社のリソースへの安全なアクセスを可能にするデバイス機能および設定を Intune で構成できるようになります。|


## <a name="ems-concepts"></a>EMS の概念
知っておきたい主な概念と EMS 機能について、以下の表に示します。

|主な概念|Description|
|------------|-----------|
|[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)|Microsoft の 2 段階認証ソリューションとして、Azure MFA はシンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを保護できます。 電話、テキスト メッセージ、モバイル アプリによる確認など、一連の照合方法を通じて確実な認証を行うことができます。|
|[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)|この Azure AD の機能は、特定の条件に基づいて環境内のクラウド アプリへのアクセスに対してコントロールを適用できるようにします。 コントロールを使用して、アクセスに要件を追加するか、アクセスをブロックできます。 条件付きアクセスの実装は、ポリシーに基づいています。|
|[Exchange Online のデータ損失防止 (DLP)](https://support.office.com/article/Overview-of-data-loss-prevention-policies-1966b2a7-d1e2-4d92-ab61-42efbb137f5e)|Exchange Online プラン 2 および Office 365 サブスクリプションのプレミアム機能として利用可能な Exchange Online のデータ損失防止 (DLP) ポリシーは、組織が Office 365 の機密情報を特定、監視し、自動的に保護することを可能にします。<br><br>Exchange Online DLP ポリシーによって、Exchange Online、SharePoint Online、および OneDrive for Business などさまざまな場所にある機密情報を特定できます。 たとえば、これらのポリシーを利用して、機密情報を含むドキュメントを特定し、機密情報を組織外の人物と誤って共有することを防止できます。|
|[Exchange メール フロー / トランスポート ルール](https://support.office.com/en-US/article/Define-mail-flow-rules-to-encrypt-or-decrypt-email-messages-9B7DAF19-D5F2-415B-BC43-A0F5F4A585E8)|Exchange メール フロー ルール (トランスポート ルールとも呼ばれる) は、組織を通過するメッセージ内で特定の条件を検出し、それらのメッセージを処理します。 メール フロー ルールは、多くの電子メール クライアントで使用できる受信トレイのルールに似ています。 メール フロー ルールと Outlook などのクライアント アプリケーションで設定するルールの主な違いは、メール フロー ルールでは転送中のメッセージを処理するのに対して、アプリケーションで設定するルールではメッセージが配信された後に処理する点です。 メール フロー ルールには豊富な条件、例外、動作の一式が含まれており、さまざまな種類のメッセージング ポリシーを実装する柔軟性を提供しています。|
|[Intune モバイル デバイス管理](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune は、モバイル オペレーティング システムで利用できるプロトコルまたは API を使って、モバイル デバイス管理 (MDM) を提供します。 Intune には、デバイスを登録する (会社サービスにアクセスしているデバイスの一覧を保持しているため)、会社のセキュリティと正常性の標準に適合するようにデバイスを構成する、会社サービスにアクセスするための証明書と Wi-Fi/VPN プロファイルを提供する、会社標準へのデバイスの準拠を報告および評価する、管理対象デバイスから会社データを削除するなどのタスクが含まれます。|
|[Intune アプリ保護ポリシー](https://docs.microsoft.com/intune/deploy-use/protect-app-data-using-mobile-app-management-policies-with-microsoft-intune)|Intune アプリ保護ポリシーは、デバイスが管理に登録されているかに関係なく、モバイル アプリ内の会社のデータを保護するために使用できます。 実際、ユーザーのモバイル デバイスは、[Intune で Office 365 情報を保護しながら、Microsoft MDM ソリューション以外のツールで管理することもできます ](https://docs.microsoft.com/enterprise-mobility-security/solutions/protect-company-data-without-managing-devices)。 従業員の生産性を確保しながら、故意および事故によるデータの損失を防止できます。 アプリレベルのポリシーを実装することで、会社リソースへのアクセスを制限し、IT 部門のコントロール内でデータを保持できます。|
|[Azure AD トークンの有効期間](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)|Azure Active Directory (Azure AD) によって発行されたトークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。|
|Microsoft Identity ブローカー|Microsoft は、すべてのモバイル プラットフォーム用に、さまざまなベンダーのアプリケーション間で資格情報をブリッジできるようにするアプリケーションを提供しています。このアプリケーションでは、資格情報を検証する安全な単一の場所を必要とする、特別な拡張機能を利用できます。 これらをブローカーといいます。 iOS および Android では、これらのブローカーは、Microsoft Authenticator アプリと Intune ポータル サイト アプリ経由で提供されます。 Windows 10 では、この機能はオペレーティング システムに組み込まれたアカウント選択によって提供されており、技術的には Web 認証ブローカーとして知られています。|

