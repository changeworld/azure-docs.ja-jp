---
title: Azure App Service で組み込み Python イメージを構成する
description: このチュートリアルでは、組み込み Python イメージを使用して、Azure App Service で Python アプリケーションを作成および構成するためのオプションについて説明します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228554"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Azure App Service で組み込み Python イメージを構成する (プレビュー)

この記事では、[Linux 上の App Service](app-service-linux-intro.md) の Python 組み込みイメージを、Python アプリケーションを実行するように構成する方法を示しています。

## <a name="python-version"></a>Python バージョン

Linux 上の App Service の Python ランタイムは、バージョン `python-3.7.0` を使用します。

## <a name="supported-frameworks"></a>サポートされているフレームワーク

`python-3.7` ランタイムと互換性のある Web Server Gateway Interface (WSGI) 準拠のすべてのバージョンの Web フレームワークがサポートされています。

## <a name="package-management"></a>パッケージの管理

Git の発行時に、Kudu エンジンがリポジトリのルートで [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) を探し、`pip` を使用して Azure にパッケージを自動的にインストールします。

発行の前にこのファイルを生成するには、リポジトリのルートに移動し、Python 環境で次のコマンドを実行します。

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Python アプリの構成

App Service の組み込み Python イメージは、[Gunicorn](http://gunicorn.org/) サーバーを使用して Python アプリケーションを実行します。 Gunicorn は、UNIX 用の Python WSGI HTTP サーバーです。 App Service は Gunicorn を自動化し、自動的に Django および Flask プロジェクト用に構成します。

### <a name="django-app"></a>Django アプリ

`wsgi.py` モジュールが含まれている Django プロジェクトを発行すると、Azure は、次のコマンドを使用して自動的に Gunicorn を呼び出します。

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask アプリ

Flask アプリを発行する場合に、エントリ ポイントが `application.py` または `app.py` モジュール内にあると、Azure は、それぞれで次のいずれかのコマンドを使用して、自動的に Gunicorn を呼び出します。

```bash
gunicorn application:app
```

または 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>スタートアップのカスタマイズ

アプリのカスタムのエントリ ポイントを定義するには、最初にカスタムの Gunicorn コマンドを使用して _.txt_ファイルを作成して、プロジェクトのルートに配置します。 たとえば、モジュール _helloworld.py_ と変数 `app` でサーバーを起動するには、次の内容の _startup.txt_ を作成します。

```bash
gunicorn helloworld:app
```

[[アプリケーションの設定]](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) ページで、**[ランタイム スタック]** として **[Python | 3.7]** を選択し、前の手順の **[スタートアップ ファイル]** の名前を入力します。 たとえば、_startup.txt_ です。
