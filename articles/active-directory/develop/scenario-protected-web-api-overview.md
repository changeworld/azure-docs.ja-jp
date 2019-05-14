---
title: 保護された Web API - 概要 | Azure
description: 保護された Web API を構築する方法 (概要) について説明します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080103"
---
# <a name="scenario-protected-web-api"></a>シナリオ: 保護された Web API

このシナリオでは、Web API を公開する方法と、認証されたユーザーのみが API にアクセスできるように Web API を保護する方法をご紹介します。 職場と学校の両方のアカウントまたは個人的な Microsoft 個人アカウントで認証されたユーザーがあなたの Web API を使用できるようにする必要が生じる場合があります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>詳細

Web API を保護するために知っておく必要があるいくつかの詳細を以下に示します。

- お使いのアプリの登録では、少なくとも 1 つのスコープを公開する必要があります。 お使いの Web API で受け入れられるトークンのバージョンは、サインイン対象ユーザーによって異なります。
- Web API のコードの構成では、Web API を呼び出すときに使用するトークンを検証する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-protected-web-api-app-registration.md)
