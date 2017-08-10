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
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f4899748ee191a64156c0e2fae87c195ae4dbc8c
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="docker-compose-application-support-in-azure-service-fabric-preview"></a>Azure Service Fabric での Docker Compose アプリケーションのサポート (プレビュー)

Docker は、複数コンテナー アプリケーションの定義に [docker-compose.yml](https://docs.docker.com/compose) ファイルを使用します。 Docker を使用した Azure Service Fabric での既存のコンテナー アプリケーションの調整を理解しやすくするため、プラットフォームでネイティブに Docker Compose をサポートするプレビューを含めています。 Service Fabric は `docker-compose.yml` ファイルのバージョン 3 以降を受け入れることができます。 

このサポートはプレビュー段階であるため、Compose ディレクティブのサブセットのみがサポートされます。 たとえば、アプリケーションのアップグレードはサポートされません。 ただし、アプリケーションをアップグレードする代わりに、いつでも削除してデプロイすることができます。

このプレビューを使用するには、Azure ポータルからプレビュー SDK (バージョン 255.255.x.x) でクラスターを作成します。 

> [!NOTE]
> この機能はプレビュー段階であり、サポートされていません。

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Service Fabric に Docker Compose ファイルをデプロイする

以下のコマンドでは、他の Service Fabric アプリケーションのように監視および管理できる (前の例の `fabric:/TestContainerApp` という名前の) Service Fabric アプリケーションが作成されます。 正常性クエリに指定したアプリケーション名を使用することができます。

### <a name="use-powershell"></a>PowerShell の使用

PowerShell で次のコマンドを実行して、docker-compose.yml ファイルから Service Fabric Compose アプリケーションを作成します。

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` および `RegistryPassword` は、コンテナー レジストリのユーザー名とパスワードです。 アプリケーションが完成したら、次のコマンドを使用して、その状態を確認できます。

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

PowerShell で Compose アプリケーションを削除するには、次のコマンドを使用します。

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

### <a name="use-azure-cli-20"></a>Azure CLI 2.0 の使用

次の Azure CLI コマンドを使用することもできます。

```azurecli
az sf compose create --application-id fabric:/TestContainerApp --compose-file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```

アプリケーションを作成したら、次のコマンドを使用して、その状態を確認できます。

```azurecli
az sf compose status --application-id TestContainerApp [ --timeout ]
```

Compose アプリケーションを削除するには、次のコマンドを使用します。

```azurecli
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

## <a name="supported-compose-directives"></a>サポートされる Compose ディレクティブ

このプレビューでは、以下のプリミティブを含む、Compose バージョン 3 形式からの構成オプションのサブセットがサポートされます。

* サービス > デプロイ > レプリカ
* サービス > デプロイ > 位置 > 制約
* サービス > デプロイ > リソース > 制限
    * -cpu-shares
    * -memory
    * -memory-swap
* サービス > コマンド
* サービス > 環境
* サービス > ポート
* サービス > イメージ
* サービス > 分離 (Windows の場合のみ)
* サービス > ログ > ドライバー
* サービス > ログ > ドライバー > オプション
* ボリューム & デプロイ > ボリューム

[Service Fabric のリソース ガバナンス](service-fabric-resource-governance.md)の説明に従って、リソースの制限を適用するようにクラスターをセットアップします。 他のすべての Docker Compose ディレクティブは、このプレビューではサポートされていません。

## <a name="servicednsname-computation"></a>ServiceDnsName 計算

Compose ファイルで指定したサービス名が完全修飾ドメイン名である (つまり、ドット "." が含まれている) 場合、Service Fabric によって登録される DNS 名は `<ServiceName>` (ドットを含む) となります。 そうでない場合は、アプリケーション名の各パス セグメントが、最上位レベルのドメイン ラベルになる最初のパス セグメントを持つ、サービス DNS 名でのドメイン ラベルになります。

たとえば、指定されたアプリケーション名が `fabric:/SampleApp/MyComposeApp` の場合、登録される DNS 名は `<ServiceName>.MyComposeApp.SampleApp` になります。

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose (インスタンスの定義) と Service Fabric アプリケーション モデル (種類の定義) の相違点

docker-compose.yml ファイルでは、プロパティと構成を含む、コンテナーのデプロイ可能なセットが記述されています。
たとえば、このファイルには環境変数やポートを含めることができます。 位置の制約、リソースの制限、および DNS 名などのデプロイ パラメーターも、docker-compose.yml ファイルで指定できます。

[Service Fabric アプリケーション モデル](service-fabric-application-model.md)は、サービスの種類とアプリケーションの種類を使います。ここでは、同じ種類の多くのアプリケーション インスタンスを持つことができます。 たとえば、顧客ごとに 1 つのアプリケーション インスタンスを使うことができます。 この種類ベースのモデルは、ランタイムに登録されている同じアプリケーションの種類の複数のバージョンをサポートします。

たとえば、顧客 A は AppTypeA の種類 1.0 でインスタンス化されたアプリケーションを持つことができ、顧客 B は同じ種類とバージョンでインスタンス化された別のアプリケーションを持つことができます。 アプリケーション マニフェストでアプリケーションの種類を定義し、アプリケーションの作成時にアプリケーション名とデプロイのパラメーターを指定します。

このモデルは柔軟性がありますが、種類がマニフェスト ファイルから暗黙的に指定されるより単純なインスタンス ベースのデプロイ モデルもサポートする予定です。 このモデルでは、各アプリケーションが独自の独立したマニフェストを取得します。 インスタンス ベースのデプロイ形式である docker-compose.yml のサポートを追加することで、この取り組みをプレビューしています。

## <a name="next-steps"></a>次のステップ

* 「[Service Fabric application model](service-fabric-application-model.md)」 (Service Fabric のアプリケーション モデル) を参照してください。

## <a name="related-articles"></a>関連記事

* [Service Fabric と Azure CLI 2.0 の概要](service-fabric-azure-cli-2-0.md)
* [Service Fabric XPlat CLI の概要](service-fabric-azure-cli.md)

