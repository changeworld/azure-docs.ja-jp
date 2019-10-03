---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827690"
---
`objectIdType` (または**オブジェクト識別子の型**) は、ロールに与えられる ID の型を示します。 `DeviceId` および `UserDefinedFunctionId` 型を除き、オブジェクト識別子の型は Azure Active Directory オブジェクトのプロパティに対応します。

次の表に、Azure Digital Twins でサポートされているオブジェクト識別子の型を示します。

| 種類 | 説明 |
| --- | --- |
| UserId | ユーザーにロールを割り当てます。 |
| deviceId | デバイスにロールを割り当てます。 |
| DomainName | ドメイン名にロールを割り当てます。 指定されたドメイン名を持つ各ユーザーには、対応するロールのアクセス権が与えられます。 |
| TenantId | テナントにロールを割り当てます。 指定された Azure AD テナント ID に属する各ユーザーには、対応するロールのアクセス権が与えられます。 |
| ServicePrincipalId | サービス プリンシパルのオブジェクト ID にロールを割り当てます。 |
| UserDefinedFunctionId | ユーザー定義関数 (UDF) にロールを割り当てます。 |