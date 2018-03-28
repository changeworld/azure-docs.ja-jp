---
title: Azure Marketplace SaaS アプリケーションの技術的な発行ガイド
description: Azure Marketplace に SaaS アプリケーションを発行するためのステップ バイ ステップ ガイドと発行チェックリスト
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/28/2018
ms.author: pabutler
ms.openlocfilehash: 64becc80192e69bd332d6657637c845acf93748b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS アプリケーションの技術的な発行ガイド

Azure Marketplace SaaS アプリケーションの技術的な発行ガイドをご利用いただきありがとうございます。 このガイドは、発行希望者と既存のパブリッシャーが SaaS アプリケーション サービスを使って自分のアプリケーションとサービスを Azure Marketplace に一覧表示できるように設計されています。  
自身の Azure サブスクリプションでソリューションがデプロイされるとき、およびデザインしたインターフェイスを通じてお客様がログオンし、アプリケーションのテストを管理するときに、SaaS アプリケーション プランを使います。 これを行うには、[Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) を使って既存の評価環境を活用します。 つまり、お客様が主導し、パートナーがホストする無料試用版です。 クラウドの購入者にソリューションを無償で体験する機会を与えられるようにソリューションを公開することが重要なので、このプランの種類はお客様がクラウド ソリューションを検索する方法に一致する試用エクスペリエンスを提供します。  

他のすべての Marketplace サービスの概要については、[Marketplace Publisher ガイド](https://aka.ms/sellerguide)に関する記事をご覧ください。

## <a name="saas-application-technical-guidance"></a>SaaS アプリケーションの技術的なガイダンス
SaaS アプリケーションの技術面の要件はシンプルです。 パブリッシャーは、発行する Azure AD と統合することのみ必要です。  Azure AD とアプリケーションの統合は明確に文書化されており、Microsoft はこれを実現する複数の SDK とリソースを提供しています。  

まず、Azure Marketplace での発行専用のサブスクリプションを用意し、作業を他の計画から分離できるようにすることをお勧めします。 さらに、まだインストールしていない場合は、開発環境の一部として、次のツールを用意することをお勧めします。 
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)  
- [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-5.0.0)  
- [Azure 開発者ツール (使用できるものを確認)](https://azure.microsoft.com/tools/)  
- [Visual Studio Code](https://code.visualstudio.com/)  

### <a name="resources"></a>リソース
次の一覧は、開始するのに最適な Azure AD リソースへのリンクを提供しています。 

**ドキュメント**

- [Azure Active Directory 開発者ガイド](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

- [Azure Active Directory との統合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

- [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

- [Azure のロードマップ - セキュリティ + ID](https://azure.microsoft.com/roadmap/?category=security-identity)

**ビデオ**

- [Vittorio Bertocci による Azure Active Directory の認証](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

- [Azure Active Directory ID 技術の簡単な説明 - パート 1/2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

- [Azure Active Directory ID 技術の簡単な説明 - パート 2/2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

- [Microsoft Azure Active Directory でのアプリの構築](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

- [Active Directory に重点を置いた Microsoft Azure ビデオ](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

**トレーニング**  
- [IT プロ向けの Microsoft Azure コンテンツ シリーズ: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

**Azure Active Directory サービスの更新情報**  
- [Azure AD サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)

サポートについては、次のリソースを使用できます。
- [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
- [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="the-azure-active-directory-gallery"></a>Azure Active Directory ギャラリー
Azure Marketplace/AppSource に表示されているアプリケーションに加えて、Azure Marketplace AppStore の一部である Azure AD アプリケーション ギャラリーに表示されているアプリケーションもあります。 ID プロバイダーとして Azure AD を使うお客様は、ここで発行されているさまざまな SaaS アプリケーション コネクタを検索できます。 IT 管理者は、アプリ ギャラリーからコネクタを追加した後、シングル サインオン (SSO) およびプロビジョニング用にコネクタを構成して使用できます。 Azure AD は、SAML 2.0、OpenID Connect、OAuth、WS-Fed など、SSO 用の主要なフェデレーション プロトコルをすべてサポートします。  

アプリケーションの統合が Azure AD で動作することをテストした後は、アプリケーション ネットワーク ポータルへのアクセスを求める要求を送信します。 Office 365 アカウントがある場合は、それを使ってポータルにサインインします。 Office 365 アカウントがない場合は、Microsoft アカウント (Outlook、Hotmail など) を使ってサインインできます。

## <a name="program-benefits"></a>プログラム特典
- 考えられる最良のシングル サインオン エクスペリエンスを顧客に提供します
- アプリケーションの構成をシンプルかつ最小限にします
- 顧客がアプリケーションをギャラリーで検索して見つけることができます
- すべての顧客が、使用する Azure AD SKU (Free、Basic、Premium) に関係なく、この統合を使用できます
- 共通の顧客向けの詳しい構成手順チュートリアルがあります
- SCIM を使っている場合、同じアプリのユーザー プロビジョニングを有効にできます

## <a name="prerequisites"></a>前提条件
Azure AD ギャラリー内のアプリケーションの一覧を表示するには、まず、アプリケーションに Azure AD でサポートされているフェデレーション プロトコルのいずれかを実装する必要があります。 「[Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal/)」にある Azure AD アプリケーション ギャラリーの使用条件をお読みください。  

使っているプロトコルに応じた追加の前提条件を次に示します。

**OpenID Connect** - Azure AD でマルチ テナント アプリケーションを作成し、アプリケーションの同意フレームワークを実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受信したテナント ID とユーザーの UPN に基づいて顧客のユーザー アクセスを制御できます。  
**SAML 2.0 または WS-Fed** - アプリケーションに、SP または IDP モードで SAML または WS-Fed SSO 統合を行う機能が必要です。
