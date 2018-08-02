---
title: カスタム開発されたアプリケーションへのサインインに関する問題 | Microsoft Docs
description: Azure AD で開発したアプリケーションにサインインできなくなる一般的なエラー
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 68ab777efee3af67cfcdddf1744a36aca0f09571
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366704"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>カスタム開発されたアプリケーションへのサインインに関する問題

いくつかのエラーによってアプリにサインインできなくなる場合があります。 この問題が発生する最大の原因は、アプリの構成ミスです。

## <a name="errors-related-to--misconfigured-apps"></a>アプリの構成ミスに関連するエラー

* ポータルの構成がアプリの構成と一致していることを確認します。 具体的には、クライアント/アプリケーション ID、応答 URL、クライアント シークレット/キー、およびアプリ ID URI を比較します。

* コード内でアクセスを要求しているリソースを、**[Required Resources]**(必要なリソース) タブの構成済みアクセス許可と比較して、自分が構成したリソースのみを要求していることを確認します。

* 類似したエラーや問題については、[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) を参照してください。

## <a name="next-steps"></a>次の手順

[Azure AD 開発者ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[同意およびアプリと Azure AD との統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Azure AD v2.0 収束済みアプリの同意およびアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)
