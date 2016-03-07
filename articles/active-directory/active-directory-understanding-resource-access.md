<properties
    pageTitle="Azure でのリソース アクセスについて | Microsoft Azure" 
    description="このトピックでは、完全な Azure ポータルでのサブスクリプション管理者によるリソース アクセス制御の概念について説明します。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/11/2016"
    ms.author="markusvi"/>


# Azure でのリソース アクセスについて


> [AZURE.NOTE] このトピックでは、完全な Azure ポータルでのサブスクリプション管理者によるリソース アクセス制御の概念について説明します。Azure プレビュー ポータルで[ロール ベースのアクセス制御](role-based-access-control-configure.md)を使用して、Azure リソースをさらに細かく管理することもできます。

2013 年 10 月、Azure クラシック ポータルと Service Management API は Azure Active Directory に統合されました。これは、Azure リソースへのアクセスを管理する際のユーザー エクスペリエンスの向上を目指し、その基盤を構築することを目的としています。Azure Active Directory には、ユーザー管理、オンプレミスのディレクトリ同期、多要素認証、アプリケーション アクセス制御など、優れた機能が用意されています。当然、こうした機能も、Azure リソースの管理に伴う作業すべてで使用できます。

Azure のアクセス制御では、最初に課金に注目します。[Azure アカウント センター](https://account.windowsazure.com/subscriptions)がアクセスする Azure アカウントの所有者は、アカウント管理者 (AA) です。サブスクリプションは課金のコンテナーですが、セキュリティ境界の役割も果たしています。各サブスクリプションに配置されているサービス管理者 (SA) は、[Azure クラシック ポータル](https://manage.windowsazure.com/)を使用して、サブスクリプション内で Azure リソースを追加、削除、変更できます。新しいサブスクリプションの既定の SA は AA ですが、AA は Azure アカウント センターで SA を変更することができます。

<br><br>![Azure アカウント][1]

サブスクリプションは、ディレクトリにも関連付けられています。ディレクトリでは、一連のユーザーを定義します。たとえば、ディレクトリを作成した職場や学校のユーザーや、外部ユーザー (つまり、Microsoft アカウント) として定義できます。サブスクリプションにアクセスできるのは、このディレクトリ ユーザーのうち、サービス管理者 (SA) またはコラボレーション管理者 (CA) として割り当てられたユーザーです。唯一の例外は、Microsoft アカウント (以前の Windows Live ID) です。このアカウントは、従来のシステムに対応するという理由から、ディレクトリ内に存在していなくても SA または CA として割り当てることができます。

<br><br>![Azure でのアクセス制御][2]


Microsoft アカウントを使用してサインインした SA は、Azure クラシック ポータル内の機能を使用して、**[設定]** の **[サブスクリプション]** ページにある **[ディレクトリの編集]** で、サブスクリプションが関連付けられているディレクトリを変更できます。この操作が、サブスクリプションのアクセス制御に対して影響を及ぼすことに注意してください。



> [AZURE.NOTE] Azure クラシック ポータルの **[ディレクトリの編集]** コマンドは、職場または学校アカウントを使用してサインインしたユーザーは利用できません。こうしたアカウントでは、自分が属しているディレクトリにしかサインインできないからです。

<br><br>![簡単なユーザー ログイン フロー][3]

簡単な例では、組織 (Contoso など) による課金とアクセス制御が、同じサブスクリプション セットに対して適用されます。つまり、ディレクトリは、1 つの Azure アカウントが所有するサブスクリプションに関連付けられます。Azure クラシック ポータルに正常にログインすると、2 つのリソース コレクションが表示されます (前の図ではオレンジ色で表示)。


- ユーザー アカウントが存在しているディレクトリ (内部で提供、または外部プリンシパルとして追加)。ログインに使用するディレクトリがこの計算に関連付けられないこと、したがって、ログインした場所にかかわりなくディレクトリは常に表示されることに注意してください。

- ログインに使用されるディレクトリに関連付けられているサブスクリプションの一部であると同時に、ユーザー (ここでは SA または CA) がアクセスできるリソース。


<br><br>![複数のサブスクリプションおよびディレクトリを持つユーザー][4]


複数のディレクトリにまたがるサブスクリプションを使用しているユーザーは、サブスクリプション フィルターを使用して、Azure クラシック ポータルの現在のコンテキストを切り替えることができます。内部的には、ディレクトリごとに異なるログインという結果になっていますが、この操作は、シングル サインオン (SSO) によってシームレスに実行されます。

サブスクリプションのディレクトリ ビューが 1 つであるため、複数のサブスクリプション間でリソースを移動する、といった操作はさらに難しくなる可能性があります。たとえば、リソースの転送を実行するには、最初に **[設定]** にある [サブスクリプション] ページの **[ディレクトリの編集]** で、複数のサブスクリプションを同じディレクトリに関連付けなければならない可能性があります。

## 次のステップ

- Azure サブスクリプションの管理者を変更する方法の詳細については、「[Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)」を参照してください。

- Azure Active Directory と Azure サブスクリプションの関係の詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)」を参照してください。

- Azure AD でロールを割り当てる方法の詳細については、「[Azure Active Directory (Azure AD) の管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=AcomDC_0224_2016-->