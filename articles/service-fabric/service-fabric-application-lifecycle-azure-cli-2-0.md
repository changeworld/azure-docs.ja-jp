---
title: "Azure CLI 2.0 を使用して Azure Service Fabric アプリケーションを管理する"
description: "Azure CLI 2.0 を使用して Azure Service Fabric クラスターにアプリケーションをデプロイまたは Azure Service Fabric クラスターからアプリケーションを削除する方法について説明します。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>Azure CLI 2.0 を使用して Azure Service Fabric アプリケーションを管理する

Azure Service Fabric クラスターで実行されているアプリケーションを作成および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure CLI 2.0 をインストールします。 次に、Service Fabric クラスターを選択します。 詳しくは、[Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)に関する記事をご覧ください。

* Service Fabric アプリケーション パッケージをデプロイできるように準備します。 アプリケーションを作成してパッケージ化する方法について詳しくは、[Service Fabric アプリケーション モデル](service-fabric-application-model.md)に関する記事をご覧ください。

## <a name="overview"></a>概要

新しいアプリケーションをデプロイするには、次の手順を実行します。

1. アプリケーション パッケージを Service Fabric イメージ ストアにアップロードします。
2. アプリケーションの種類をプロビジョニングします。
3. アプリケーションを指定して作成します。
4. サービスを指定して作成します。

既存のアプリケーションを削除するには、次の手順を実行します。

1. アプリケーションを削除します。
2. 関連付けられているアプリケーションの種類のプロビジョニングを解除します。
3. イメージ ストアのコンテンツを削除します。

## <a name="deploy-a-new-application"></a>新しいアプリケーションのデプロイ

新しいアプリケーションをデプロイするには、次のタスクを実行します。

### <a name="upload-a-new-application-package-to-the-image-store"></a>新しいアプリケーション パッケージをイメージ ストアにアップロードする

アプリケーションを作成する前に、アプリケーション パッケージを Service Fabric イメージ ストアにアップロードします。 

たとえば、アプリケーション パッケージが `app_package_dir` ディレクトリにある場合は、次のコマンドを使用してディレクトリをアップロードします。

```azurecli
az sf application upload --path ~/app_package_dir
```

大規模なアプリケーション パッケージの場合は、`--show-progress` オプションを指定するとアップロードの進行状況を確認できます。

### <a name="provision-the-application-type"></a>アプリケーションの種類をプロビジョニングする

アップロードが完了したら、アプリケーションをプロビジョニングします。 アプリケーションをプロビジョニングするには、次のコマンドを使用します。

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` の値は、アプリケーション パッケージをアップロードしたディレクトリの名前です。

### <a name="create-an-application-from-an-application-type"></a>アプリケーションの種類からアプリケーションを作成する

アプリケーションがプロビジョニングされたら、次のコマンドを使用して、アプリケーションに名前を付けて作成できます。

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` はアプリケーション インスタンスに対して使用する名前です。 追加のパラメーターは、以前にプロビジョニングしたアプリケーション マニフェストから取得できます。

アプリケーション名の先頭にはプレフィックス `fabric:/` が必要です。

### <a name="create-services-for-the-new-application"></a>新しいアプリケーションのサービスを作成する

アプリケーションを作成したら、そのアプリケーションからサービスを作成します。 次の例では、アプリケーションから新しいステートレス サービスを作成します。 アプリケーションから作成できるサービスは、プロビジョニング済みのアプリケーション パッケージ内のサービス マニフェストで定義されています。

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>アプリケーションのデプロイと正常性を確認する

アプリケーションとサービスが正常にデプロイされていることを確認するには、アプリケーションとサービスが一覧にあることを確認します。

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

アプリケーションを削除するには、次のタスクを実行します。

### <a name="delete-the-application"></a>アプリケーションを削除する

アプリケーションを削除するには、次のコマンドを使用します。

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>アプリケーションの種類のプロビジョニングを解除する

アプリケーションを削除したら、不要になったアプリケーションの種類のプロビジョニングを解除できます。 アプリケーションの種類のプロビジョニングを解除するには、次のコマンドを実行します。

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

指定するアプリケーションの種類の名前とバージョンは、以前にプロビジョニングしたアプリケーション マニフェストにある名前およびバージョンと一致している必要があります。

### <a name="delete-the-application-package"></a>アプリケーション パッケージを削除する

アプリケーションの種類のプロビジョニングを解除したら、不要になったアプリケーション パッケージをイメージ ストアから削除できます。 アプリケーション パッケージを削除すると、ディスク領域が解放されます。 

アプリケーション パッケージをイメージ ストアから削除するには、次のコマンドを使用します。

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` はアプリケーションを作成したときにアップロードしたディレクトリの名前である必要があります。

## <a name="related-articles"></a>関連記事

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

