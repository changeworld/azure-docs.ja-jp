---
title: Azure Static Web Apps とは
description: Azure Static Web Apps の主な特徴と機能。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 5a335525bef3509f547bbccc478ec9402ad05e28
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109815056"
---
# <a name="what-is-azure-static-web-apps"></a>Azure Static Web Apps とは

Azure Static Web Apps は、コード リポジトリから Azure にフル スタックの Web アプリを自動的にビルドしてデプロイするサービスです。

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Azure Static Web Apps の概要図":::

Azure Static Web Apps のワークフローは、開発者の日常のワークフローに合わせて調整されています。 アプリは、コードの変更に基づいてビルドおよびデプロイされます。

Azure Static Web Apps リソースを作成すると、Azure は GitHub または Azure DevOps と直接やり取りして、選択したブランチを監視します。 コミットをプッシュするたび、または監視対象のブランチへの pull request を受け入れるたびに、自動的にビルドが実行されて、自分のアプリと API が Azure にデプロイされます。

通常、静的 Web アプリは、Angular、React、Svelte、Vue、Blazor など、サーバー側のレンダリングが不要なライブラリとフレームワークを使用して構築されます。 これらのアプリには、アプリケーションを構成する HTML、CSS、JavaScript、およびイメージ アセットが含まれます。 従来の Web サーバーでは、これらのアセットは、必要な API エンドポイントと共に 1 台のサーバーから提供されます。

Static Web Apps では、静的アセットは従来の Web サーバーから分離され、世界各地の地理的に分散したポイントから提供されます。 この分散により、ファイルがエンド ユーザーに物理的に近づくため、ファイルの提供が大幅に高速になります。 さらに、API エンドポイントは[サーバーレス アーキテクチャ](../azure-functions/functions-overview.md)を使用してホストされます。これにより、完全なバックエンド サーバーが不要になります。

## <a name="key-features"></a>主要な機能

- HTML、CSS、JavaScript、画像などの静的コンテンツの **Web ホスティング**。
- 標準アカウントを使用して既存の Azure Functions アプリをリンクするオプションを備えた Azure Functions によって提供される **統合 API** サポート。
- リポジトリの変更によってビルドとデプロイがトリガーされる **ファーストクラスの GitHub 統合および Azure DevOps 統合**。
- コンテンツをユーザーの近くに配置する、**グローバルに分散** された静的コンテンツ。
- 自動的に更新される **無料の SSL 証明書**。
- ブランド化されたカスタマイズをアプリに提供する **カスタム ドメイン**。
- API の呼び出し時にリバースプロキシを使用する **シームレスなセキュリティ モデル**。CORS 構成は必要ありません。
- Azure Active Directory、GitHub、および Twitter との **認証プロバイダーの統合**。
- **カスタマイズ可能な認可ロールの定義** と割り当て。
- 提供するコンテンツとルートを完全に制御できるようにする **バックエンド ルーティング規則**。
- 発行前にサイトのプレビュー バージョンを有効にする pull request を使用した **生成済みステージング バージョン**。

## <a name="what-you-can-do-with-static-web-apps"></a>Static Web Apps でできること

- [Azure Functions](apis.md) バックエンドと共に [Angular](getting-started.md?tabs=angular)、[React](getting-started.md?tabs=react)、[Svelte](/learn/modules/publish-app-service-static-web-app-api/)、[Vue](getting-started.md?tabs=vue) などの JavaScript フレームワークとライブラリを使用した **最新の Web アプリケーションの構築**、または [Blazor](./deploy-blazor.md) を使用した WebAssembly アプリケーションの作成。
- [Gatsby](publish-gatsby.md)、[Hugo](publish-hugo.md)、[VuePress](publish-vuepress.md) などのフレームワークを使用した **静的サイトの発行**。
- [Next.js](deploy-nextjs.md) や [Nuxt.js](deploy-nuxtjs.md) などのフレームワークを使用した **Web アプリケーションのデプロイ**。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の静的アプリを構築する](getting-started.md)
