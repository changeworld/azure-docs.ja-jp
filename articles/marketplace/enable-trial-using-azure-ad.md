---
title: Azure Active Directory を使って Azure Marketplace の試用版を有効にする | Azure
description: アプリとサービスの公開元のために、Azure Marketplace と AppSource で Azure Active Directory を使用して試用版の登録リストの種類を有効にします。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160469"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Azure Active Directory を使って試用版の登録リストを有効にする

Azure Active Directory (Azure AD) はクラウド ID サービスであり、業界標準のフレームワークを使って Microsoft 職場または学校アカウントでの認証を有効にします。 Azure AD は、OAuth と OpenID Connect の認証をサポートします。 [Azure Marketplace](https://azuremarketplace.microsoft.com) では、公開元とその顧客が Azure AD を使って認証されます。

Azure AD の詳細については、「[Azure Active Directory](https://azure.microsoft.com/services/active-directory)」を参照してください。

顧客が Marketplace で試用版の登録リストを選択すると、試用版の環境にリダイレクトされます。 試用版の環境内では、追加のサインイン手順を必要とせずに、顧客を直接設定できます。 公開元のアプリやプランは、認証時に、Azure AD からトークンを 受け取ります。 このトークンには、アプリやプランのユーザー アカウントを作成するために用いられる有益なユーザー情報が含まれています。 顧客のセットアップを自動化したりコンバージョン率を高めたりすることができます。

認証時に Azure AD から送信されるトークンの詳細については、「[トークンのサンプル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)」を参照してください。

Azure AD を使って、アプリまたは試用版で 1 クリック認証を有効にします。 Azure AD には、次のような利点があります。 
*   Marketplace から試用版への顧客エクスペリエンスを簡素化する。
*   ユーザーが Marketplace から公開元のドメインまたは試用環境にリダイレクトされる場合でも、製品内エクスペリエンスの感覚を維持する。
*   余分なサインイン手順がないため、リダイレクト時に中止される可能性を低くする。
*   大規模な Azure AD ユーザーの展開障壁を削減する。

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Marketplace での Azure AD の統合を検証する: マルチテナント アプリ
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Marketplace での Azure AD の統合を検証する: シングルテナント アプリ
シングルテナント ソリューションで、Azure AD を使って、次のオプションのいずれかをサポートします。 
*   Azure Active Directory B2B (Azure AD B2B) を使って、ユーザーを公開元のディレクトリにゲスト ユーザーとして追加する。
    
    Azure AD B2B の詳細については、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」を参照してください。
*   [お問い合わせ] の公開オプションを使用して、顧客用に試用版を手動で設定する。
*   顧客ごとの "体験版" を開発する。
*   SSO を使ったマルチテナント サンプル デモ アプリを構築する。

## <a name="next-steps"></a>次の手順
*   「[Azure Marketplace と AppSource のパブリッシャー ガイド](./marketplace-publishers-guide.md)」をご覧ください。
