---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543561"
---
| プロパティ | [説明] | 例 | 必須 |
|---|---|---|---|
| `app_location` | アプリケーション コードの場所です。 | アプリケーションのソース コードがリポジトリのルートにある場合は「`/`」を入力し、アプリケーション コードが `app` という名前のディレクトリにある場合は「`/app`」を入力します。 | はい |
| `api_location` | Azure Functions コードの場所です。 | アプリ コードが `api` という名前のフォルダーにある場合は、「`/api`」を入力します。 フォルダー内で Azure Functions アプリが検出されない場合、ビルドは失敗せず、API が必要とされていないことがワークフローで想定されます。 | いいえ |
| `output_location` | `app_location` を基準としたビルド出力ディレクトリの場所です。 | アプリケーションのソース コードが `/app` にあり、ビルド スクリプトによってファイルが `/app/build` フォルダーに出力される場合、`build` を値 `output_location` として設定します。 | いいえ |