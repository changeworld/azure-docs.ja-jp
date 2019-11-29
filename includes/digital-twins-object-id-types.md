---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e46041a33c12b3fcb40e5a04de11108471e59855
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307230"
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