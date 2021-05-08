---
title: API 前提条件 - Azure Marketplace
description: Cloud パートナー ポータル API を使用するための前提条件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: f5fc77b65f6a83f4f7ca8ed8b8c9294b95307735
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107314"
---
# <a name="api-prerequisites"></a>API 前提条件

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 「[Cloud パートナー ポータルの API リファレンス](cloud-partner-portal-api-overview.md)」に記載されている変更内容を調べて、パートナー センターへの切り替え後もコードが引き続き動作することを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

Cloud パートナー ポータルの API を使用するには、2 つの必須のプログラム アセット (サービス プリンシパルと Azure Active Directory (Azure AD) アクセス トークン) が必要になります。

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Azure Active Directory テナントにサービス プリンシパルを作成する

最初に、Azure AD テナントでサービス プリンシパルを作成する必要があります。 このテナントには、クラウド パートナー ポータルで独自のアクセス許可セットが割り当てられます。 コードでは、個人の資格情報の代わりに、このテナントを使用して API が呼び出されます。 サービス プリンシパルを作成する方法の詳細については、「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」のガイダンスに従って、サービス プリンシパルを作成します。

## <a name="add-service-principal-to-your-account"></a>サービス プリンシパルをアカウントに追加する

テナントにサービス プリンシパルを作成したので、それをユーザーとしてパートナー センター ポータル アカウントに追加できます。 ユーザーと同様に、サービス プリンシパルには、所有者またはポータルの共同作成者を指定できます。 詳細については、下の **次の手順** を参照してください。

## <a name="next-steps"></a>次の手順

[Azure AD アプリケーションを管理する](manage-aad-apps.md)を参照てください。
