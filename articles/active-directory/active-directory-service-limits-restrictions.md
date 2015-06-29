<properties 
	pageTitle="Azure AD サービスの制限と制約" 
	description="Azure Active Directory サービスの使用上の制約およびその他のサービスの制限" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD サービスの制限と制約

ここでは、Azure Active Directory サービスの使用上の制約およびその他のサービスの制限を説明します。Microsoft Azure サービスの制限すべてをご覧になりたい場合は、「[Azure サブスクリプションとサービスの制限、クォータ、および制約](../azure-subscription-service-limits.md)」を参照してください。

## ディレクトリ

1 人のユーザーに関連付けできるのは最大 20 個の Azure Active Directory ディレクトリです。次のような場合に、この制限に達する可能性があります。

- 1 人のユーザーが 20 個のディレクトリを作成する。
- 1 人のユーザーがメンバーとして 20 個のディレクトリに追加される。
- 1 人のユーザーが 10 個のディレクトリを作成し、後で他のユーザーによって別の 10 個のディレクトリに追加される。

## オブジェクト

- Azure Active Directory Premium、Azure Active Directory Basic、Enterprise Mobility Suite、Office 365、Microsoft Intune、またはディレクトリ サービスに Azure Active Directory を利用しているその他の Microsoft オンライン サービスのサブスクライバーには制限がありません。
- Free エディションの Azure Active Directory では、1 つのディレクトリで最大 500,000 個のオブジェクトを使用できます。
- 管理者以外のユーザーは、最大 250 個のオブジェクトを作成できます。

##スキーマの拡張機能

現在、"文字列" 型または "バイナリ" 型の単一値の属性を使用して "User"、"Group"、"TenantDetail"、"Device"、"Application"、および "ServicePrincipal" エンティティを拡張できます。この場合、次のような制限があります。

- 文字列型の拡張の最大文字数は 256 文字です。
- バイナリ型の拡張の最大バイト数は 256 バイトです。
- 1 つのオブジェクトに対して、(すべての型とすべてのアプリケーションで合計) 100 個の拡張値を書き込むことができます。
- スキーマ拡張は、Graph API-version 1.21-preview でのみ使用できます。拡張を登録するには、アプリケーションに書き込みアクセス権を付与する必要があります。

## アプリケーション

1 つのアプリケーションに対して、最大 10 ユーザーが所有者になれます。

## グループ 

- 最大 10 人のユーザーが 1 つのグループの所有者になれます。
- 1 つの Azure Active Directory グループのメンバーとして登録できるオブジェクト数に制限はありません。


> [AZURE.NOTE]
> 
オンプレミスの Active Directory から Azure Active Directory に同期できるオブジェクトの数には制限があります。DirSync を使用している場合、制限は 15,000 ユーザーです。 Azure AD Connect を使用している場合、制限は 50,000 ユーザーです。

## アクセス パネル

- Azure AD Premium、Azure AD Basic、またはエンタープライズ モビリティ スイートのサブスクライバーの場合、1 エンド ユーザーあたり、アクセス パネルに表示できるアプリケーション数に制限はありません。
- Azure Active Directory の Free エディションでは、最大 10 個の統合済み SaaS アプリ (例: Box、Salesforce、または Dropbox) を各エンド ユーザーのアクセス パネルに表示できます。所属組織が開発した複数アプリケーションが、後で Azure Active Directory に統合されている場合は、エンド ユーザーのアクセス パネルに 10 個を超えるアプリケーションが表示されることがあります。この制限は、管理者アカウントには適用されません。

## レポート

いずれのレポートでも、最大 1,000 行を表示またはダウンロードできます。それを超えるデータは切り捨てられます。

## 参照トピック
- [Azure への組織としてのサインアップ](sign-up-organization.md)
- [Azure サブスクリプションを Azure AD に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
- [Azure AD の用語](active-directory-terminology.md)


 

<!---HONumber=58_postMigration-->