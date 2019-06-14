---
title: Azure AD エンタイトルメント管理 (プレビュー) でカタログを作成および管理する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) でリソースとアクセス パッケージの新しいコンテナーを作成する方法を説明します。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64540680"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でカタログを作成および管理する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-a-catalog"></a>カタログを作成する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。 カタログ所有者は、別のカタログ所有者を追加できます。

**前提となるロール:** ユーザー管理者またはカタログ作成者

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックします。

    ![Azure portal 内のエンタイトルメント管理カタログ](./media/entitlement-management-catalog-create/catalogs.png)

1. **[新しいカタログ]** をクリックします。

1. カタログの一意の名前と説明を入力します。

    この情報は、アクセス パッケージの詳細に表示されます。

1. このカタログ内のアクセス パッケージが作成されたらすぐにユーザーがそのパッケージを要求できるようにする場合は、 **[有効]** を **[はい]** に設定します。

1. 選択された外部ディレクトリ内のユーザーがこのカタログ内のアクセス パッケージを要求できるようにする場合は、 **[Enabled for external users]\(外部ユーザーに対して有効にする\)** を **[はい]** に設定します。

    ![新しいカタログ ペイン](./media/entitlement-management-catalog-create/new-catalog.png)

1. **[作成]** をクリックすると、カタログが作成されます。

## <a name="add-resources-to-a-catalog"></a>カタログにリソースを追加する

アクセス パッケージにリソースを含めるには、リソースがカタログ内に存在している必要があります。 追加できるリソースの種類は、グループ、アプリケーション、および SharePoint Online サイトです。

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを追加するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. **[リソースの追加]** をクリックします。

1. リソースの種類として、 **[グループ]** 、 **[アプリケーション]** 、または **[SharePoint サイト]** のいずれかをクリックします。

    カタログ作成者である場合は、所有するすべての Office 365 グループまたは Azure AD セキュリティ グループをカタログに追加できます。 ユーザーに割り当てるグループがあるが、そのグループを所有していない場合は、ユーザー管理者にそのグループをカタログに追加してもらう必要があります。

    カタログ作成者である場合は、所有するすべての Azure AD エンタープライズ アプリケーション (SaaS アプリケーションと、Azure AD にフェデレーションされた独自のアプリケーションの両方を含む) をカタログに追加できます。 ユーザーに割り当てるアプリケーションがあるが、所有はしていない場合、ユーザー管理者にそのアプリケーションをカタログに追加してもらう必要があります。 アプリケーションがカタログに追加されると、アクセス パッケージ内のそのアプリケーションのロールをどれでも選択できるようになります。

1. カタログに追加する種類のリソースを 1 つ以上選択します。

1. 作業が終了したら、 **[追加]** をクリックします。

    これらのリソースをカタログ内のアクセス パッケージに含めることができるようになりました。

## <a name="remove-resources-from-a-catalog"></a>カタログからリソースを削除する

カタログからリソースを削除できます。 カタログからリソースを削除できるのは、そのリソースがカタログのどのアクセス パッケージでも使用されていない場合に限られます。

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを削除するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. 削除するリソースを選択します。

1. **[削除]** をクリックします (または、省略記号 ( **...** ) をクリックして **[リソースの削除]** をクリックします)。

## <a name="add-catalog-owners-or-access-package-managers"></a>カタログ所有者またはアクセス パッケージ マネージャーを追加する

カタログまたはカタログ内のアクセス パッケージの管理を委任する場合は、カタログ所有者またはアクセス パッケージ マネージャーを追加します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

1. **[所有者の追加]** または **[Add access package managers]\(アクセス パッケージ マネージャーの追加\)** をクリックして、それらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="edit-a-catalog"></a>カタログを編集する

カタログの名前と説明を編集できます。 この情報は、アクセス パッケージの詳細に表示されます。

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、編集するカタログを開きます。

1. カタログの **[概要]** ページで **[編集]** をクリックします。

1. カタログの名前または説明を編集します。

1. **[Save]** をクリックします。

## <a name="delete-a-catalog"></a>カタログを削除する

カタログを削除できるのは、そのカタログにどのアクセス パッケージも含まれていない場合に限られます。

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、削除するカタログを開きます。

1. カタログの **[概要]** で **[削除]** をクリックします。

1. 表示されるメッセージ ボックスで、 **[はい]** をクリックします。

## <a name="next-steps"></a>次の手順

- [アクセス パッケージを作成および管理する](entitlement-management-access-package-create.md)
