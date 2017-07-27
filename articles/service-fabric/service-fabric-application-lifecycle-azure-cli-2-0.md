---
title: "Azure CLI 2.0 を使用した Service Fabric アプリケーションの管理"
description: "Azure CLI 2.0 を使用して Service Fabric クラスターからアプリケーションをデプロイおよび削除するプロセスについて説明します。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Azure CLI 2.0 を使用した Service Fabric アプリケーションの管理

Service Fabric クラスターで実行されているアプリケーションを作成および削除するには、このドキュメントに従います。

## <a name="prerequisites"></a>前提条件

Azure CLI 2.0 がインストールされ、Service Fabric クラスターが選択されている必要があります。 詳細については、[Azure CLI 2.0 の概要に関するドキュメント](service-fabric-azure-cli-2-0.md)を参照してください。

また、Service Fabric アプリケーション パッケージをデプロイする準備ができている必要もあります。 アプリケーションを作成してパッケージ化する方法の詳細については、[アプリケーションのモデル化に関するドキュメント](service-fabric-application-model.md)を参照してください。

## <a name="overview"></a>概要

新しいアプリケーションをデプロイするには、次の 4 つの手順を行います。

1. アプリケーション パッケージを Service Fabric イメージ ストアにアップロードする
1. アプリケーションの種類をプロビジョニングする
1. アプリケーションを指定して作成する
1. サービスを指定して作成する

既存のアプリケーションを削除するには、次の 3 つの手順が必要です。

1. Delete application
1. 関連付けられているアプリケーションの種類のプロビジョニングを解除する
1. イメージ ストアのコンテンツを削除する

## <a name="deploy-a-new-application"></a>新しいアプリケーションのデプロイ

アプリケーションをデプロイするには、次の手順に従います。

### <a name="upload-a-new-application-package-to-the-image-store"></a>新しいアプリケーション パッケージをイメージ ストアにアップロードする

アプリケーションを作成する前に、アプリケーション パッケージを Service Fabric イメージ ストアにアップロードする必要があります。
たとえば、アプリケーション パッケージが `app_package_dir` ディレクトリ内に存在する場合は、 次のコマンドを使用してこのディレクトリをアップロードします。

```azurecli
az sf application upload --path ~/app_package_dir
```

大規模なアプリケーション パッケージの場合は、`--show-progress` オプションを指定するとアップロードの進行状況を確認できます。

### <a name="provision-application-type"></a>アプリケーションの種類をプロビジョニングする

アップロードが完了したら、アプリケーションをプロビジョニングする必要があります。 アプリケーションをプロビジョニングするには、次のコマンドを使用します。

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` には、先ほどアップロードしたアプリケーション パッケージを含むディレクトリと同じディレクトリ名を指定します。

### <a name="create-application-from-application-type"></a>アプリケーションの種類からアプリケーションを作成する

アプリケーションがプロビジョニングされたら、次のコマンドを使用して、任意の名前でアプリケーションを作成できます。

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` には、アプリケーションのインスタンスに付ける名前を指定します。 その他のパラメーターは、プロビジョニング済みのアプリケーション マニフェストで確認できます。

アプリケーション名の先頭には `fabric:/` プレフィックスが必要です。

### <a name="create-services-for-the-new-application"></a>新しいアプリケーションのサービスを作成する

アプリケーションを作成したら、そのアプリケーションからサービスを作成できます。 この例では、アプリケーションから新しいステートレス サービスを作成します。 アプリケーションから作成できるサービスは、プロビジョニング済みのアプリケーション パッケージ内のサービス マニフェストで定義されています。

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>アプリケーションの作成と正常性の確認

アプリケーションとサービスが正常にデプロイされていることを確認するには、次のコマンドを使用して、アプリケーションとサービスがリストにあることを確認します。

```azurecli
az sf application list
az sf service list --application-list TestApp
```

サービスが正常な状態にあることを確認するには、同様のコマンドを使用して、サービスとアプリケーションの両方の正常性を取得します。

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

サービスとアプリケーションが正常な状態であれば、`HealthState` の値が `Ok` になっています。

## <a name="remove-an-existing-application"></a>既存のアプリケーションの削除

アプリケーションを削除するには、次の手順に従います。

### <a name="delete-the-application"></a>アプリケーションを削除する

次のコマンドを実行して、アプリケーションを削除します。

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>アプリケーションの種類のプロビジョニングを解除する

アプリケーションが削除されたら、不要になったアプリケーションの種類のプロビジョニングを解除できます。 次のコマンドを使用して、アプリケーションのプロビジョニングを解除します。

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

ここで指定するアプリケーションの種類の名前とバージョンは、プロビジョニング済みのアプリケーション マニフェストにある名前とバージョンと一致している必要があります。

### <a name="delete-application-package"></a>アプリケーション パッケージを削除する

アプリケーションの種類のプロビジョニングが解除されたら、不要になったアプリケーション パッケージをイメージ ストアから削除することができます。 アプリケーション パッケージを削除すると、ディスク領域が解放されます。 次のコマンドを使用して、アプリケーション パッケージをイメージ ストアから削除します。

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` は、アプリケーションの作成時にアップロードしたディレクトリと同じ名前である必要があります。

## <a name="related-articles"></a>関連記事

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

