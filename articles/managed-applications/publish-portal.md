---
title: ポータル経由で Azure マネージ アプリケーションを発行する | Microsoft Docs
description: 組織のメンバーを対象とする Azure マネージ アプリケーションを Azure ポータルを使用して作成する方法について説明します。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 69d31a7199347574e8866b275ec17ba3997d80c2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Azure ポータル経由でサービス カタログ アプリケーションを発行する

組織のメンバーを対象とする[マネージ アプリケーション](overview.md)を、Azure ポータルを使用して発行できます。 たとえば、IT 部門が、組織標準に確実に準拠するマネージ アプリケーションを発行できます。 こうしたマネージ アプリケーションは、Azure Marketplace ではなく、サービス カタログを利用して入手できます。

## <a name="prerequisites"></a>前提条件

マネージ アプリケーションを発行するときに、リソースを管理するための ID を指定します。 Azure Active Directory ユーザー グループを指定することをお勧めします。 Azure Active Directory ユーザー グループを作成するには、「[Azure Active Directory でグループを作成し、メンバーを追加する](../active-directory/active-directory-groups-create-azure-portal.md)」を参照してください。 

マネージ アプリケーション定義を含む .zip ファイルを、URI を通して使用できる必要があります。 .zip ファイルをストレージ BLOB にアップロードしておくことをお勧めします。 

## <a name="create-managed-application-with-portal"></a>ポータルを使用してマネージ アプリケーションを作成する

1. 左上にある **[+ 新規]** を選択します。

   ![新しいサービス](./media/publish-portal/new.png)

1. **サービス カタログ**を検索します。

1. 結果から、**[Service Catalog Managed Application Definition]\(サービス カタログのマネージ アプリケーション定義\)** までスクロールします。 それを選択します。

   ![マネージ アプリケーション定義を検索する](./media/publish-portal/select-managed-apps-definition.png)

1. **[Create]\(作成\)** を選択し、マネージ アプリケーション定義を作成するプロセスを開始します。

   ![マネージ アプリケーション定義を作成する](./media/publish-portal/create-definition.png)

1. 名前、表示名、説明、場所、サブスクリプション、リソース グループの値を指定します。 パッケージ ファイルの URI については、作成した zip ファイルのパスを指定します。

   ![値を指定する](./media/publish-portal/fill-application-values.png)

1. [Authentication and Lock Level]\(認証とロックのレベル\) セクションで、**[Add Authorization]\(承認の追加\)** を選択します。

   ![承認を追加する](./media/publish-portal/add-authorization.png)

1. リソースを管理する Azure Active Directory グループを選択し、**[OK]** を選択します。

   ![承認グループを追加する](./media/publish-portal/add-auth-group.png)

1. すべての値を指定したら、**[Create]\(作成\)** を選択します。

   ![マネージ アプリケーションを作成する](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>次の手順

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。
* マネージ アプリケーションの例については、[Azure マネージ アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージ アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。