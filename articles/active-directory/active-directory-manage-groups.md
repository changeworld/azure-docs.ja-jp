<properties 
	pageTitle="Azure AD でのグループの管理" 
	description="Azure AD でグループを管理する方法について説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD でのグループの管理

グループは、1 つの単位として管理できるユーザーとグループのコレクションです。特定のグループに属しているユーザーとグループは、グループのメンバーと呼ばれます。グループを使用すると、多数のアカウントに一度に共通のアクセス許可と権限のセットを割り当てることができるので、管理を簡素化できます。アカウントごとに個別にアクセス許可と権限を割り当てる必要はありません。

現在の Azure AD では、作成できるのはセキュリティ グループだけです。

同じアプリケーションに多くのユーザーを割り当てる必要がある場合、ユーザーにアプリケーションへのアクセスを割り当てる便利な方法としてグループを使用できます。また、リソースへのアクセスを制御する他のオンライン サービス (SharePoint Online など) のアクセス管理を構成するときにも、グループを使用できます。

ディレクトリの同期を構成してある場合は、ローカルなオンプレミスの Windows Server Active Directory から同期されているグループが表示される場合があります。このようなグループでは、[ソース ディレクトリ] プロパティの値が [ローカル Active Directory] になっています。このようなグループは、引き続きローカル Active Directory で管理する必要があります。これらのグループを Azure 管理ポータルで管理または削除することはできません。

Office 365 を使用している場合は、Office 365 の Exchange 管理センターで作成および管理された配布グループおよびメール対応セキュリティ グループが表示されることがあります。このようなグループは、[ソース ディレクトリ] プロパティの値が [Office 365] になっており、引き続き Exchange 管理センターで管理する必要があります。

アクセス パネルで統合グループを作成することもできます。[構成] タブの [グループ管理] で、**[ユーザーは O365 グループを作成できます]** ウィジェットを **[はい]** に設定します。アクセス パネルまたは Office 365 で作成された Office 365 統合グループは、[ソース ディレクトリ] プロパティが [Azure Active Directory] に設定されており、アクセス パネルを使用して管理できます。

ユーザーに対してセルフ サービスのグループ管理を有効にしてある場合 (詳細については、「Azure AD でのユーザーのセルフ サービス グループ管理」を参照)、テナント管理者は Azure 管理ポータルでこれらのグループを管理することもできます。グループのメンバーの追加と削除、グループの所有者の追加と削除、グループのプロパティの編集、グループの履歴アクティビティ レポートの表示などを行うことができます。履歴アクティビティ レポートには、グループ内で実行されたアクション、アクションを実行したユーザー、実行時刻などが表示されます。

> [AZURE.NOTE]アプリケーションにグループを割り当てるには、Azure AD Premium を使用する必要があります。Azure AD Premium では、グループを使用して、Azure AD と統合された SaaS アプリケーションへのアクセスを割り当てることもできます。詳細については、「Assign access for a group to a SaaS application in Azure AD (Azure AD で SaaS アプリケーションへのアクセスをグループに割り当てる)」を参照してください。

## 参照トピック

- [Administer your Azure AD directory (Azure AD ディレクトリの管理)](active-directory-administer.md)
- [Azure AD でのユーザーの作成または編集](active-directory-create-users.md)
- [Azure AD でのパスワードの管理](active-directory-manage-passwords.md)

<!---HONumber=58--> 