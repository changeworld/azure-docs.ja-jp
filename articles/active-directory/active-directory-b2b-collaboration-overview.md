<properties
   pageTitle="Azure Active Directory の B2B コラボレーション | Microsoft Azure"
   description="Azure Active Directory の B2B コラボレーションは、ビジネス パートナーが会社のアプリケーションにアクセスできるようにします。パートナーの各ユーザーは、1 つの Azure AD アカウントによって表されます。"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# Azure Active Directory の B2B コラボレーション

Azure Active Directory (Azure AD) の B2B コラボレーションは、パートナーによって管理されるユーザーが会社のアプリケーションにアクセスできるようにします。パートナー企業のユーザーを招待し、リソースにアクセスできるように認証することで、会社間の関係を作成できます。いったん各企業と Azure Active Directory をフェデレーションすると各ユーザーが 1 つの Azure AD アカウントで表されるようになるため、複雑さが軽減されます。ビジネス パートナーが Azure AD でアカウントを管理すると、パートナーのユーザーが組織を離れるとアクセス権が取り消されるほか、内部ディレクトリ内のメンバーシップを使用した意図しないアクセスが防止されるため、高いレベルのセキュリティが確保されます。B2B コラボレーションには、Azure AD をまだ保有していないビジネス パートナー向けに、Azure AD のアカウントをビジネス パートナーに提供するための合理化されたサインアップ エクスペリエンスが用意されています。

-   ビジネス パートナーはサインインに自分の資格情報を使用するため、あなたが外部パートナーのディレクトリを管理する必要はありません。またユーザーがパートナー組織から離れたときにアクセス権を削除する必要もありません。

-   アプリへのアクセスは、ビジネス パートナーのアカウントのライフサイクルとは無関係に管理します。つまり、ビジネス パートナーの IT 部門に処理を依頼する必要なくアクセスを取り消すことができます。

## 機能

B2B コラボレーションは、管理を簡略化し、SaaS アプリ (Office 365、Salesforce、Azure Services、およびすべてのモバイル、クラウド、およびオンプレミスの要求対応アプリケーション) を含む会社のリソースへのパートナーによるアクセスのセキュリティを向上させます。B2B コラボレーションでは、パートナーが独自のアカウントを管理する一方で、企業がパートナーのアクセスにセキュリティ ポリシーを適用することができます。

Azure Active Directory B2B コラボレーションは、構成が簡単で、あらゆる規模のパートナーが、電子メール検証プロセスを使用する独自の Azure Active Directory を持たない場合でも、簡単にサインアップできます。さらに、外部のディレクトリやパートナーごとのフェデレーション構成が必要ないため、容易に管理できます。

## B2B コラボレーションのプロセス

1. Azure AD B2B コラボレーションを利用する会社の管理者が、外部ユーザーを招待および承認するために、2,000 行以下のコンマ区切り値 (CSV) ファイルを B2B コラボレーション ポータルにアップロードします。

  ![CSV [ファイルのアップロード] ダイアログ](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. ポータルを介して、これらの外部ユーザーに電子メール招待状が送信されます。

3. 招待されたユーザーは、(Azure AD で管理される) Microsoft の既存の職場アカウントにサインインするか、または Azure AD で新しい職場アカウントを取得します。

4. サインインすると、ユーザーは、共有されているアプリにリダイレクトされます。

一般消費者向け電子メール アドレス (Gmail や [*comcast.net*](http://comcast.net/) など) への招待状は現在サポートされていません。

B2B コラボレーションのしくみについては、[このビデオ](http://aka.ms/aadshowb2b)を参照してください。

## 次のステップ
Azure AD B2B コラボレーションに関する他の記事をご覧ください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [動作のしくみ](active-directory-b2b-how-it-works.md)
- [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
- [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー トークンの形式](active-directory-b2b-references-external-user-token-format.md)
- [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)
- [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0824_2016-->