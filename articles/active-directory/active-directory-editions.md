<properties
	pageTitle="Azure Active Directory のエディション | Microsoft Azure"
	description="Azure Active Directory の無料と有料のエディションの選択肢について説明するトピックです。Azure Active Directory Basic は無料のエディションで、Azure Active Directory Premium は有料のエディションです。"
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markvi"/>

# Azure Active Directory のエディション

Microsoft のオンライン ビジネス サービスはすべて、サインオンやその他の ID のニーズを Azure Active Directory に依存しています。Microsoft のオンライン ビジネス サービス (Office 365、Microsoft Azure など) をサブスクライブすると、Azure Active Directory (Azure AD) と、以下で説明する Free エディションのすべての機能へのアクセス許可が与えられます。


Azure Active Directory は、従業員、パートナー、顧客向けにクラウドで包括的な ID およびアクセス管理機能を提供するサービスです。このサービスには、開発者向けにディレクトリ サービス、高度な ID 管理、標準に準拠した機能豊富なプラットフォーム、および独自のアプリケーションや事前に統合された多数のアプリケーションに対応したアプリケーション アクセス管理が集約されています。Azure Active Directory Free エディションでは、ユーザーとグループの管理、オンプレミスのディレクトリとの同期、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリケーション間でのシングル サインオンを実現することができます。Azure Active Directory の詳細については、「[Azure AD とは](active-directory-whatis.md)」を参照してください。



Azure Active Directory を強化するには、Azure Active Directory Basic エディションおよび Premium エディションを使用することで有料の機能を追加できます。Azure Active Directory の有料エディションは、既存の無料のディレクトリ上に構築されます。セルフサービス、拡張された監視機能、セキュリティ レポート、Multi-Factor Authentication (MFA)、セキュリティで保護されたモバイル ユーザーのアクセスなどエンタープライズ クラスの機能を提供します。

Office 365 サブスクリプションには、下の比較表に記載されているその他の Azure Active Directory 機能が含まれます。


> [AZURE.NOTE] これらのエディションの価格オプションについては、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。<br>Azure Active Directory Premium と Azure Active Directory Basic は、現在、中国ではサポートされていません。詳細については、Azure Active Directory フォーラムからお問い合わせください。


- **Azure Active Directory Basic**: このエディションは、クラウド優先のニーズを持つタスク ワーカー向けに設計されており、クラウド中心のアプリケーション アクセスおよびセルフサービス ID 管理のソリューションを提供します。Azure Active Directory Basic エディションでは、グループベースのアクセス管理、クラウド アプリケーション向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure Active Directory を使用してオンプレミス Web アプリケーションを発行するため) などの生産性の強化とコスト削減の機能が提供され、すべてがアップタイム 99.9% のエンタープライズレベルの SLA によって保証されます。
 
- **Azure Active Directory Premium**: Azure Active Directory Premium エディションは、より要求の厳しい ID およびアクセスの管理を必要とする組織を支援することを目的として、機能豊富なエンタープライズレベルの ID 管理機能が追加され、ハイブリッド ユーザーがオンプレミスの機能とクラウドの機能にシームレスにアクセスできるようになっています。このエディションには、クラウド内のアプリケーション アクセス、セルフサービスの ID とアクセスの管理 (IAM)、ID 保護とセキュリティに関して、ハイブリッド環境のインフォメーション ワーカーと ID 管理者が必要とするすべてが含まれています。また、動的なグループやセルフサービス グループ管理のような高度な管理と委任のリソースをサポートします。さらに、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート) が含まれており、オンプレミス ユーザー向けのセルフ サービスのパスワード リセットなどのソリューションを実現するクラウドの書き戻し機能を提供します。

すぐに Active Directory Premium にサインアップして使用する方法については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。


> [AZURE.NOTE] 
さまざまな Azure Active Directory の機能を "従量課金制" のエディションで使用できます。
>
>- Active Directory B2C は、コンシューマー向けアプリケーションの ID およびアクセスの管理ソリューションです。詳しくは、「[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)」を参照してください。
 
>-	Azure Multi-Factor Authentication は、ユーザーごとまたは認証プロバイダーごとに使用できます。詳細については、「[Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)」を参照してください。


##一般公開機能の比較

> [AZURE.NOTE] このデータの異なるビューについては、「[マイクロソフト クラウド プラットフォームの全体像](https://www.microsoft.com/server-cloud/products/azure-active-directory/Features.aspx)」を参照してください。

| | Azure AD Free | Azure AD Basic | Azure AD Premium |
| ---                      | :-:           | :-:            | :-:              |
| 共通機能 | ![○][12] | ![○][12] | ![○][12] |
| Basic の機能 | | ![○][12] | ![○][12] |
| Premium の機能 | | | ![○][12] |



<br>

**共通機能**

- [ディレクトリ オブジェクト](#directory-objects) 

- [ユーザー/グループの管理 (追加/更新/削除)/ユーザー ベースのプロビジョニング、デバイス登録](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [シングル サインオン (SSO)](#single-sign-on-sso)

- [クラウド ユーザーに対するセルフサービスのパスワード変更](#self-service-password-change-for-cloud-users)

- [接続 (オンプレミスのディレクトリを Azure Active Directory に拡張する同期エンジン)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [セキュリティ/使用状況レポート](#securityusage-reports)



**Basic の機能**

- [グループ ベースのアクセス管理/プロビジョニング](#group-based-access-managementprovisioning)

- [クラウド ユーザーに対するセルフサービスのパスワード リセット](#self-service-password-reset-for-cloud-users)

- [企業ブランド (ログオン ページ/アクセス パネルのカスタマイズ)](#company-branding-logon-pagesaccess-panel-customization)

- [アプリケーション プロキシ](#application-proxy)

- [SLA 99.9%](#sla-999)


**Premium の機能**

- [セルフサービスによるグループとアプリの管理/セルフサービスによるアプリケーションの追加/動的なグループ](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [セルフサービスによるパスワードのリセット、変更、ロック解除 (オンプレミスの書き戻しが可能)](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Multi-Factor Authentication (クラウドおよびオンプレミス (MFA サーバー))](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [MIM CAL + MIM サーバー](#mim-cal-mim-server)

- [Cloud App Discovery](#cloud-app-discovery)

- [Connect Health](#connect-health)

- [グループ アカウントの自動パスワード ロールオーバー](#automatic-password-rollover-for-group-accounts)


**Azure Active Directory Join – Windows 10 のみの関連機能**

- [Azure AD へのデバイスの参加、デスクトップ SSO、Azure AD 用の Microsoft Passport、管理者による Bitlocker 回復](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [MDM の自動登録、セルフサービスによる Bitlocker 回復、Azure AD Join による Windows 10 デバイスへのローカル管理者の追加](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)




<br> <hr>
## 共通機能
#### ディレクトリ オブジェクト 

**種類:** 共通機能

既定の使用量クォータは 150,000 オブジェクトです。1 個のオブジェクトは、一意の識別名によって表される、ディレクトリ サービス内の 1 つのエントリです。オブジェクトの例としては、認証目的に使用されるユーザー エントリなどがあります。この既定のクォータを超えるオブジェクト数が必要な場合は、サポートにお問い合わせください。Office 365、Microsoft Intune、および Azure Active Directory をディレクトリ サービスの基盤とするその他の有料 Microsoft オンライン サービスには 500,000 オブジェクト制限は適用されません。


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| 最大で 500,000 のオブジェクト| オブジェクトの制限なし| オブジェクトの制限なし| Office 365 ユーザー アカウントにはオブジェクトの制限なし|


<br>

#### ユーザー/グループの管理 (追加/更新/削除)/ユーザー ベースのプロビジョニング、デバイス登録

**種類:** 共通機能

**可用性:**


| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| ![○][12]| ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [Azure AD ディレクトリの管理](active-directory-administer.md)
- [Azure Active Directory Device Registration の概要](active-directory-conditional-access-device-registration-overview.md)


<br> <hr>
#### シングル サインオン (SSO)

**種類:** 共通機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| 10 個のアプリ (ユーザーあたり) [1] <br>(事前統合済みの SaaS アプリおよび開発者が統合したアプリ)| 10 個のアプリ (ユーザーあたり) [1] <br>(Free レベル + アプリケーション プロキシ アプリ) | 制限なし [2] <br> (Free、Basic レベル + セルフサービスのアプリ統合テンプレート)| 10 個のアプリ (ユーザーあたり) [1] <br> (事前統合済みの SaaS アプリおよび開発者が統合したアプリ)|

[1] Azure AD Free と Azure AD Basic で、SaaS アプリへのアクセス権が割り当てられているエンド ユーザーは、アクセス パネルで最大 10 個のアプリを表示でき、アプリへの SSO アクセスを取得できます。管理者は SSO を構成して、Free と Basic のユーザーに対して、SaaS アプリへのアクセス権を何個でも割り当てることができますが、エンド ユーザーはアクセス パネルで一度に 10 個のアプリしか表示できません。

[2] アプリケーション ギャラリーのメニューに用意されているテンプレートを使用して、SAML、SCIM、またはフォーム ベース認証をサポートする任意のアプリケーションのセルフサービスによる統合。詳細については、「[Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](active-directory-saas-custom-apps.md)」を参照してください。

**詳細:**

- [Azure Active Directory (AD) を使ったアプリケーションの管理](active-directory-enable-sso-scenario.md)

<br> <hr>
#### クラウド ユーザーに対するセルフサービスのパスワード変更

**種類:** 共通機能

**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| ![○][12]| ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [自分のパスワードを更新する方法](active-directory-passwords-update-your-own-password.md)


<br> <hr>
#### 接続 (オンプレミスのディレクトリを Azure Active Directory に拡張する同期エンジン) 

**種類:** 共通機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| ![○][12]| ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<br> <hr>
#### セキュリティ/使用量レポート

**種類:** 共通機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| 3 個の基本レポート| 3 個の基本レポート| 詳細レポート| 3 個の基本レポート|

**詳細:**

- [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)

<br> <hr>

## Premium および Basic の機能
#### グループ ベースのアクセス管理/プロビジョニング

**種類:** Basic の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | ![○][12]| ![○][12]| |

**詳細:**

- [SaaS アプリケーションへのアクセスをグループで管理する](active-directory-accessmanagement-group-saasapps.md)

<br> <hr>
#### クラウド ユーザーに対するセルフサービスのパスワード リセット

**種類:** Basic の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [ユーザーおよび管理者の Azure AD のパスワード リセット](active-directory-passwords.md)

<br> <hr>
#### 企業ブランド (ログオン ページ/アクセス パネルのカスタマイズ)

**種類:** Basic の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)

<br> <hr>
#### アプリケーション プロキシ

**種類:** Basic の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | ![○][12]| ![○][12]| |

**詳細:**

- [オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)

<br> <hr>
#### SLA 99.9%

**種類:** Basic の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | ![○][12]| ![○][12]| ![○][12]|

**詳細:**

- [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)

<br> <hr>

## Premium の機能
#### セルフサービスによるグループとアプリの管理/セルフサービスによるアプリケーションの追加/動的なグループ

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |



<br>
         
#### セルフサービスによるパスワードのリセット、変更、ロック解除 (オンプレミスの書き戻しが可能)

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |



<br>

#### Multi-Factor Authentication (クラウドおよびオンプレミス (MFA サーバー))

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| Office 365 アプリ用のクラウドのみに限定|

**詳細:**

- [Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)

<br> <hr>
#### MIM CAL + MIM サーバー 

Microsoft Identity Manager サーバー ソフトウェアの使用権は、Windows Server ライセンス (任意のエディション) と共に付与されます。Microsoft Identity Manager は、Windows Server OS で実行されるため、サーバーで Windows Server の有効なライセンス コピーが実行されている限り、そのサーバー上に Microsoft Identity Manager をインストールでき、使用することができます。Microsoft Identity Manager サーバーに他の個別のライセンスは不要です。

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |



<br>

#### Cloud App Discovery 

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |

**詳細:**

- [管理されていないクラウド アプリケーションを Cloud App Discovery で検出する](active-directory-cloudappdiscovery-whatis.md)

<br> <hr>
#### Connect Health

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |

**詳細:**

- [クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](active-directory-aadconnect-health.md)

<br> <hr>
#### グループ アカウントの自動パスワード ロールオーバー

**種類:** Premium の機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |


<br> <hr>
## Azure Active Directory Join – Windows 10 のみの関連機能
#### Azure AD へのデバイスの参加、デスクトップ SSO、Azure AD 用の Microsoft Passport、管理者による Bitlocker 回復

**種類:** Azure Active Directory Join – Windows 10 のみの関連機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| ![○][12]| ![○][12]| ![○][12]| ![○][12]|


<br>

#### MDM の自動登録、セルフサービスによる Bitlocker 回復、Azure AD Join による Windows 10 デバイスへのローカル管理者の追加

**種類:** Azure Active Directory Join – Windows 10 のみの関連機能


**可用性:**

| Free エディション| Basic エディション| Premium Edition| Office 365 アプリのみ |
| :-: | :-: | :-: | :-: |
| | | ![○][12]| |

<hr>


## Azure AD プレビュー機能
Azure AD には、Free、Basic、Premium エディションの一般提供されている機能に加えて、一連のプレビュー機能も用意されています。プレビュー機能を使用すると、近い将来に公開予定の機能を体験し、これらの機能が環境の改善に役立つかどうかを判断できます。

**使用可能なプレビュー機能**

- [B2B コラボレーション](active-directory-b2b-collaboration-overview.md)
- 条件付きアクセス
- [管理単位](active-directory-administrative-units-management.md)
- Privileged Identity Management
- [HR アプリケーションの統合](active-directory-saas-workday-inbound-tutorial.md)





## 参照トピック

- [Azure Active Directory Premium の概要](active-directory-get-started-premium.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_0218_2016-->