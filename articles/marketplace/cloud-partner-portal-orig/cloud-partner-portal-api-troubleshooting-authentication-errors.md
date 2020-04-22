---
title: 一般的な認証エラーのトラブルシューティング | Azure Marketplace
description: Cloud パートナー ポータル API を使用するときの一般的な認証エラーについて説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255993"
---
# <a name="troubleshooting-common-authentication-errors"></a>一般的な認証エラーのトラブルシューティング

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターに統合されており、オファーがパートナー センターに移行された後も引き続き機能します。 統合によりわずかな変更が行われました。 「[Cloud パートナー ポータル API リファレンス](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)」に記載されている変更内容を調べて、パートナー センターへの移行後もコードが引き続き動作することを確認してください。

この記事では、Cloud パートナー ポータル API を使用するときの一般的な認証エラーについて説明します。

## <a name="unauthorized-error"></a>未承認エラー

`401 unauthorized` エラーがいつも発生する場合は、有効なアクセス トークンがあることを確認します。  まだ行っていない場合は、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」で説明されているように、基本的な Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成します。 次に、アプリケーションまたは簡単な HTTP POST 要求を使用して、アクセス権を確認します。  次の図のように、アクセス トークンを取得するには、テナント ID、アプリケーション ID、オブジェクト ID、およびシークレット キーを使用します。

![401 エラーのトラブルシューティング](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>アクセス不可エラー

`403 forbidden` エラーが発生する場合は、Cloud パートナー ポータルで発行元アカウントに正しいサービス プリンシパルが追加されていることを確認します。
[前提条件](./cloud-partner-portal-api-prerequisites.md)に関するページの手順に従って、ポータルにサービス プリンシパルを追加します。

正しいサービス プリンシパルが追加されている場合は、他のすべての情報を確認します。 ポータルで入力されているオブジェクト ID に特に注意します。 Azure Active Directory アプリ登録ページには 2 つのオブジェクト ID があり、ローカル オブジェクト ID を使用する必要があります。 アプリの **[アプリの登録]** ページに移動し、 **[ローカル ディレクトリでのマネージド アプリケーション]** でアプリ名をクリックすることにより、正しい値を見つけることができます。 このようにすると、アプリのローカル プロパティに移動、そこの **[プロパティ]** ページで正しいオブジェクト ID を確認できます (次図参照)。 また、サービス プリンシパルを追加するときと、API 呼び出しを行うときに、正しい発行元 ID を使用していることを確認します。

![403 エラーのトラブルシューティング](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
