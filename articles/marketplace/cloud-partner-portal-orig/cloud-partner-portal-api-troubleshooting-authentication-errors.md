---
title: 一般的な認証エラーのトラブルシューティング | Azure Marketplace
description: Cloud パートナー ポータル API を使用するときの一般的な認証エラーについて説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 1da9bbd1ed4bc4abea0699e56d8adc397086d6e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280424"
---
# <a name="troubleshooting-common-authentication-errors"></a>一般的な認証エラーのトラブルシューティング

この記事では、Cloud パートナー ポータル API を使用するときの一般的な認証エラーについて説明します。

## <a name="unauthorized-error"></a>未承認エラー

`401 unauthorized` エラーがいつも発生する場合は、有効なアクセス トークンがあることを確認します。  まだ行っていない場合は、「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)」で説明されているように、基本的な Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成します。 次に、アプリケーションまたは簡単な HTTP POST 要求を使用して、アクセス権を確認します。  次の図のように、アクセス トークンを取得するには、テナント ID、アプリケーション ID、オブジェクト ID、およびシークレット キーを使用します。

![401 エラーのトラブルシューティング](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>アクセス不可エラー

`403 forbidden` エラーが発生する場合は、Cloud パートナー ポータルで発行元アカウントに正しいサービス プリンシパルが追加されていることを確認します。
[前提条件](./cloud-partner-portal-api-prerequisites.md)に関するページの手順に従って、ポータルにサービス プリンシパルを追加します。

正しいサービス プリンシパルが追加されている場合は、他のすべての情報を確認します。 ポータルで入力されているオブジェクト ID に特に注意します。 Azure Active Directory アプリ登録ページには 2 つのオブジェクト ID があり、ローカル オブジェクト ID を使用する必要があります。 アプリの **[アプリの登録]** ページに移動し、 **[ローカル ディレクトリでのマネージド アプリケーション]** でアプリ名をクリックすることにより、正しい値を見つけることができます。 このようにすると、アプリのローカル プロパティに移動、そこの **[プロパティ]** ページで正しいオブジェクト ID を確認できます (次図参照)。 また、サービス プリンシパルを追加するときと、API 呼び出しを行うときに、正しい発行元 ID を使用していることを確認します。

![403 エラーのトラブルシューティング](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
