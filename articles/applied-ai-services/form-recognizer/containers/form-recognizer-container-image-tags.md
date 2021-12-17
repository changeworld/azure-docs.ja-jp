---
title: Form Recognizer イメージのタグとリリース ノート
titleSuffix: Azure Applied AI Services
description: Form Recognizer のコンテナー イメージのタグの全一覧。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Docker, コンテナー, イメージ
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481515"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>Form Recognizer のコンテナー イメージのタグとリリース ノート

> [!IMPORTANT]
>
> * **Form Recognizer v2.1 コンテナー** は、限定的なプレビュー段階にあります。 これらを使用するには、[オンライン リクエスト](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)を送信し、承認を受ける必要があります。 
>
> * オンライン リクエスト フォームでは、Azure サブスクリプション ID を所有する組織に属している有効なメール アドレスを指定する必要があり、ご自身がそのサブスクリプションを所有しているか、そのアクセス権を付与されている必要があります。

## <a name="feature-containers"></a>機能のコンテナー

Form Recognizer 機能は、以下の 7 つのコンテナーによってサポートされています。

| コンテナー名 | イメージの完全修飾名 |
|---|---|
| **レイアウト** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **名刺** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **身分証明書** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **Receipt** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **請求書** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **カスタム API** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **カスタムの教師あり** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Microsoft コンテナー レジストリ

Form Recognizer のコンテナー イメージは、Microsoft が公開しているすべての Docker イメージの主要なレジストリである、Microsoft Container Registry の **mcr.microsoft.** **<span></span>com** コンテナー レジストリ シンジケートにあります。

* MCR の検出エクスペリエンスは [Docker Hub](https://hub.docker.com/publishers/microsoftowner) から提供されます。

* [MCR 内のリポジトリの一覧](https://mcr.microsoft.com/v2/_catalog)を照会することもできます。

## <a name="form-recognizer-tags"></a>Form Recognizer のタグ

Form Recognizer では、次のタグが使用できます。

### <a name="latest-version"></a>[最新バージョン](#tab/current)

`v2.1` のリリース ノート (限定的なプレビュー):

| コンテナー | タグ |
|------------|:------|
| **レイアウト**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **名刺** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **身分証明書** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Receipt**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **請求書**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **カスタム API** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **カスタムの教師あり**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[以前のバージョン](#tab/previous)

> [!IMPORTANT]
> Form Recognizer v1.0 コンテナーは廃止されました。

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Form Recognizer コンテナーのインストールと実行](form-recognizer-container-install-run.md)
>