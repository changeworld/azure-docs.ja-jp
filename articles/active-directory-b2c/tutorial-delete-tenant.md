---
title: リソースをクリーンアップしてテナントを削除する - Azure Active Directory B2C
description: Azure AD B2C テナントを削除する方法を説明する手順です。 すべてのテナント リソースを削除してから、テナントを削除する方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 11b1382a19937d4835093979b72d7ee4c28b152b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044063"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>リソースをクリーンアップしてテナントを削除する

Azure AD B2C チュートリアルを完了すると、テストまたはトレーニングに使用したテナントを削除できます。 テナントを削除するには、最初にすべてのテナント リソースを削除する必要があります。 この記事では、次のことを行います。

> [!div class="checklist"]
> * **[テナントの削除]** オプションを使用してクリーンアップ タスクを識別する
> * テナント リソース (ユーザーフロー、ID プロバイダー、アプリケーション、ユーザー) を削除する
> * テナントを削除する

## <a name="identify-cleanup-tasks"></a>クリーンアップタスクを識別する

1. 全体管理者アカウントまたは共同管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。 Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントを使用します。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure Active Directory** サービスを選択します。
1. **[管理]** の下で、 **[プロパティ]** を選択します。
1. **[Azure リソースのアクセス管理]** の下で **[はい]** を選択した後、 **[保存]** を選択します。
1. Azure portal からサインアウトした後に、もう一度サインインして、ご自分のアクセス権を更新します。 ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure Active Directory** サービスを選択します。
1. **[概要]** ページで **[テナントの削除]** を選択します。 **必須のアクション** 列 (省略可能) には、テナントを削除する前に削除する必要があるリソースを示します。

   ![テナントタスクを削除する](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>テナントリソースを削除する

前のセクションから確認ページを開いている場合は、 **[必要なアクション]** 列のリンクを使用して、これらのリソースを削除できる Azure portal ページを開くことができます。 または、次の手順に従って、Azure AD B2C サービス内からテナント リソースを削除することもできます。

1. 全体管理者アカウントまたは共同管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。 Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントを使用します。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure AD B2C** サービスを選択します。 または、検索ボックスを使用して検索し、 **[Azure AD B2C]** を選択します。
1. 現在サインインしている管理者アカウント *以外* のすべてのユーザーを削除する: **[管理]** で **[ユーザー]** を選択します。 **[すべてのユーザー]** ページで、各ユーザーの横にあるチェックボックスをオンにします (現在サインインしている管理者アカウントは除きます)。 **[Delete]** をクリックして、入力を求められたら **[はい]** を選択します。

   ![ユーザーを削除する](media/tutorial-delete-tenant/delete-users.png)

1. アプリの登録と *b2c-extensions-app* の削除: **[管理]** で **[アプリの登録]** を選択します。 **[すべてのアプリケーション]** タブを選択します。アプリケーションを選択し、 **[削除]** を選択します。 **b2c-extensions-app** アプリケーションも含め、すべてのアプリケーションについて繰り返します。

   ![Delete application](media/tutorial-delete-tenant/delete-applications.png)

1. 構成したすべての ID プロバイダーを削除する: **[管理]** で、 **[ID プロバイダー]** を選択します。 構成した ID プロバイダーを選択し、 **[削除]** を選択します。

   ![ID プロバイダーを削除する](media/tutorial-delete-tenant/identity-providers.png)

1. ユーザーフローを削除する: **[ポリシー]** で **[ユーザーフロー]** を選択します。 各ユーザーフローの横にある省略記号 (...) を選択し、 **[削除]** を選択します。

   ![ユーザー フローを削除する](media/tutorial-delete-tenant/user-flow.png)

1. ポリシー キーを削除する: **[ポリシー]** で、 **[Identity Experience Framework]** 、 **[ポリシー キー]** の順に選択します。 各ポリシー キーの横にある省略記号 (...) を選択し、 **[削除]** を選択します。

1. カスタム ポリシーを削除する: **[ポリシー]** で **[Identity Experience Framework]** を選択し、 **[カスタムポリシー]** を選択して、すべてのポリシーを削除します。

## <a name="delete-the-tenant"></a>テナントを削除する

1. 全体管理者アカウントまたは共同管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。 Azure へのサインアップに使用したものと同じ職場または学校アカウント、または同じ Microsoft アカウントを使用します。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. **Azure Active Directory** サービスを選択します。
1. アクセス管理のアクセス許可がまだ付与されていない場合は、次の手順を実行します。

   * **[管理]** の下で、 **[プロパティ]** を選択します。
   * **[Azure リソースのアクセス管理]** の下で **[はい]** を選択した後、 **[保存]** を選択します。
   * Azure portal からサインアウトした後に、もう一度サインインして、ご自分のアクセス権を更新し、 **[Azure Active Directory]** を選択します。

1. **[概要]** ページで **[テナントの削除]** を選択します。

   ![テナントを削除する](media/tutorial-delete-tenant/delete-tenant.png)

1. 画面の指示に従って、プロセスを完了します。

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * テナント リソースを削除する
> * テナントを削除する

次に、Azure AD B2C の [ユーザーフローとカスタム ポリシー](user-flow-overview.md)の概要について説明します。
