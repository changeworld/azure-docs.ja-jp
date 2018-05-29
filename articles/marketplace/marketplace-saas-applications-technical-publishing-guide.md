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
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 48b0b4177dad6262105bf30be2b8714f6ea1228f
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076712"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS アプリケーションの技術的な発行ガイド

Azure Marketplace SaaS アプリケーションの技術的な発行ガイドをご利用いただきありがとうございます。 このガイドは、発行希望者と既存のパブリッシャーが SaaS アプリケーション サービスを使って自分のアプリケーションとサービスを Azure Marketplace に一覧表示できるように設計されています。 

SaaS オファーを発行する方法についての理解をより深められるように、このガイドは次のセクションに分かれています。
* オファーの概要
* ビジネス要件
* 技術的な要件
* 発行プロセス
* Azure Active Directory を使用して試用を有効にする
* Marketplace に対する Azure AD 統合の認定

## <a name="offer-overview"></a>オファーの概要  

SaaS アプリケーションは、両方の Azure ネットショップで入手できます。次の表では、現在利用できるオプションについて説明しています。

| ネットショップのオプション | 登録 | 試用版/取引 |  
| --- | --- | --- |  
| AppSource | あり (お問い合わせ) | あり (PowerBI/Dynamics) |
| Azure Marketplace | なし | あり (SaaS アプリ) |   

**登録:** 発行の登録オプションは、お問い合わせのオファー タイプから構成され、試用版レベルまたは取引レベルの参加を実現できない場合に使用されます。 この方法の利点は、市場にソリューションのあるパブリッシャーはすぐに取引に転換し、ビジネスを向上できるリードの受け取りを始められることです。  
**試用版/取引:** 顧客はソリューションを直接購入するか、試用版を要求することができます。 試用版エクスペリエンスを提供することで、顧客に提供されるエンゲージメント レベルが高くなり、顧客は購入前にソリューションを調べることができます。 試用版エクスペリエンスでは、ネットショップでのプロモーションの機会が多くなり、顧客エンゲージメントからより多くの有望なリードを期待できます。 試用は、少なくとも試用期間中は無料サポートを含む必要があります。  

| SaaS アプリのオファー | ビジネス要件 | 技術的な要件 |  
| --- | --- | --- |  
| **お問い合わせ** | あり | なし |  
| **PowerBI/Dynamics** | あり | あり (Azure AD の統合) |  
| **SaaS アプリ**| あり | あり (Azure AD の統合) |     

Marketplace ネットショップの詳細と各発行オプションの説明については、[Marketplace のパブリッシャー ガイド](https://aka.ms/sellerguide)と[発行のオプション](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option)に関するページを参照してください。

## <a name="business-requirements"></a>ビジネス要件
SaaS オファーのビジネス要件は、テクノロジ要件と並行して満たすことができます。 ビジネス要件と情報のほとんどは、Cloud パートナー ポータルで SaaS オファーを作成するときに収集されます。 ビジネス要件は次のとおりです。 
* 参加ポリシーに同意する
* Microsoft との統合 
* オファーの対象ユーザーを特定する
* 使用するリード管理を定義し、決定する
* プライバシー ポリシーと使用条件を設定する
* サポートの連絡先を定義する  

詳細については、「[Marketplace での発行に関する前提条件](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)」を参照してください。

## <a name="technical-requirements"></a>技術的な要件

SaaS アプリケーションの技術面の要件はシンプルです。 パブリッシャーは、発行する Azure Active Directory (Azure AD) と統合することのみ必要です。 Azure AD とアプリケーションの統合は明確に文書化されており、Microsoft はこれを実現する複数の SDK とリソースを提供しています。  

まず、Azure Marketplace での発行専用のサブスクリプションを用意し、作業を他の計画から分離できるようにすることをお勧めします。 これが完了すると、このサブスクリプションで SaaS アプリケーションを展開し、開発作業を開始することができます。  

最適な Azure Active Directory のドキュメント、サンプル、およびガイダンスについては、以下のサイトを参照してください。 

* [Azure Active Directory 開発者ガイド](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

* [Azure Active Directory との統合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

* [Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure のロードマップ - セキュリティ + ID](https://azure.microsoft.com/roadmap/?category=security-identity)

ビデオ チュートリアルについては、以下をご覧ください。

* [Vittorio Bertocci による Azure Active Directory の認証](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 技術の簡単な説明 - パート 1/2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 技術の簡単な説明 - パート 2/2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Microsoft Azure Active Directory でのアプリの構築](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directory に重点を置いた Microsoft Azure ビデオ](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

無料の Azure Active Directory トレーニングは以下で利用できます。  
* [IT プロ向けの Microsoft Azure コンテンツ シリーズ: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

さらに、Azure Active Directory は、サービス更新プログラムを確認するサイトを提供しています   
* [Azure AD サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)

サポートについては、次のリソースを使用できます。
* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>発行プロセス

SaaS の発行プロセスには、技術的な手順とビジネス上の手順があります。  Azure Active Directory の開発と統合を行うほとんどの作業は、オファーのビジネス要件を満たすために必要な作業と並行して実行できます。 ビジネス要件の大部分は、SaaS アプリ オファーの構成の一部である Cloud パートナー ポータルです。  
次の図は、試用版/取引オファーの主な発行手順を示しています。  

![SaaS の発行手順](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

次の表は、各主要な発行手順を示しています。  

| 発行手順 | [説明] |   
| --- | --- |  
| **SaaS アプリケーションを作成する** | Cloud パートナー ポータルにログインし、**[新規]** を選択し、**[SaaS アプリ]** オファーを選択します。 |  
| **Azure AD との統合を作成する** | 前のセクションで説明したテクノロジ要件に従って、SaaS オファーと Azure AD を統合します。 |  
| **オファー設定を指定する**| すべての SaaS オファーの初期情報を入力します。 使用するオファー ID とオファー名。 |     
| **技術情報を設定する** | オファーに関する技術情報を入力します。 SaaS アプリケーションの場合、ソリューションの URI とオファーの種類の取得ボタン (無料、トレイル、またはお問い合わせ) が必要です。 |  
| **体験版 (省略可能)** | これは省略可能な種類の試用版で、主に他の種類の Marketplace オファーの場合に必要です。 パブリッシャーのサブスクリプションまたはエンド ユーザーに試用版を展開することができます。 |  
| **オファーのネットショップ素材を設定する**| このセクションで、パブリッシャーはロゴ、Marketplace 素材、法的文書をリンクしてアップロードし、リード管理システムを構成します。 |
| **オファーの連絡先を設定する** | SaaS オファーのエンジニアリングの連絡先とサポートの連絡先について両方の情報を入力します。 |  
| **SaaS App Azure AD の統合を確認する** | 発行する SaaS アプリを送信する前に、アプリが Azure AD と統合されていることを確認する必要があります |  
| **オファーを発行する**| オファーとテクノロジ資産が完成したら、オファーを送信することができます。 これにより、発行プロセスが開始されます。このプロセスでは、ソリューション テンプレートの発行のテスト、検証、認定、承認が行われます。 |

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory を使用して試用を有効にする  

Microsoft はすべての Marketplace ユーザーを Azure AD で認証するので、認証されたユーザーが Marketplace で試用リストをクリックし、試用環境にリダイレクトされた場合、それ以上のサインイン ステップを必要とせずに、ユーザーを直接試用にプロビジョニングできます。 認証の間にアプリが Azure AD から受け取るトークンには、アプリでユーザー アカウントを作成するために使うことができる重要なユーザー情報が含まれているので、プロビジョニング エクスペリエンスを自動化し、変換の可能性を高くすることができます。 トークンの詳細については、[サンプル トークン](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)に関するページを参照してください。

Azure AD を使ってアプリまたは試用版の 1 クリック認証を有効にすると、次のことが可能になります。  
* Marketplace から試用版への顧客エクスペリエンスを簡素化する。  
* ユーザーが 'Marketplace からパブリッシャーのドメインまたは試用環境にリダイレクトされる場合でも、"製品内エクスペリエンス" の感覚を維持する。  
* 余分なサインイン手順がないため、リダイレクトで中止される可能性を低くする。  
* 大規模な Azure AD ユーザーの展開障壁を削減する。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Marketplace に対する Azure AD 統合の認定  

アプリケーションがシングル テナントかマルチテナントか、Azure AD フェデレーショ シングル サインオン (SSO) を初めて使用するか、既にサポートしているかに応じて、Azure AD の統合を認定することができます。  

**マルチテナント アプリケーションの場合:**  

既に Azure AD をサポートしている場合は、以下を実行します。
1.  Azure Portal でアプリケーションを登録します
2.  Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。 詳細については、[こちら](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)を参照してください。  

Azure AD フェデレーション SSO が初めての場合は、以下を実行します。 
1.  Azure Portal でアプリケーションを登録します
2.  [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) または [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code) を使って、Azure AD で SSO を開発します。
3.  AAD のマルチテナントのサポート機能を有効にして、ワンクリック試用環境を用意します。詳細情報については、[こちら](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified)を参照してください。  

**シングルテナントアプリケーションの場合は、以下のいずれかのオプションを使用してください。**  
* [Azure B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) を使用して、ユーザーをゲスト ユーザーとしてディレクトリに追加します
* "お問い合わせ" を使用して、顧客用に試用版を手動でプロビジョニングします
* 顧客ごとの "体験版" を開発します
* SSO を使ってマルチテナント サンプル デモ アプリをビルドします

