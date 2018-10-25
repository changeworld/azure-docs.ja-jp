---
title: Azure Active Directory (Azure AD) とは | Microsoft Docs
description: Azure Active Directory を使用して、既存のオンプレミス ID をクラウドに拡張したり、Azure AD 統合アプリを開発する方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 406baeac60c7c0cdf5f74876e5fc29ea23d3d6f6
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957549"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory とは
Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。 Azure AD は、コア ディレクトリ サービス、アプリケーション アクセス管理、および ID 保護を 1 つのソリューションに統合し、開発者が一元的なポリシーとルールに基づいてアプリにアクセス制御を追加することができる標準ベースのプラットフォームを用意しています。

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Azure AD のメリット
Azure AD は、以下の場合に役立ちます。

-   エンタープライズ全体でユーザーごとに 1 つの ID を作成して管理し、ユーザー、グループ、デバイスが [Azure AD Connect](../connect/active-directory-aadconnect.md) と同期された状態を維持する。

-   何千もの事前統合された SaaS アプリを含むアプリにシングル サインオン アクセスを提供したり、[Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)を使用してオンプレミス SaaS アプリケーションへのセキュリティで保護されたリモート アクセスを提供したりする。

-   ルール ベースの [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) ポリシーをオンプレミス アプリケーションとクラウド アプリの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする。

-   [セルフサービスによるパスワードのリセット](../user-help/user-help-reset-password.md)、[MyApps ポータル](../user-help/active-directory-saas-access-panel-introduction.md)を使用したグループおよびアプリケーション アクセス要求によって、ユーザーの生産性を向上させる。

-   世界規模でエンタープライズ レベルでクラウド ベースの ID およびアクセス管理ソリューションの[高可用性および信頼性](https://docs.microsoft.com/azure/architecture/checklist/availability)の利点を活用する。

## <a name="who-uses-azure-ad"></a>Azure AD の利用者
Azure AD は、IT 管理者、アプリケーション開発者、Office 365 ユーザー、Azure ユーザー、Dynamics CRM Online ユーザーを対象としています。

- **IT 管理者。** Azure AD では、強化された ID 管理と、何千もの[クラウド ベースの SaaS アプリ](../saas-apps/tutorial-list.md)およびオンプレミスのアプリへのシングル サインオン (SSO) アクセスを使用して、より安全なソリューションが組織に提供されます。 これらのアプリを通じて、クラウドベース アプリのセキュリティ、シームレスなアクセス、強化されたコラボレーション、ユーザーの ID ライフサイクルの自動化も実現できるため、セキュリティとコンプライアンスの両方の向上に役立ちます。

    さらに、[Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) を使用すると、Azure AD を既存の Windows Server Active Directory と統合できるので、組織は既存のオンプレミス ID への投資を利用して、クラウドベースの SaaS アプリへのアクセスを管理できます。

- **アプリ開発者にとって。** Azure AD を使用することで、世界中の数多くの組織が使用する ID 管理ソリューションと統合できるため、アプリケーションの構築に集中できるようになります。

- **Office 365、Azure、または Dynamics CRM Online のお客様にとって。** お客様は Azure AD を既にお使いです。 Office 365、Azure、Dynamics CRM Online の各テナントは、実際には Azure AD のテナントであるため、統合されたクラウド アプリへのユーザー アクセスの管理をすぐに開始できます。

## <a name="how-reliable-is-azure-ad"></a>Azure AD の信頼性
Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。 Azure AD は、世界中の 28 か所のデータ センターから自動フェールオーバーで実行されます。 つまり、データ センターがダウンした場合でも、ディレクトリ データのコピーは少なくとも 2 つのリージョンに分散したデータセンターに存在しているので、すぐにアクセスできます。

サービス レベル アグリーメントの詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="choose-an-edition"></a>エディションの選択
すべての Microsoft Online ビジネス サービスは、サインオンや他の ID のニーズに対応するために、Azure AD に依存しています。 Microsoft Online ビジネス サービス (Office 365、Microsoft Azure など) にサブスクライブすると、自動的に Azure AD を入手し、Free エディションのすべての機能を利用できるようになります。 Azure Active Directory Free エディションでは、ユーザーとグループの管理、オンプレミスのディレクトリとの同期、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリ間でのシングル サインオンを実現することができます。 

Azure AD の実装を強化するために、Azure Active Directory Basic、Premium P1、または Premium P2 エディションにアップグレードして、有料機能を追加することもできます。 Azure AD の有料エディションは、既存の無料のディレクトリ上に構築されます。セルフサービス、拡張された監視機能、セキュリティ レポート、Multi-Factor Authentication (MFA)、セキュリティで保護されたモバイル ユーザーのアクセスなどエンタープライズ クラスの機能を提供します。

> [!NOTE]
> これらのエディションの価格オプションについては、「 [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。 Azure Active Directory Premium P1、Premium P2、Azure Active Directory Basic は、現在、中国ではサポートされていません。 Azure AD の価格の詳細については、Azure Active Directory フォーラムにお問い合わせください。

- **Azure Active Directory Basic。** このエディションは、クラウド優先のニーズを持つタスク ワーカー向けであり、クラウド中心のアプリケーション アクセスおよびセルフサービス ID 管理のソリューションを提供します。 Basic エディションでは、グループベースのアクセス管理、クラウド アプリ向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure AD を使用してオンプレミス Web アプリを発行するため) などの生産性の強化とコスト削減の機能が提供され、すべてがアップタイム 99.9% のエンタープライズの SLA によって保証されます。

- **Azure Active Directory Premium P1。** Azure Active Directory Premium Edition は、より要求の厳しい ID およびアクセスの管理を必要とする組織を支援することを目的としており、機能豊富なエンタープライズレベルの ID 管理機能が追加され、ハイブリッド ユーザーがオンプレミスの機能とクラウドの機能にシームレスにアクセスできるようになっています。 このエディションには、クラウド内でのアプリケーション アクセス、セルフサービスの ID、アクセス管理 (IAM)、ID 保護、およびセキュリティに関して、ハイブリッド環境のインフォメーション ワーカーと ID 管理者が必要とするすべてが含まれています。 また、動的なグループやセルフサービス グループ管理のような高度な管理と委任のリソースをサポートします。 さらに、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート) が含まれており、オンプレミス ユーザー向けのセルフ サービスのパスワード リセットなどのソリューションを実現するクラウドの書き戻し機能を提供します。

- **Azure Active Directory Premium P2。** この新しいエディションは、ユーザーと管理者を対象とした高度な保護機能を中心に設計され、Azure AD Premium P1 の全機能に加え、Identity Protection と Privileged Identity Management が含まれています。 Azure Active Directory Identity Protection では、何十億ものシグナルを活用して、アプリや会社の重要なデータへのリスク ベースの条件付きアクセスを提供します。 また、Azure Active Directory Privileged Identity Management では、特権アカウントを管理および保護できるので、管理者と管理者によるリソースへのアクセスを検出、制限、監視し、必要に応じて Just-In-Time アクセスを提供できます。  

> [!NOTE]
> さまざまな Azure Active Directory の機能は "従量課金制" のエディションでも使用できます。<ul><li>**Azure Active Directory B2C。** コンシューマー向けアプリの ID およびアクセス管理ソリューション。 詳細については、[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) に関するページを参照してください。</li><li>**Azure Multi-Factor Authentication。** ユーザーごと、またはアプリケーション プロバイダーごとに使用されます。 詳細については、[Azure Multi-Factor Authentication の概要](../authentication/multi-factor-authentication.md)に関するページを参照してください。

## <a name="as-an-admin-how-do-i-get-started"></a>管理者の開始方法
30 日間の無料試用版にサインアップし、最初のクラウド ソリューションをデプロイし、[Azure Active Directory Premium の試用版](https://azure.microsoft.com/trial/get-started-active-directory/)に関するページを参照してください。

## <a name="as-a-developer-how-do-i-get-started"></a>開発者の開始方法
30 日間の無料試用版にサインアップし、アプリと Azure AD を統合し、[Azure Active Directory Premium の試用版](https://azure.microsoft.com/trial/get-started-active-directory/)に関するページを参照してください。 詳細については、Azure Active Directory の[開発者ガイド](../develop/v1-overview.md)のページも参照してください。

## <a name="next-steps"></a>次の手順
- [Azure の ID およびアクセス管理の基礎](identity-fundamentals.md)。

- [Azure AD を Windows Server Active Directory と統合する](../hybrid/how-to-connect-install-express.md)。
