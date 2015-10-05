<properties
   pageTitle="複数の Azure AD ディレクトリを追加および管理する | Microsoft Azure"
   description="ディレクトリを完全に独立したリソースとして説明する、Azure AD ディレクトリの追加と管理のための手順とベスト プラクティス"
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
   ms.date="09/21/2015"
   ms.author="curtand"/>

# 複数の Azure AD ディレクトリを追加および管理する

Azure AD の各ディレクトリは、完全に独立したリソースです。つまり、対等であり、フル機能を備え、管理対象の他のディレクトリから論理的に独立しています。ディレクトリ間に親子関係はありません。このディレクトリ間の独立には、リソースの独立、管理上の独立、同期の独立があります。

##リソースの独立

後述する外部ユーザーの一部の例外を除き、あるディレクトリでリソースを作成または削除しても、別のディレクトリのリソースには影響しません。あるディレクトリで "contoso.com" というカスタム ドメインを使用している場合、このドメインを他のディレクトリで使用することはできません。

##管理上の独立

"Contoso" ディレクトリの管理者以外のユーザーが "Test" というテスト ディレクトリを作成した場合、次のようになります。既定では、ディレクトリを作成したユーザーがその新しいディレクトリの外部のユーザーとして追加され、そのディレクトリのグローバル管理者ロールが割り当てられます。"Test" ディレクトリの管理者が特に特権を付与しない限り、"Contoso" ディレクトリの管理者には "Test" の直接の管理者特権はありません。"Contoso" の管理者は、"Test" を作成したユーザー アカウントを制御している場合は、"Test" ディレクトリへのアクセスを制御できます。1 つのディレクトリのユーザーの管理者ロールを変更 (追加または削除) しても、そのユーザーが別のディレクトリに対して持っている管理者ロールには影響しません。

##同期の独立

次のどちらかの 1 つのインスタンスからデータが同期されるように各 Azure AD ディレクトリを独立して構成することができます。1 つは、単一の AD フォレストとデータを同期するディレクトリ同期 (DirSync) ツールです。もう 1 つは、1 つ以上のオンプレミスのフォレストと Azure 以外の AD データ ソースの一方または両方とデータを同期する Forefront Identity Manager 用 Azure Active Directory コネクタです。

##Azure AD ディレクトリを追加する

Azure 管理ポータルで Azure AD ディレクトリを追加するには、左側で Active Directory 拡張機能を選択し、**[追加]** をタップします。

> [AZURE.NOTE]他の Azure リソースとは異なり、ディレクトリは Azure サブスクリプションの子リソースではありません。Azure サブスクリプションを取り消した場合や、期限切れを許可した場合でも、Azure PowerShell、Azure Graph API、Office 365 管理センターなどの他のインターフェイスを使用して、ディレクトリ データに引き続きアクセスできます。また、ディレクトリに別のサブスクリプションを関連付けることもできます。

Azure AD のライセンスに関する問題とベスト プラクティスの概要については、[Azure Active Directory ライセンス](active-directory-licensing-what-is.md)に関するページを参照してください。

<!---HONumber=Sept15_HO4-->