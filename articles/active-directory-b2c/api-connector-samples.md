---
title: Azure AD B2C ユーザー フローを変更するための API のサンプル
description: API コネクタを使用してユーザー フローを変更するためのコード サンプル
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d4a807d725caa224eba67ee7419b91753dcc439c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038044"
---
# <a name="api-connector-rest-api-samples"></a>API コネクタ REST API サンプル

次の表に、[API コネクタ](api-connectors-overview.md)を使用したユーザー フローで Web API を活用するためのコード サンプルへのリンクを示します。 これらのサンプルは、主に組み込みのユーザー フローと共に使用するように設計されています。

## <a name="azure-function-quickstarts"></a>Azure 関数のクイックスタート
| サンプル                                                                                                                          | 説明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | この .NET Core Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | この Node.js Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | この Python Azure 関数サンプルには、特定の電子メール ドメインへのサインアップを制限し、ユーザー指定の情報を検証する方法が示されています。    |


## <a name="automated-fraud-protection-services--captcha"></a>自動化された不正行為防止サービスと CAPTCHA
| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs の不正行為および不正使用防止](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | このサンプルでは、Arkose Labs の不正行為および不正使用防止サービスを使用して、ユーザー サインアップを保護する方法を示します。 |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | このサンプルでは、reCAPTCHA チャレンジを使用して、自動化された不正使用を防ぐことで、ユーザー サインアップを保護する方法を示します。 |


## <a name="identity-verification"></a>本人確認

| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | このサンプルでは、IDology のサービスで、サインアップ フローの一環としてユーザー ID を確認する方法を示します。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | このサンプルでは、Experian のサービスで、サインアップ フローの一環としてユーザー ID を確認する方法を示します。 |


## <a name="other"></a>その他

| サンプル                                                                                                            | 説明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [招待コード](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | このサンプルでは、招待コードを使用して、サインアップを特定の対象ユーザーに制限する方法を示します。|
| [API コネクタのコミュニティ サンプル](https://github.com/azure-ad-b2c/api-connector-samples) | このリポジトリには、API コネクタを使用することで可能になるシナリオのサンプルがコミュニティで保持されています。|
