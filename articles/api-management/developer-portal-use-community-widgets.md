---
title: 開発者ポータルでコミュニティ ウィジェットを使用する
titleSuffix: Azure API Management
description: API Management 開発者ポータルのコミュニティ ウィジェットについて、およびそれらをコードに挿入して使用する方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 3fe751483ea22ff3464cc1fd505b313a5a48ade7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656324"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>開発者ポータルでコミュニティ ウィジェットを使用する

すべての開発者は、自身がコミュニティに提供したウィジェットを API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)の `/community/widgets/` フォルダーに配置します。 それぞれが開発者ポータル チームによって受け入れられています。 ウィジェットは、ポータルの[セルフホステッド バージョン](developer-portal-self-host.md)に挿入することで使用できます。 開発者ポータルのマネージド バージョンでは、現在、コミュニティ ウィジェットはサポートされていません。

> [!NOTE]
> 開発者ポータル チームは、提供されたウィジェットとその依存関係を徹底的に検査します。 ただし、チームは、ウィジェットの読み込みが安全であることを保証することはできません。 コミュニティによって提供されるウィジェットの使用を決定する際には、ご自身で判断してください。 Microsoft の予防手段については、[ウィジェットの提供に関するガイドライン](developer-portal-widget-contribution-guidelines.md#contribution-guidelines)を参照してください。

## <a name="inject-and-use-external-widgets"></a>外部ウィジェットの挿入と使用

1. 開発者ポータルの最新リリース用に[ローカル環境](developer-portal-self-host.md#step-1-set-up-local-environment)を設定します。

1. `/community/widgets` ディレクトリ内のウィジェットのフォルダーにアクセスします。 `readme.md` ファイル内のウィジェットの説明を読み取ります。

1. ポータルのモジュールにウィジェットを登録します。

    1. `src/apim.design.module.ts` - デザイン時の依存関係を登録するモジュール。
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` - 公開時の依存関係を登録するモジュール。
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` - 実行時の依存関係を登録するモジュール。
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. ウィジェットに `npm_dependencies` ファイルがあるかどうかを確認します。

1. ある場合は、ファイルからコマンドをコピーし、リポジトリの最上位ディレクトリで実行します。

    これにより、ウィジェットの依存関係がインストールされます。

1. `npm start` を実行します。

ウィジェットは、ウィジェット セレクターの **[コミュニティ]** カテゴリに表示されます。

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="ウィジェット セレクターのスクリーンショット":::


## <a name="next-steps"></a>次の手順


開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)

- [ウィジェットを提供する](developer-portal-widget-contribution-guidelines.md)
