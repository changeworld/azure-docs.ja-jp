---
title: Azure Active Directory を使用して Microsoft AppSource と Azure Marketplace のリストを有効にする | Azure
description: アプリとサービスの公開元のために、Azure Marketplace と AppSource で Azure Active Directory を使用して登録リストの種類を有効にします。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 324f8def5ddafb15156a31fe5addabadcee6f115
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160615"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Azure Active Directory を使用して AppSource と Marketplace のリストを有効にする

 Azure Active Directory (Azure AD) はクラウド ID サービスであり、Microsoft アカウントでの認証を有効にします。 Azure AD では、業界標準のフレームワークを使用します。 [Azure Active Directory の詳細を確認します](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD の利点

Microsoft AppSource と Azure Marketplace のお客様は、製品内エクスペリエンスを利用して一覧表カタログを検索します。 これらのアクションでは、お客様が製品にサインインする必要があります。 Azure AD 統合には次のような利点があります。

- より迅速なエンゲージメントと最適化されたカスタマー エクスペリエンス
- 数百万単位のエンタープライズ ユーザーのシングル サインオン (SSO)
- さまざまなパートナーが発行しているアプリケーションで一貫性のあるサインイン体験
- モバイル アプリとクラウド アプリのための拡張可能なクロスプラットフォーム認証

## <a name="offers-that-require-azure-ad"></a>Azure AD を必要とするオファー

AppSource と Azure Marketplace 用のさまざまな[オプションとオファーの種類の一覧](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)には、Azure AD の実装に対してそれぞれ異なる要件があります。 詳しくは、以下の表をご覧ください。

| **オファリング タイプ**    | **Azure AD の SSO が必要か?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | お問い合わせ | 試用版 | 体験版 | トランザクション |
| 仮想マシン | 該当なし | いいえ | いいえ | いいえ |
| Azure アプリ (ソリューション テンプレート)  | 該当なし | 該当なし | 該当なし | 該当なし |
| マネージド アプリ  | 該当なし | 該当なし | 該当なし | いいえ |
| SaaS  | いいえ | はい | はい | はい |
| Containers  | 該当なし | 該当なし | 該当なし | いいえ |
| コンサルティング サービス  | いいえ | 該当なし | 該当なし | 該当なし |

SaaS 技術要件に関する詳細については、「[SaaS アプリケーションのオファー発行ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)」を参照してください。

## <a name="azure-ad-integration"></a>Azure AD の統合

- 一覧への Azure AD の統合によってシングル サインオンを有効にする方法については、[開発者向けの Azure Active Directory]( https://docs.microsoft.com/azure/active-directory/develop/) に関する記事をご覧ください。
- Azure AD シングル サインオンについて詳しくは、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」をご覧ください。

## <a name="enable-a-trial-listing"></a>試用版の一覧を有効にする

自動化された顧客のセットアップにより、コンバージョン率を高めることができます。 顧客が試用版の一覧を選択し、試用版の環境にリダイレクトされた場合、追加のサインイン手順を必要とせずに、顧客を直接設定できます。

認証時に、Azure AD からアプリまたはオファーにトークンが送信されます。 トークンによって提供されるユーザー情報により、アプリまたはオファー内にユーザー アカウントを作成できます。 詳しくは、[サンプル トークン](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)に関する記事をご覧ください。

Azure AD を使って、アプリまたは試用版一覧でワンクリック認証を有効にした場合、次のことが行われます。

- Marketplace から試用版一覧へのカスタマー エクスペリエンスを簡素化する。
- ユーザーが Marketplace から公開元のドメインまたは試用環境にリダイレクトされる場合でも、製品内エクスペリエンスの感覚を維持する。
- 余分なサインイン手順がないため、ユーザーがリダイレクトされるときに中止される可能性を低くする。
- 大規模な Azure AD ユーザーの展開障壁を削減する。

## <a name="verify-azure-ad-integration"></a>Azure AD の統合の確認

### <a name="multitenant-solutions"></a>マルチテナント ソリューション

Azure AD を使用して、次のアクションをサポートします。

- Marketplace のネットショップの 1 つにアプリを登録する。 詳しくは、[アプリの登録](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)または [AppSource 認定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)に関する記事をご覧ください。
- Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを取得する。

Azure AD フェデレーション シングル サインオンを使用した経験がない場合は、次の手順に従います。

1. Marketplace にアプリを登録します。
1. [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) または [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) を使って、Azure AD で SSO を開発します。
1. Azure AD でマルチテナント サポート機能を有効にして、ワンクリックの試用版エクスペリエンスを提供します。

### <a name="single-tenant-solutions"></a>シングル テナント ソリューション

Azure AD を使用して、次のアクションの 1 つをサポートします。

- [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) を使って、ディレクトリにゲスト ユーザーを追加する。
- **[お問い合わせ]** の公開オプションを使用して、顧客用に試用版を手動で設定する。
- 顧客ごとの "体験版" を開発する。
- SSO を使用するマルチテナント サンプル デモ アプリを構築する。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、 

- マーケットプレースについて[学習](https://azuremarketplace.microsoft.com/sell)します。

パートナー センターに登録するには、新しいプランの作成を開始するか、既存のものを操作します。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)して、プランを作成するか完成させます。
