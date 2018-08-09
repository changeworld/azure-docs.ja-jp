---
title: Azure Active Directory (Azure AD) とは | Microsoft Docs
description: Azure Active Directory を使用すると、既存のオンプレミス ID をクラウドに拡張したり、Azure AD 統合アプリケーションを開発したりすることができます。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449996"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory とは
Azure Active Directory (Azure AD) は、Microsoft の、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスで、中核となるディレクトリ サービス、アプリケーションのアクセス管理機能、ID の保護機能が 1 つのソリューションに統合されています。 また、Azure AD は、リッチな標準ベースのプラットフォームとなっており、開発者は、一元化されたポリシーとルールを基に、開発したアプリケーションへのアクセス制御を行うことができます。

![Azure AD Connect Stack](./media/active-directory-whatis/Azure_Active_Directory.png)

- **IT 管理者にとって。** Azure AD では、強化された ID 管理と、何千もの[クラウド ベースの SaaS アプリ](../saas-apps/tutorial-list.md)およびオンプレミスのアプリへのシングル サインオン (SSO) アクセスを使用して、より安全なソリューションが組織に提供されます。 これらのアプリを通じて、クラウドベース アプリのセキュリティ、シームレスなアクセス、強化されたコラボレーション、従業員の ID ライフサイクルの自動化も実現できるため、セキュリティとコンプライアンスの両方の向上に役立ちます。

    さらに、わずか [4 回のクリック](./../connect/active-directory-aadconnect-get-started-express.md)で Azure AD を既存の Windows Server Active Directory と統合できるので、組織は既存のオンプレミス ID への投資を利用して、クラウドベースの SaaS アプリへのアクセスを管理できます。

- **アプリ開発者にとって。** Azure AD を使用することによって、世界中の数多くの組織が使用する ID 管理ソリューションと統合できるため、アプリケーションの構築に集中できるようになります。

- **Office 365、Azure、または Dynamics CRM Online のお客様にとって。** お客様は Azure AD を既にお使いです。 Office 365、Azure、Dynamics CRM Online の各テナントは、実際には Azure AD のテナントであるため、統合されたクラウド アプリへの従業員アクセスの管理をすぐに開始できます。

## <a name="how-reliable-is-azure-ad"></a>Azure AD の信頼性
Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。 Azure AD は、フェールオーバーが自動化された世界 28 か所のデータ センターから実行されているため、Azure AD の信頼性が高く、データ センターがダウンした場合でも、地理的に分散した 2 か所以上のデータ センターでディレクトリ データのデータが有効で、すぐにアクセスできるという安心感が得られます。

サービス レベル アグリーメントの詳細については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="choose-an-edition"></a>エディションの選択
すべての Microsoft Online ビジネス サービスは、サインオンや他の ID のニーズに対応するために、Azure Active Directory (Azure AD) に依存しています。 Microsoft Online ビジネス サービス (Office 365、Microsoft Azure など) にサブスクライブすると、Azure AD を入手することができ、Free エディションのすべての機能を利用できます。 Azure Active Directory Free エディションでは、ユーザーとグループの管理、オンプレミスのディレクトリとの同期、Azure、Office 365、および Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox などの多くの人気のある SaaS アプリケーション間でのシングル サインオンを実現することができます。 

Azure Active Directory を強化するには、Azure Active Directory Basic、Premium P1、Premium P2 の各エディションを使用して有料の機能を追加します。 Azure Active Directory の有料エディションは、既存の無料のディレクトリ上に構築されます。セルフサービス、拡張された監視機能、セキュリティ レポート、Multi-Factor Authentication (MFA)、セキュリティで保護されたモバイル ユーザーのアクセスなどエンタープライズ クラスの機能を提供します。

> [!NOTE]
> これらのエディションの価格オプションについては、「 [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。 Azure Active Directory Premium P1、Premium P2、Azure Active Directory Basic は、現在、中国ではサポートされていません。 Azure AD の価格の詳細については、Azure Active Directory フォーラムにお問い合わせください。
>

* **Azure Active Directory Basic** : このエディションは、クラウド優先のニーズを持つタスク ワーカー向けに設計されており、クラウド中心のアプリケーション アクセスおよびセルフサービス ID 管理のソリューションを提供します。 Azure Active Directory Basic エディションでは、グループベースのアクセス管理、クラウド アプリケーション向けのセルフサービスのパスワード リセット、Azure Active Directory アプリケーション プロキシ (Azure Active Directory を使用してオンプレミス Web アプリケーションを発行するため) などの生産性の強化とコスト削減の機能が提供され、すべてがアップタイム 99.9% のエンタープライズレベルの SLA によって保証されます。
* **Azure Active Directory Premium P1** : Azure Active Directory Premium エディションは、より要求の厳しい ID とアクセスの管理を必要とする組織を支援することを目的として、機能豊富なエンタープライズレベルの ID 管理機能を追加し、ハイブリッド ユーザーがオンプレミスの機能とクラウドの機能にシームレスにアクセスできるようにします。 このエディションには、クラウド内でのアプリケーション アクセス、セルフサービスの ID、アクセス管理 (IAM)、ID 保護、およびセキュリティに関して、ハイブリッド環境のインフォメーション ワーカーと ID 管理者が必要とするすべてが含まれています。 また、動的なグループやセルフサービス グループ管理のような高度な管理と委任のリソースをサポートします。 さらに、Microsoft Identity Manager (オンプレミスの ID およびアクセス管理スイート) が含まれており、オンプレミス ユーザー向けのセルフ サービスのパスワード リセットなどのソリューションを実現するクラウドの書き戻し機能を提供します。
* 
  **Azure Active Directory Premium P2** - すべてのユーザーと管理者を対象とした高度な保護機能を備えたこの新しいエディションには、Azure AD Premium P1 の全機能に加え、Identity Protection と Privileged Identity Management が含まれています。 Azure Active Directory Identity Protection では、何十億ものシグナルを活用して、アプリケーションや会社の重要なデータへのリスク ベースの条件付きアクセスを提供します。 また、Azure Active Directory Privileged Identity Management では、特権アカウントを管理および保護できるので、管理者と管理者によるリソースへのアクセスを検出、制限、監視し、必要に応じて Just-In-Time アクセスを提供できます。  

> [!NOTE]
> さまざまな Azure Active Directory の機能を "従量課金制" のエディションで使用できます。
>
> * Active Directory B2C は、コンシューマー向けアプリケーションの ID およびアクセスの管理ソリューションです。 詳細については、[Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/) に関するページをご覧ください
> * Azure Multi-Factor Authentication は、ユーザーごとまたは認証プロバイダーごとに使用できます。 詳細については、「[Azure Multi-Factor Authentication とは](../authentication/multi-factor-authentication.md)」を参照してください
>

## <a name="how-can-i-get-started"></a>利用を始めるには?

**IT 管理者の方:**

* [実際に使ってみてください。](https://azure.microsoft.com/trial/get-started-active-directory/) - このリンクを使って 30 日間無料試用版に今すぐサインアップすると、初めてのクラウド ソリューションを 5 分程度でデプロイできます

* [Azure AD の概要](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)に関するページで、Azure AD テナントをすばやく稼働させるためのヒントとテクニックをお読みください。

**開発者の方:**
 
* Azure Active Directory の[開発者ガイド](../develop/azure-ad-developers-guide.md)に関するページを確認してください

* [試用版の開始](https://azure.microsoft.com/trial/get-started-active-directory/) – 30 日の無料試用版に今すぐサインアップして、Azure AD とアプリの統合を開始してください

## <a name="next-steps"></a>次の手順
[Azure の ID およびアクセス管理の基礎](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
