---
title: Azure Digital Twins でマルチテナント アプリケーションを有効にする方法 | Microsoft Docs
description: 顧客の Azure Active Directory テナントを Azure Digital Twins に登録する方法を説明します
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324019"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Azure Digital Twins でマルチテナント アプリケーションを有効にする方法

Azure Digital Twins プラットフォームを使用している開発者は、マルチテナント アプリケーションを構築することがあります。 "*マルチテナント アプリケーション*" は、それぞれが独自の独立したデータと特権を持つ複数の顧客をサポートするために使用される 1 つのプロビジョニングされたインスタンスです。

このドキュメントでは、複数の Azure Active Directory (AD) テナントと顧客をサポートする Azure Digital Twins マルチテナント アプリを作成する方法について説明します。

## <a name="scenario-summary"></a>シナリオの概要

このシナリオでは開発者 D と顧客 C について考えます。

- 開発者 D は、Azure AD テナントを含む Azure サブスクリプションを持っています。
- 開発者 D は、自分の Azure サブスクリプションに Digital Twins インスタンスをデプロイしました。
- Azure AD によって開発者 D の Azure AD テナント内にサービス プリンシパルが作成されているので、開発者 D の Azure AD テナント内のユーザーは Digital Twins サービスに対するトークンを取得できます。
- 開発者 D は、Digital Twins の Management API と直接統合するモバイル アプリを作成します。
- 開発者 D は、顧客 C にそのモバイル アプリケーションの使用を許可します。
- 顧客 C は、開発者 D のアプリケーション内で Digital Twins の Management API を使用するには承認される必要があります。

  > [!IMPORTANT]
  > - 顧客 C が開発者 D のアプリケーションにログインしたとき、アプリは顧客 C のユーザーが Management API と対話するためのトークンを取得することができません。
  > - Azure AD は、顧客 C のディレクトリ内で Digital Twins が認識されないことを示すエラーをスローします。

## <a name="solution"></a>解決策

上記のシナリオを解決するには、以下のようにして、顧客 C の Azure AD テナント内に Digital Twins のサービス プリンシパルを作成する必要があります。

- 顧客 C が、Azure AD テナントを含む Azure サブスクリプションをまだ持っていない場合:

  - 顧客 C の Azure AD テナント管理者は、[従量課金制 Azure サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を取得する必要があります。
  - その後、顧客 C の Azure AD テナント管理者は、[テナントを新しいサブスクリプションとリンクする](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)必要があります。

- 顧客 C の Azure AD テナント管理者は、[Azure Portal](https://portal.azure.com) から以下のことを行う必要があります。
  1. **[サブスクリプション]** を開きます。
  1. 開発者 D のアプリケーションで使用する Azure AD テナントのあるサブスクリプションを選択します。
  1. **[リソース プロバイダー]** を選択します。
  1. **Microsoft.IoTSpaces** を検索します。
  1. **[登録]** をクリックします。
  
## <a name="next-steps"></a>次の手順

Azure Digital Twins でのユーザー定義関数の使用について詳しくは、[Azure Digital Twins の UDF](how-to-user-defined-functions.md) に関するページをご覧ください。

ロールベースのアクセス制御を使用してロールの割り当てでアプリケーションのセキュリティを強化する方法については、[Digital Twins のロールベースのアクセス制御](security-create-manage-role-assignments.md)に関するページをご覧ください。
