---
title: '方法: Microsoft ID プラットフォームで最近削除されたアプリケーションを復元または削除する | Azure'
titleSuffix: Microsoft identity platform
description: このハウツーでは、Microsoft ID プラットフォームに登録されていて最近削除されたアプリケーションを復元または完全に削除する方法について説明します。
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079788"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームで最近削除されたアプリケーションを復元または削除する
アプリの登録を削除した後、アプリは 30 日間、停止状態のままになります。 その 30 日の期間中に、アプリの登録をそのすべてのプロパティと共に復元することができます。 その 30 日の期間が経過した後は、アプリの登録を復元できず、永続的な削除プロセスが自動的に開始される可能性があります。  この機能は、ディレクトリに関連付けられているアプリケーションにのみ適用されます。  個人用 Microsoft アカウントからアプリケーションを使用することはできず、復元できません。

Azure portal の Azure Active Directory (Azure AD) にあるアプリの登録エクスペリエンスを使用すると、削除されたアプリケーションの表示、削除されたアプリケーションの復元、またはアプリケーションの完全な削除を行うことができます。

お客様も Microsoft カスタマー サポートも、完全に削除されたアプリケーション、または 30 日より前に削除されたアプリケーションを、復元することはできないことに注意してください。

> [!IMPORTANT]
> 削除されたアプリケーション ポータルの UI 機能[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>必要なアクセス許可
アプリケーションを完全に削除するには、次のいずれかのロールが必要です。

- 全体管理者
- アプリケーション管理者
- クラウド アプリケーション管理者
- ハイブリッド ID の管理者
- アプリケーションの所有者

アプリケーションを復元するには、次のいずれかのロールが必要です。

- 全体管理者
- アプリケーションの所有者

### <a name="view-your-deleted-applications"></a>削除されたアプリケーションを表示する
論理的に削除された状態のすべてのアプリケーションを表示することができます。  復元できるのは、削除されてから 30 日未満のアプリケーションのみです。

#### <a name="to-view-your-restorable-applications"></a>復元可能なアプリケーションを表示するには
1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure Active Directory]** を見つけて選択し、 **[アプリの登録]** を選択して、 **[Deleted applications (Preview)]\(削除されたアプリケーション (プレビュー)\)** タブを選択します。

アプリケーションの一覧を確認します。 復元できるのは、過去 30 日以内に削除されたアプリケーションのみです。 アプリの登録の検索プレビューを使用している場合は、[削除日] 列でフィルター処理して、これらのアプリケーションのみを表示できます。

## <a name="restore-a-recently-deleted-application"></a>最近削除されたアプリケーションを復元する

アプリの登録が組織から削除されると、そのアプリは停止状態になり、構成が保持されます。 アプリの登録を復元すると、その構成も復元されます。  ただし、アプリケーションのホーム テナント用の **エンタープライズ アプリケーション** に組織固有の設定があった場合、それらは復元されません。  

これは、組織固有の設定は、サービス プリンシパルと呼ばれる別のオブジェクトに格納されているためです。  サービス プリンシパルに保持される設定には、特定の組織に対するアクセス許可の同意およびユーザーとグループの割り当てが含まれます。これらの構成は、アプリを復元しても復元されません。 詳細については、[アプリケーションとサービス プリンシパルのオブジェクト](app-objects-and-service-principals.md)に関する記事を参照してください。 


### <a name="to-restore-an-application"></a>アプリケーションを復元するには
1. **[Deleted applications (Preview)]\(削除されたアプリケーション (プレビュー)\)** タブで、削除されてから 30 日経っていないアプリケーションの 1 つを検索して選択します。
2. **[アプリの登録を復元]** を選択します。

## <a name="permanently-delete-an-application"></a>アプリケーションを完全に削除する
組織からアプリケーションを手動で完全に削除することができます。 完全に削除されたアプリケーションは、担当の管理者も、別の管理者も、Microsoft カスタマー サポートも復元することができません。

### <a name="to-permanently-delete-an-application"></a>アプリケーションを完全に削除するには

1. **[Deleted applications (Preview)]\(削除されたアプリケーション (プレビュー)\)** タブで、該当するアプリケーションの 1 つを検索して選択します。
2. **[完全に削除]** を選びます。
3. 警告テキストを読み、 **[はい]** を選択します。

## <a name="next-steps"></a>次の手順
アプリを復元するか、完全に削除した後は、次のことができます。

- [アプリケーションを追加します](quickstart-register-app.md)。
- Microsoft ID プラットフォームの[アプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)について確認します。
