---
title: Spring Boot ライブラリに変換する
titleSuffix: Azure App Configuration
description: App Configuration Spring Boot ライブラリを古いバージョンから新しいバージョンに変換する方法について説明します。
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553054"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>新しい App Configuration Spring Boot ライブラリに変換する

Spring Boot 用の App Configuration ライブラリの新しいバージョンが使用できるようになりました。 このバージョンでは、プッシュ更新などの新機能が導入されますが、多数の大きな破壊的変更もあります。 これらの変更には、以前のライブラリ バージョンを使用していた構成設定との下位互換性がありません。 以下のトピックについて、

* グループ ID と成果物 ID
* Spring プロファイル
* 構成の読み込みと再読み込み
* 機能フラグの読み込み

この記事では、変更点と、新しいライブラリ バージョンに移行するために必要な操作に関するリファレンスを提供します。

## <a name="group-and-artifact-id-changed"></a>グループ ID と成果物 ID の変更点

すべての Azure Spring Boot ライブラリで、新しい形式に合わせて、グループ ID と成果物 ID が更新されました。 新しいパッケージ名は、次のとおりです。

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Spring プロファイルの使用

前のリリースでは、Spring プロファイルは構成の一部として使用されていたため、構成ファイルの形式と一致させることができました。 たとえば、次のように入力します。

```properties
/<application name>_dev/config.message
```

これが変更され、クエリ内の既定のラベルが次の形式の Spring プロファイルになり、ラベルは Spring プロファイルと一致するようになりました。

```properties
/application/config.message
```

 新しい形式に変換するには、自分のストア名で次のコマンドを実行します。

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

または、ポータルでインポートやエクスポート機能を使用します。

新しいバージョンに完全に移行したら、次のように実行して古いキーを削除できます。

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

このコマンドでは削除しようとしているすべてのキーの一覧が表示されるので、予期しないキーが削除されないように確認できます。 キーは、ポータルで削除することもできます。

## <a name="which-configurations-are-loaded"></a>読み込まれる構成

`/applicaiton/*` と一致する構成が読み込まれる既定のケースは、変更されていません。 変更されたのは、`/${spring.application.name}/*` が、設定されない限りは追加で自動的に使用されなくなったことです。 `/${spring.application.name}/*` を使用するには、代わりに、新しい Selects 構成を使用できます。

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>構成の再読み込み

すべての構成ストアの監視が、既定で無効になりました。 構成ストアで監視を有効にできるように、新しい構成がライブラリに追加されました。 さらに、cache-expiration の名前が refresh-interval に変更され、構成ストアごとに設定できるようにもなりました。 また、構成ストアの監視が有効になっている場合は、少なくとも 1 つの監視対象キーを構成する必要があります。オプションで、ラベルを付けることができます。

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

refresh-interval の動作に変更はありません。変更点は、構成の名前が機能を明示するようにしたことです。 監視対象キーの要件により、ライブラリは、構成が変更されているときに、すべての変更が完了するまで構成の読み込みを試みないことが保証されます。

## <a name="feature-flag-loading"></a>機能フラグの読み込み

既定では、機能フラグの読み込みが無効になりました。 さらに、機能フラグにラベル フィルターと refresh-interval を設定できるようになりました。

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```
