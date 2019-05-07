---
title: Azure Storage へのアクセスを承認する | Microsoft Docs
description: Azure Active Directory、共有キー認証、共有アクセス署名など、Azure Storage へのアクセスを承認するさまざまな方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9e8c9755c444ca7b81891f5f83164bc51aa694eb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147062"
---
# <a name="authorizing-access-to-azure-storage"></a>Azure Storage へのアクセスを承認する

ストレージ アカウントのデータにアクセスするたびに、クライアントは HTTP/HTTPS 経由で Azure Storage に要求を行います。 クライアントにデータへのアクセスに必要なアクセス許可があることをサービスが確認できるように、セキュリティで保護されたリソースに対するすべての要求が承認される必要があります。 Azure Storage には、セキュリティで保護されたリソースへのアクセスを承認するために以下のオプションが用意されています。

- BLOB とキュー用の **Azure AD (Azure AD) 統合**。 Azure AD には、ストレージ アカウント内のリソースに対するクライアントのアクセスをきめ細かく制御するロールベースのアクセス制御 (RBAC) が用意されています。 詳細については、[Azure Active Directory を使用した Azure Storage に対する要求の認証](storage-auth-aad.md)に関する記事を参照してください。
- BLOB、ファイル、キュー、およびテーブル用の**共有キー認証**。 共有キーを使用するクライアントは、ストレージ アカウントのアクセス キーを使用して署名されたすべての要求にヘッダーを渡します。 詳細については、[共有キーによる承認](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)に関するページを参照してください。
- BLOB、ファイル、キュー、およびテーブル用の**共有アクセス署名**。 共有アクセス署名 (SAS) には、ストレージ アカウント内のリソースに対する制限付きの委任アクセス機能があります。 署名が有効な期間、または付与するアクセス許可に制約を追加すると、柔軟にアクセスを管理できます。 詳細については、「[Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。
- コンテナーと BLOB 用の**匿名パブリック読み取りアクセス** 承認は必要ありません。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」を参照してください。  

既定では、Azure Storage のすべてのリソースはセキュリティで保護され、アカウント所有者だけが使用できます。 前述の承認戦略のいずれかを使用して、ストレージ アカウントのリソースに対するアクセスをクライアントに許可することはできますが、セキュリティと使いやすさを最大限に高めるには Azure AD を使用することをお勧めします。 



