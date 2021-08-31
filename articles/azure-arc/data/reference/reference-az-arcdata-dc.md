---
title: az arcdata dc リファレンス
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc コマンドのリファレンス記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779569"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>コマンド
| command | 説明|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | データ コントローラーを作成します。
[az arcdata dc delete](#az-arcdata-dc-delete) | データ コントローラーを削除します。
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | エンドポイント コマンド。
[az arcdata dc status](reference-az-arcdata-dc-status.md) | 状態コマンド。
[az arcdata dc config](reference-az-arcdata-dc-config.md) | 構成コマンド。
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | データ コントローラーをデバッグします。
[az arcdata dc export](#az-arcdata-dc-export) | メトリック、ログ、使用状況をエクスポートします。
[az arcdata dc upload](#az-arcdata-dc-upload) | エクスポートされたデータ ファイルをアップロードします。
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
データ コントローラーを作成します。システムに Kube 構成と環境変数 ['AZDATA_USERNAME', 'AZDATA_PASSWORD'] が必要です。
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>使用例
データ コントローラーの配置。
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--k8s-namespace -k`
データ コントローラーを配置する Kubernetes 名前空間。 既に存在する場合は、それが使用されます。 存在しない場合は、最初に作成が試みられます。
#### `--name -n`
データ コントローラーの名前。
#### `--connectivity-mode`
データ コントローラーが動作する Azure への間接的または直接的な接続。
#### `--resource-group -g`
データ コントローラー リソースを追加する Azure リソース グループ。
#### `--location -l`
データ コントローラーのメタデータを格納する Azure の場所 (例: eastus)。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--profile-name`
既存の構成プロファイルの名前。 使用できるオプションを表示するには、`az arcdata dc config list` を実行します。 次のいずれかです: ['azure-arc-gke'、'azure-arc-eks'、'azure-arc-kubeadm'、'azure-arc-aks-default-storage'、'azure-arc-azure-openshift'、'azure-arc-ake'、'azure-arc-openshift'、'azure-arc-aks-hci'、'azure-arc-aks-premium-storage']。
#### `--path -p`
使用するカスタム構成プロファイルが格納されているディレクトリへのパス。 カスタム構成プロファイルを作成するには、`az arcdata dc config init` を実行します。
#### `--storage-class`
データとログの永続ボリュームを必要とするすべてのデータ コントローラー ポッドに対するすべてのそのボリュームに使用するストレージ クラス。
#### `--infrastructure`
データ コントローラーが実行されるインフラストラクチャ。 使用可能な値: ['aws'、'gcp'、'azure'、'alibaba'、'onpremises'、'other'、'auto']
#### `--labels`
すべてのデータ コントローラー リソースに適用するラベルのコンマ区切りリスト。
#### `--annotations`
すべてのデータ コントローラー リソースに適用する注釈のコンマ区切りリスト。
#### `--service-annotations`
すべての外部データ コントローラー サービスに適用する注釈のコンマ区切りリスト。
#### `--service-labels`
すべての外部データ コントローラー サービスに適用するラベルのコンマ区切りリスト。
#### `--storage-labels`
データ コントローラーによって作成されるすべての PVC に適用するラベルのコンマ区切りリスト。
#### `--storage-annotations`
データ コントローラーによって作成されるすべての PVC に適用する注釈のコンマ区切りリスト。
#### `--use-k8s`
ローカル Kubernetes API を使用してデータ コントローラーを作成します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
データ コントローラーを削除します。Kube 構成がシステムに必要です。
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>使用例
データ コントローラーの配置。
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--name -n`
データ コントローラー名。
#### `--k8s-namespace -k`
データ コントローラーが存在している Kubernetes 名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--force -f`
データ コントローラーとそのすべてのデータ サービスを強制的に削除します。
#### `--yes -y`
確認プロンプトを表示せずにデータ コントローラーを削除します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
メトリック、ログ、または使用状況をファイルにエクスポートします。
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--type -t`
エクスポートするデータの種類。 オプション: logs、metrics、usage。
#### `--path -p`
エクスポート対象のファイルのファイル名を含む完全パスまたは相対パス。
#### `--k8s-namespace -k`
データ コントローラーが存在している Kubernetes 名前空間。
### <a name="optional-parameters"></a>省略可能のパラメーター
#### `--force -f`
出力ファイルを強制的に作成します。 同じパスにある既存のファイルを上書きします。
#### `--use-k8s`
このアクションを実行するには、ローカル Kubernetes API を使用します。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
データ コントローラーからエクスポートされたデータ ファイルを Azure にアップロードします。
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>必須のパラメーター
#### `--path -p`
アップロード対象のファイルのファイル名を含む完全パスまたは相対パス。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  json、jsonc、none、table、tsv、yaml、yamlc のいずれかの値を指定できます。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org](http://jmespath.org) を参照してください。
#### `--subscription`
サブスクリプションの名前または ID。 `az account set -s NAME_OR_ID` を使用して、既定のサブスクリプションを構成できます。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。
