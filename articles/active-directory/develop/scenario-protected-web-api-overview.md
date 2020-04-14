---
title: 保護された Web API - 概要
titleSuffix: Microsoft identity platform
description: 保護された Web API を構築する方法 (概要) について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9017fbde1a44bcdf39fa8730bed25141da19fd56
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882405"
---
# <a name="scenario-protected-web-api"></a>シナリオ:保護された Web API

このシナリオでは、Web API を公開する方法について説明します。 また、認証されたユーザーのみがアクセスできるように Web API を保護する方法についても説明します。

Web API を使用するには、職場または学校アカウントの両方で認証されたユーザーを有効にするか、Microsoft の個人アカウントを有効にする必要があります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>詳細

ここでは、Web API を保護するために必要な特定の情報について説明します。

- お使いのアプリの登録では、少なくとも 1 つのスコープを公開する必要があります。 お使いの Web API で受け入れられるトークンのバージョンは、サインイン対象ユーザーによって異なります。
- Web API のコード構成では、Web API が呼び出されたときに使用されるトークンを検証する必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリの登録](scenario-protected-web-api-app-registration.md)
