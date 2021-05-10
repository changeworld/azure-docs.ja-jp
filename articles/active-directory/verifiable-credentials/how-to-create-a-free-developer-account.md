---
title: 無料の Azure Active Directory 開発者テナントを作成する方法
description: この記事では、開発者アカウントを作成する方法について説明します。
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587267"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>無料の Azure Active Directory 開発者テナントを作成する方法

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

> [!NOTE]
> プレビュー段階では P2 ライセンスが必要です。 

P2 試用版ライセンスを使用して無料の Azure Active Directory を作成する簡単な方法が 2 つあり、検証可能な資格情報発行者サービスをインストールし、検証可能な資格情報を作成して検証することができます。

- 無料の Microsoft 365 開発者プログラムに[参加](https://aka.ms/o365devprogram)し、P2 ライセンスで無料のサンドボックス、ツール、その他の Azure Active Directory などのリソースを入手してください。 構成済みのユーザー、グループ、メールボックスなど。
- 新しい[テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)を作成し、新しいテナントで[無料試用版](https://azure.microsoft.com/trial/get-started-active-directory/)の Azure AD Premium P1 または P2 をアクティブ化します。

無料の Microsoft 365 開発者プログラムにサインアップする場合は、次の簡単な手順に従う必要があります。

1. 画面の **[今すぐ参加]** ボタンをクリックします。

2. 新しい Microsoft アカウントでサインインするか、既に所有している既存の (職場) アカウントを使用します。

3. サインアップ ページで、リージョンを選択し、会社名を入力して、プログラムの使用条件に同意してから、 **[次へ]** をクリックします。

4. **[set up subscription]\(サブスクリプションの設定\)** をクリックします。 新しいテナントを作成するリージョンを指定し、ユーザー名とドメインを作成して、パスワードを入力します。 これにより、新しいテナントとテナントの最初の管理者が作成されます。

5. 新しいテナントの管理者アカウントを保護するために必要なセキュリティ情報を入力します。 これにより、アカウントの MFA 認証が設定されます。


この時点で、25 個の E5 ユーザー ライセンスを持つテナントが作成されました。 E5 ライセンスには、Azure AD P2 ライセンスが含まれています。 必要に応じて、開発環境でのテストに役立つ、ユーザー、グループ、メール、SharePoint が含まれるサンプル データ パックを追加できます。 検証可能な資格情報発行サービスの場合、それらは必要ありません。

便宜上、新しく作成されたテナントに[ゲスト](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal)として自分の職場アカウントを追加し、そのアカウントを使用してテナントを管理できます。 ゲスト アカウントで検証可能な資格情報サービスを管理できるようにするには、そのユーザーに "グローバル管理者" ロールを割り当てる必要があります。

## <a name="next-steps"></a>次の手順

開発者アカウントが用意できたので、[最初のチュートリアル](get-started-verifiable-credentials.md)を試して、検証可能な資格情報の詳細を確認できます。
