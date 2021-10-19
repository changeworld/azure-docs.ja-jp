---
title: Web アプリ チュートリアル - Azure API for FHIR をセットアップする
description: このチュートリアルでは、単純な Web アプリケーションをデプロイする例について説明します。 この最初のチュートリアルでは、Azure API for FHIR の前提条件とデプロイについて説明します
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: a29321072c81974c3afc4ab7a1a9894d1a886b90
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785869"
---
# <a name="deploy-javascript-app-to-read-data-from-azure-api-for-fhir"></a>FHIR の Azure API からデータを読み取る JavaScript アプリをデプロイする
このチュートリアルでは、小さな JavaScript アプリをデプロイします。このアプリは、FHIR サービスからデータを読み取ります。 このチュートリアルの手順は、次のとおりです。
1. FHIR サーバーをデプロイする
1. パブリック クライアント アプリケーションを登録する
1. アプリケーションへのアクセスをテストする
1. この FHIR データを読み取る Web アプリケーションを作成する

## <a name="prerequisites"></a>前提条件
このチュートリアルのセットを開始する前に、次の項目を用意する必要があります。
1. Azure サブスクリプション
1. Azure Active Directory テナント
1. インストールされている [Postman](https://www.getpostman.com/)

> [!NOTE]
> このチュートリアルでは、FHIR サービス、Azure AD アプリケーション、および Azure AD ユーザーは、すべて同じ Azure AD テナントにあります。 そうでない場合でも、このチュートリアルに従って作業できますが、いくつかの参照ドキュメントを調べて追加の手順を実行しなければならないことがあります。

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR をデプロイする
チュートリアルの最初の手順は、Azure API for FHIR を正しくセットアップすることです。

1. [Azure API for FHIR](fhir-paas-portal-quickstart.md) をデプロイしていない場合はデプロイします。
1. Azure API for FHIR をデプロイしたら、Azure API for FHIR に移動し、CORS を選択して、[CORS](configure-cross-origin-resource-sharing.md) 設定を構成します。 
    1. **[配信元]** を * に設定します
    1. **[ヘッダー]** を * に設定します
    1. **[メソッド]** で **[すべて選択]** を選択します
    1. **[最長有効期間]** を **600** に設定します

## <a name="next-steps"></a>次の手順
これで、Azure API for FHIR がデプロイされたので、パブリック クライアント アプリケーションを登録する準備ができました。

>[!div class="nextstepaction"]
>[パブリック クライアント アプリケーションを登録する](tutorial-web-app-public-app-reg.md)
