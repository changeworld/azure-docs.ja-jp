---
title: Azure AD を使って試用版を有効にする | Azure
description: アプリとサービスの公開元のために、Azure Marketplace と AppSource で Azure Active Directory (Azure AD) を使用して試用版の登録リストの種類を有効にします
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826063"
---
# <a name="enable-trial-using-azure-ad"></a>Azure AD を使って試用版を有効にする  
Azure Active Directory (Azure AD) はクラウド ID サービスであり、業界標準のフレームワークである OAuth および OpenID Connect を使って Microsoft 職場または学校アカウントでの認証を有効にします。  
*   Azure AD の詳細については、Azure Active Directory のページ ([azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory)) を参照してください。  

公開元と公開元の顧客は、Azure AD を使用して、Marketplace で認証されます。 公開元の顧客が Marketplace で試用版の登録リストを選択すると、試用版の環境にリダイレクトされます。  試用版の環境内では、追加のサインイン手順を必要とせずに、顧客を直接設定できます。 公開元のアプリやサービスは、認証中に、Azure AD からトークンを 受け取ります。このトークンには、アプリやサービスにユーザー アカウントを作成するための有益なユーザー情報が含まれています。 設定を自動化したり変換の可能性を高めたりできる場合があります。  
*   認証時に Azure AD から送信されるトークンの詳細については、「トークンのサンプル」セクション ([docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)) を参照してください

Azure AD を使って、アプリまたは試用版で 1 クリック認証を有効にします。  
*   Marketplace から試用版への顧客エクスペリエンスを簡素化する。  
*   ユーザーが Marketplace から公開元のドメインまたは試用環境にリダイレクトされる場合でも、製品内エクスペリエンスの感覚を維持する。  
*   余分なサインイン手順がないため、リダイレクト時に中止される可能性を低くする。  
*   大規模な Azure AD ユーザーの展開障壁を削減する。  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Marketplace での Azure AD の統合を検証する: マルチテナント アプリ  
Azure AD を使用して、ソリューションに対して次のオプションをサポートします。  
*   Marketplace のネットショップにアプリを登録する。  
*   Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。  
    *   アプリの登録の詳細については、「Azure Active Directory とアプリケーションの統合」([docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)) を参照してください。  

Azure AD フェデレーション シングル サインオン (SSO) を使用した経験がない場合は、次の手順に従います。  
1.  Marketplace にアプリを登録します。 
2.  OAuth 2.0 または OpenID Connect を使って、Azure AD で SSO を開発します。  
    *   OAuth 2.0 の詳細については、OAuth 2.0 に関するページ ([docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)) を参照してください。  
    *   OpenID Connect の詳細については、OpenID Connect に関するページ ([docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)) を参照してください。  
3.  Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを提供します。  
    *   AppSource 認定の詳細については、AppSource 認定に関するページ ([docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)) を参照してください。 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Marketplace での Azure AD の統合を検証する: シングルテナント アプリ  
シングルテナント ソリューションに対して、次のオプションのいずれかをサポートします。  
*   Azure AD B2B を使って、ユーザーを公開元のディレクトリにゲスト ユーザーとして追加する。  
    *   Azure AD B2B の詳細については、「Azure AD B2B コラボレーションとは」([docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)) を参照してください。
*   [お問い合わせ] を使用して、顧客用に試用版を手動で設定します。  
*   顧客ごとの "体験版" を開発する。  
*   SSO を使ってマルチテナント サンプル デモ アプリをビルドする。  

## <a name="next-steps"></a>次の手順
*   「[Azure Marketplace と AppSource のパブリッシャー ガイド](./marketplace-publishers-guide.md)」ページをご覧ください。  
 
---  

