<properties
	pageTitle="Azure Active Directory プレビューの説明 | Microsoft Azure"
	description="このトピックでは、クラシック ポータルの Azure Active Directory と Azure Portal の Azure Active Directory の違いについて説明します。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Azure Portal での Azure Active Directory のプレビュー版の管理サービス

Azure Active Directory (Azure AD) 管理サービスが、Azure Portal でプレビュー段階になりました。この機能を試すには、ディレクトリのグローバル管理者として [Azure Portal](https://portal.azure.com) にサインインします。表示されている場合は、サービスの一覧で Azure Active Directory を選択します。一覧にこのサービスが表示されていない場合は、**[その他のサービス]** を選択してすべてのサービスの一覧を表示します。Azure Portal で Azure AD の管理サービスを試すのに Azure サブスクリプションは必要ありません。


## プレビュー版の機能

プレビュー版では、ユーザー、グループ、アプリケーション、ディレクトリ設定など、多くのディレクトリ リソースを Azure Portal で管理することができます。現在、こちらのサービスに [Azure クラシック ポータル](https://manage.windowsazure.com)の Azure AD 管理サービスで使用可能なすべての機能を含めるように改善中です。この改善が完了するまでは、一部のディレクトリ管理タスクはクラシック ポータルで完了する必要があります。

## 同じ Azure AD テナントを管理する

プレビュー サービスでは、クラシック ポータルや Office 365 管理センターと同じ Azure Active Directory テナントの読み取りおよび書き込みを行います。これらのポータルのいずれかで変更を行うと、ほかのすべてのポータルに反映されます。

## 同じ承認ロジックを使用する

プレビュー サービスでは、既存の Active Directory クライアントと同じ承認ロジックを使用しています。ユーザーには、グローバル管理者、ユーザー管理者、パスワード管理者などのディレクトリ ロールに基づいてディレクトリ リソースの変更が許可されます。ユーザーが Azure リソースのロールや Azure サブスクリプションを持っていても、ディレクトリ リソースの管理は許可されません。Azure AD の管理ロールの詳細については、「[Azure Active Directory の管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。

プレビュー サービスは、グローバル管理者用に最適化されています。グローバル管理者ではないユーザーとしてサインインしプレビュー サービスを使用する場合、機能が低下する場合があります。たとえば、あるボタンをクリックした場合に、ディレクトリ内では完了できないタスクが開始される可能性があります。この点についてはまもなく改善される予定です。
 
## ご意見をお寄せください

プレビュー サービスに関するフィードバックを、[Azure AD フィードバック フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc)の管理ポータル セクションでお寄せください。

<!---HONumber=AcomDC_0914_2016-->