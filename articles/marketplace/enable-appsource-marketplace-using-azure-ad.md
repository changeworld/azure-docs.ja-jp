---
title: Azure Active Directory を使用して Microsoft AppSource と Azure Marketplace のリストを有効にする | Azure
description: アプリとサービスの公開元のために、Azure Marketplace と AppSource で Azure Active Directory を使用して登録リストの種類を有効にします。
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987343"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Azure Active Directory を使用して Microsoft AppSource と Azure Marketplace のリストを有効にする

Microsoft Azure Active Directory (Azure AD) はクラウド ID サービスであり、業界標準のフレームワークを使って Microsoft アカウントでの認証を有効にします。  Azure AD の詳細については、「[Azure Active Directory](https://azure.microsoft.com/services/active-directory)」を参照してください。

## <a name="benefits-of-using-azure-active-directory"></a>Azure Active Directory を使用することの利点

Microsoft AppSource と Azure Marketplace のお客様は、製品内エクスペリエンスを利用して一覧表カタログを検索します。それには、製品にサインインする必要があります。  アプリケーションと Azure AD を統合することで、エンゲージメントをはかどらせ、カスタマー エクスペリエンスを最適化します。 Azure AD:

- 数百万単位のユーザーのシングル サインオン (SSO) を有効にします。
- さまざまなパートナーが発行しているアプリケーションで一貫性のあるユーザー サインオン体験を可能にします。
- モバイル アプリとクラウド アプリのために拡張可能なクロスプラットフォーム認証を提供します。

下のセクションにある説明のように、Azure AD を実装して Marketplace に発行するには、特定のオファーが必須となります。

## <a name="azure-active-directory-requirements"></a>Azure Active Directory の要件

Microsoft AppSource と Azure Marketplace にはさまざまな[リスト オプションとオファリング タイプ](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)があります。  以下では、これらのリスト オプションとオファリング タイプに対する Azure AD 要件をまとめています。

| **オファリング タイプ**    | **AAD SSO は必須か?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 連絡する | 試用版 | 体験版 | トランザクション |
| 仮想マシン | 該当なし | いいえ  | いいえ  | いいえ  |
| Azure アプリ (ソリューション テンプレート)  | 該当なし | 該当なし | 該当なし | 該当なし |
| マネージド アプリ  | 該当なし | 該当なし | 該当なし | いいえ  |
| SaaS  | いいえ  | 可能  | はい | [はい] |
| Containers  | 該当なし | 該当なし | 該当なし | いいえ  |
| コンサルティング サービス  | いいえ  | 該当なし | 該当なし | 該当なし |

SaaS 技術要件に関する詳細については、「[SaaS アプリケーションのオファー発行ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)」を参照してください。

## <a name="integration-with-azure-active-directory"></a>Azure Active Directory との統合

Azure AD と統合し、SSO を有効にする方法については、「 https://aka.ms/aaddev」を参照してください。

Azure AD SSO の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」をご覧ください。

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Azure Active Directory を使って試用版の登録リストを有効にする

顧客が Marketplace で試用版の登録リストを選択すると、試用版の環境にリダイレクトされます。 試用版の環境内では、追加のサインイン手順を必要とせずに、顧客を直接設定できます。 公開元のアプリやプランは、認証時に、Azure AD からトークンを 受け取ります。 このトークンには、アプリやプランのユーザー アカウントを作成するために用いられる有益なユーザー情報が含まれています。 顧客のセットアップを自動化したりコンバージョン率を高めたりすることができます。

認証時に Azure AD から送信されるトークンの詳細については、「[トークンのサンプル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)」を参照してください。

Azure AD を使って、アプリまたは試用版で 1 クリック認証を有効にします。 Azure AD には、次のような利点があります。 
*   Marketplace から試用版への顧客エクスペリエンスを簡素化する。
*   ユーザーが Marketplace から公開元のドメインまたは試用環境にリダイレクトされる場合でも、製品内エクスペリエンスの感覚を維持する。
*   余分なサインイン手順がないため、リダイレクト時に中止される可能性を低くする。
*   大規模な Azure AD ユーザーの展開障壁を削減する。

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Marketplace での Azure AD の統合を検証する: マルチテナント アプリ
Azure AD を使用して、ソリューションに対して次のオプションをサポートします。
*   Marketplace のネットショップにアプリを登録する。
*   Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。

アプリの登録の詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」をご覧ください。

Azure AD フェデレーション シングル サインオン (SSO) を使用した経験がない場合は、次の手順に従います。
1.  Marketplace にアプリを登録します。 
2.  OAuth 2.0 または OpenID Connect を使って、Azure AD で SSO を開発します。
    *   OAuth 2.0 の詳細については、[OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) に関するページを参照してください。
    *   Open ID Connect の詳細については、[OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) に関するページを参照してください。
3.  Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを提供します。
    
    AppSource 認定の詳細については、[AppSource 認定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)に関するページを参照してください。 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Marketplace での Azure AD の統合を検証する: シングルテナント アプリ
シングルテナント ソリューションで、Azure AD を使って、次のオプションのいずれかをサポートします。 
*   Azure Active Directory B2B (Azure AD B2B) を使って、ユーザーを公開元のディレクトリにゲスト ユーザーとして追加する。 Azure AD B2B の詳細については、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」を参照してください。
*   [お問い合わせ] の公開オプションを使用して、顧客用に試用版を手動で設定する。
*   顧客ごとの "体験版" を開発する。
*   SSO を使用するマルチテナント サンプル デモ アプリを構築する。

## <a name="next-steps"></a>次の手順

まだ行っていない場合は、 
- マーケットプレースに[登録](https://azuremarketplace.microsoft.com/sell)します。

登録済みかつ新しいオファーを作成しているまたは既存のオファーを操作している場合は、
- [Cloud パートナー ポータルにログイン](https://cloudpartner.azure.com/)して、オファーを作成または完成させます。

