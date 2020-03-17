---
title: Spring Boot アプリで動的な構成を使用する
titleSuffix: Azure App Configuration
description: Spring Boot アプリの構成データを動的に更新する方法について説明します
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 6445b9707273d273c562b7d643da34f5ba26e1fc
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967504"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>チュートリアル:Java Spring アプリで動的構成を使用する

App Configuration Spring Boot クライアント ライブラリでは、アプリケーションを再起動せずに、必要に応じて一連の構成設定を更新できます。 クライアント ライブラリでは、構成ストアへの呼び出しが多くなりすぎないようにするため、個々の設定がキャッシュされます。 構成ストア内の値が変更されたとしても、キャッシュされた値の有効期限が切れるまでは、更新操作で値は更新されません。 各要求の既定の有効期限は 30 秒です。 その設定は、必要に応じてオーバーライドできます。

`AppConfigurationRefresh` の `refreshConfigurations()` メソッドを呼び出すことで、更新された設定をオンデマンドでチェックすることができます。

または、`spring-web` に依存する `spring-cloud-azure-appconfiguration-config-web` パッケージを使用して自動更新を処理することもできます。

## <a name="use-automated-refresh"></a>自動更新を使用する

自動更新を使用するためには、App Configuration を使用する Spring Boot アプリから始めてみましょう。たとえば、[App Configuration 用の Spring Boot クイックスタート](quickstart-java-spring-app.md)で作成したアプリが利用できます。

テキスト エディターで *pom.xml* ファイルを開き、`spring-cloud-azure-appconfiguration-config-web` の `<dependency>` を追加します。

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

ファイルを保存し、通常どおりにアプリケーションをビルドして実行します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように Spring Boot アプリを設定しました。 詳細については、「[Azure の Spring](https://docs.microsoft.com/java/azure/spring-framework/)」を参照してください。
