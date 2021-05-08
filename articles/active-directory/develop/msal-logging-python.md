---
title: MSAL for Python でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL for Python でエラーと例外をログに記録する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578755"
---
# <a name="logging-in-msal-for-python"></a>Python 用の MSAL でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL for Python でのログ記録

MSAL for Python でのログ記録では、[Python 標準ライブラリのログ モジュール](https://docs.python.org/3/library/logging.html)が使用されます。 次のように MSAL ログを構成できます ([username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) で動作を確認できます)。

### <a name="enable-debug-logging-for-all-modules"></a>すべてのモジュールのデバッグ ログを有効にする

既定では、すべての Python スクリプトでログ記録は無効になっています。 スクリプト内の **すべて** の Python モジュールの詳細ログ記録を有効にする場合は、`logging.basicConfig` を `logging.DEBUG` のレベルで使用します。

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

これにより、ログ モジュールに渡されるすべてのログ メッセージが標準出力に出力されます。

### <a name="configure-msal-logging-level"></a>MSAL ログ レベルの構成

次のようにロガー名 `"msal"` を指定して `logging.getLogger()` メソッドを使用することにより、MSAL for Python ログ プロバイダーのログ レベルを構成できます。

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Azure アプリ Insights を使用して MSAL ログを構成する

Python のログは、既定では `StreamHandler` というログ ハンドラーに渡されます。 インストルメンテーション キーを使用して Application Insights に MSAL ログを送信するには、`opencensus-ext-azure` ライブラリによって提供される `AzureLogHandler` を使用します。

`opencensus-ext-azure` をインストールするには、PyPI から依存関係または pip install に `opencensus-ext-azure` パッケージを追加します。

```console
pip install opencensus-ext-azure
```

次に、`APP_INSIGHTS_KEY` 環境変数に設定されているインストルメンテーション キーを使用して、`"msal"` ログ プロバイダーの既定のハンドラーを `AzureLogHandler` のインスタンスに変更します。

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Python での個人と組織のデータ

MSAL for Python では、個人データおよび組織データはログに記録されません。 個人または組織のデータのログ記録を有効または無効にするプロパティはありません。

標準的な Python ログ記録を使用して任意のログを記録することができますが、機密データを安全に処理し、規制要件に従う責任があります。

Python でのログ記録の詳細については、Python の「[Logging: how-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)」を参照してください。

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。
