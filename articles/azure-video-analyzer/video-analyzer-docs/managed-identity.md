---
title: Azure Video Analyzer を使用したマネージド ID
description: このトピックでは、Azure Video Analyzer でマネージド ID を使用する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: 8d9558319168e2a94c81f764c9b19f70dc904f79
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386803"
---
# <a name="managed-identity"></a>マネージド ID

異なるサービス間でやり取りされる通信のセキュリティを確保するシークレットと資格情報の管理は、開発者に共通の課題です。 Azure のマネージド ID を使用すれば、開発者が資格情報を管理する必要がなくなります。Azure リソースの ID は Azure Active Directory (Azure AD) から提供され、その ID を使用して Azure AD トークンが取得されます。

Azure Video Analyzer アカウントを作成する場合は、Azure ストレージ アカウントを関連付ける必要があります。 Video Analyzer を使用してカメラからライブ ビデオを記録する場合、そのデータはストレージ アカウント内のコンテナーに BLOB として格納されます。 次のように、マネージド ID を使用して、Video Analyzer アカウントにストレージ アカウントへの適切なアクセス権を付与する必要があります。


## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Video Analyzer のユーザー割り当てマネージド ID

1. [ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を作成する

1. Azure のストレージ アカウントの作成

   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

1. 上記のストレージ アカウントの[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールと[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールをマネージド ID に追加します。

1. ユーザー割り当てマネージド ID とストレージ アカウントを関連するプロパティの値として指定して、Video Analyzer アカウントを作成します。

Video Analyzer では、マネージド ID を使用して、ユーザーに代わってストレージ アカウントにアクセスできるようになります。

上記 の手順 を実行する方法の例については、[この](create-video-analyzer-account.md)記事を参照してください。


## <a name="next-steps"></a>次のステップ

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。
