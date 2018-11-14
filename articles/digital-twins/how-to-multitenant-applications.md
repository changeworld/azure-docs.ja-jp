---
title: Azure Digital Twins でマルチテナント アプリケーションを有効にする | Microsoft Docs
description: 顧客の Azure Active Directory テナントを Azure Digital Twins に登録する方法を説明します
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259889"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Azure Digital Twins でマルチテナント アプリケーションを有効にする

Azure Digital Twins を使用する開発者は、一般に、マルチテナント アプリケーションを構築する必要があります。 *マルチ テナント アプリケーション*は、複数の顧客をサポートする、1 つのプロビジョニング済みインスタンスです。 それぞれの顧客は、固有の独立したデータと特権を持ちます。

このドキュメントでは、複数の Azure Active Directory (Azure AD) テナントと顧客をサポートする Azure Digital Twins マルチテナント アプリを作成する方法を詳しく説明します。

## <a name="scenario-summary"></a>シナリオの概要

このシナリオでは開発者 D と顧客 C について考えます。

- 開発者 D は、Azure AD テナントを含む Azure サブスクリプションを持っています。
- 開発者 D は、自分の Azure サブスクリプションに Azure Digital Twins インスタンスをデプロイします。
- Azure AD によって開発者 D の Azure AD テナント内にサービス プリンシパルが作成されているため、開発者 D の Azure AD テナント内のユーザーは、Azure Digital Twins サービスに対するトークンを取得できます。
- ここで開発者 D は、Azure Digital Twins の管理 API と直接統合するモバイル アプリを作成します。
- 開発者 D は、顧客 C に、そのモバイル アプリケーションの使用を許可します。
- 顧客 C が開発者 D のアプリケーション内で Azure Digital Twins の管理 API を使用するには、承認される必要があります。

  > [!IMPORTANT]
  > - 顧客 C が開発者 D のアプリケーションにログインしたときに、アプリは顧客 C のユーザーが管理 API と対話するためのトークンを取得できません。
  > - Azure AD は、顧客 C のディレクトリ内で Azure Digital Twins が認識されていないことを示すエラーをスローします。

## <a name="solution"></a>解決策

前記のシナリオを解決するには、顧客 C の Azure AD テナント内に Azure Digital Twins のサービス プリンシパルを作成するために、以下のアクションが必要になります。

- 顧客 C が、Azure AD テナントを含む Azure サブスクリプションをまだ持っていない場合:

  - 顧客 C の Azure AD テナント管理者は、[従量課金制の Azure サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を取得する必要があります。
  - その後、顧客 C の Azure AD テナント管理者は、[テナントを新しいサブスクリプションとリンクする](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)必要があります。

- 顧客 C の Azure AD テナント管理者は、[Azure portal](https://portal.azure.com) で以下の手順を実行します。

  1. **[サブスクリプション]** を開きます。
  1. 開発者 D のアプリケーションで使用する Azure AD テナントがあるサブスクリプションを選択します。
  1. **[リソース プロバイダー]** を選択します。
  1. **Microsoft.IoTSpaces** を検索します。
  1. **[登録]** を選択します。
  
## <a name="next-steps"></a>次の手順

Azure Digital Twins でユーザー定義関数を使用する方法の詳細については、[Azure Digital Twins の UDF](how-to-user-defined-functions.md) に関するページをご覧ください。

ロールベースのアクセス制御を使用して、ロールの割り当てによってアプリケーションのセキュリティ保護を強化する方法については、[Azure Digital Twins のロールベースのアクセス制御](security-create-manage-role-assignments.md)に関するページをご覧ください。
