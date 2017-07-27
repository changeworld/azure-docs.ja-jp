---
title: "Azure Service Fabric Docker Compose プレビュー"
description: "Azure Service Fabric では、Service Fabric を使用して既存のコンテナーの調整を容易にするため、Docker Compose 形式を受け入れます。 このサポートは現在プレビューの段階です。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 91b6e98df5df98bb557d7fac0475354322d95640
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---
# <a name="compose-application-support-in-service-fabric-preview"></a>Service Fabric での Compose アプリケーションのサポート (プレビュー)

Docker は、複数コンテナー アプリケーションの定義に [docker-compose.yml](https://docs.docker.com/compose) ファイルを使用します。
Docker を使用した Service Fabric での既存のコンテナー アプリケーションの調整を理解しやすくするため、プラットフォームでネイティブに Docker Compose をサポートするプレビューを含めています。 Service Fabric は `docker-compose.yml` ファイルのバージョン 3(+) を受け入れることができます。 このサポートはプレビューであるため、Compose ディレクティブのサブセットのみがサポートされます。 たとえば、アプリケーションのアップグレードはサポートされません。 ただし、アプリケーションをアップグレードする代わりに、いつでも削除してデプロイすることができます。

このプレビューを使用するには、ポータルからプレビュー SDK (バージョン 255.255.x.x) でクラスターを作成する必要があります。 

> [!NOTE]
> この機能はプレビューであり、サポートされていません。

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Service Fabric に Docker Compose ファイルをデプロイする

以下のコマンドでは、他の Service Fabric アプリケーションと同じように監視および管理できる Service Fabric アプリケーション (前の例で名前を付けた `fabric:/TestContainerApp`) が作成されます。 指定したアプリケーション名は正常性クエリに使うことができます。

### <a name="using-powershell"></a>PowerShell の使用

PS で次のコマンドを実行して、docker-compose.yml ファイルから Service Fabric Compose アプリケーションを作成します。

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

`RepositoryUserName` および `RepoistoryPassword` は、コンテナー レジストリのユーザー名とパスワードです。 アプリケーションが完了したら、次のコマンドを使ってアプリケーションの状態を確認することができます。

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

PS で Compose アプリケーションを削除するには、次のコマンドを使います。

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="using-azure-cli-20"></a>Azure CLI 2.0 の使用

または、次の Azure CLI コマンドを使います。

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

アプリケーションが作成されたら、次のコマンドを使ってアプリケーションの状態を確認できます。

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

Compose アプリケーションを削除するには、次のコマンドを使います。

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>サポートされる Compose ディレクティブ

このプレビューでは、Compose V3 形式からの構成オプションのサブセットがサポートされています。 次のプリミティブがサポートされています。

* サービス -> デプロイ -> レプリカ
* サービス -> デプロイ -> 位置 -> 制約
* サービス -> デプロイ -> リソース -> 制限
*         -cpu-shares
*         -memory
*         -memory-swap
* サービス -> コマンド
* サービス -> 環境
* サービス -> ポート
* サービス -> イメージ
* サービス -> 分離 (Windows のみ)
* サービス -> ログ -> ドライバー
* サービス -> ログ -> ドライバー -> オプション
* ボリューム & デプロイ -> ボリューム

クラスターは、[Service Fabric のリソース ガバナンス](service-fabric-resource-governance.md)の説明に従って、リソースの制限を適用するように設定する必要があります。 その他の Docker Compose ディレクティブは、いずれもこのプレビューではサポートされていません。

## <a name="servicednsname-computation"></a>ServiceDnsName 計算

Compose ファイルで指定されたサービス名が完全修飾ドメイン名 (つまり、ピリオド '.' が含まれている) の場合、Service Fabric によって登録された DNS 名はピリオドを含む `<ServiceName>` です。 そうでない場合は、ApplicationName の各パス セグメントが、最上位レベルのドメイン ラベルになる最初のパス セグメントを持つサービス DNS 名でのドメイン ラベルになります。

たとえば、`fabric:/SampleApp/MyComposeApp` というアプリケーション名を指定すると、登録される DNS 名は `<ServiceName>.MyComposeApp.SampleApp` になります。

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose (インスタンスの定義) と Service Fabric アプリケーション モデル (種類の定義) の相違点

docker-compose.yml ファイルでは、プロパティと構成を含むコンテナーのデプロイ可能なセットが記述されています。
たとえば、このファイルには環境変数やポートを含めることができます。 位置の制約、リソースの制限、および DNS 名などのデプロイ パラメーターも、docker-compose.yml ファイルで指定できます。

[Service Fabric アプリケーション モデル](service-fabric-application-model.md)は、サービスの種類とアプリケーションの種類を使います。ここでは、同じ種類の多くのアプリケーション インスタンスを持つことができます。 たとえば、顧客ごとに 1 つのアプリケーション インスタンスを使うことができます。 この種類ベースのモデルは、ランタイムに登録されている同じアプリケーションの種類の複数のバージョンをサポートします。
たとえば、顧客 A は AppTypeA の種類 1.0 でインスタンス化されたアプリケーションを持つことができ、顧客 B は同じ種類とバージョンでインスタンス化された別のアプリケーションを持つことができます。 アプリケーションの種類はアプリケーション マニフェストで定義され、アプリケーション名とデプロイのパラメーターはアプリケーションの作成時に指定されます。

このモデルは柔軟性がありますが、種類がマニフェスト ファイルから暗黙的に指定されるより単純なインスタンス ベースのデプロイ モデルもサポートする予定です。 このモデルでは、各アプリケーションが独自の独立したマニフェストを取得します。 インスタンス ベースのデプロイ形式である docker-compose.yml のサポートを追加することで、この取り組みをプレビューしています。

## <a name="next-steps"></a>次のステップ

* 「[Service Fabric application model](service-fabric-application-model.md)」 (Service Fabric のアプリケーション モデル) を参照してください。

## <a name="related-articles"></a>関連記事:

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

