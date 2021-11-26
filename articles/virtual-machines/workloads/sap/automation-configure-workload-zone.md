---
title: オートメーション フレームワークのワークロード ゾーン構成について
description: Azure 上の SAP デプロイ オートメーション フレームワーク内の SAP ワークロード ゾーン構成プロセスの概要。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 4fd67edf5f6668efa26fd3c6c6f4adf7a3707c24
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725224"
---
# <a name="workload-zone-configuration-in-sap-automation-framework"></a>SAP オートメーション フレームワークのワークロード ゾーン構成

通常、[SAP アプリケーション](automation-deployment-framework.md#sap-concepts)には複数の開発レベルがあります。 たとえば、開発、品質保証、実稼働のレベルがある場合があります。 [Azure 上の SAP デプロイ オートメーション フレームワーク](automation-deployment-framework.md)では、これらのレベルを[ワークロード ゾーン](automation-deployment-framework.md#deployment-components)と呼びます。

## <a name="workload-zone-deployment-configuration"></a>ワークロード ゾーンのデプロイ構成

SAP ワークロード ゾーンの構成は、Terraform tfvars 変数ファイルを使用して行います。

## <a name="terraform-parameters"></a>Terraform パラメーター

次の表に Terraform パラメーターを示します。デプロイ スクリプトを使用しない場合、これらのパラメーターは手動で入力する必要があります。


| 変数                | Type       | 説明                           | 
| ----------------------- | ---------- | ------------------------------------- | 
| `tfstate_resource_id`   | 必須 * | Terraform 状態ファイルが入る SAP ライブラリ内のストレージ アカウントの Azure リソース識別子  |
| `deployer_tfstate_key`  | 必須 * | Deployer の状態ファイルの名前  |

## <a name="generic-parameters"></a>ジェネリック パラメーター

次の表に、リソース グループとリソースの名前付けを定義するパラメーターを示します。


> [!div class="mx-tdCol2BreakAll "]
> | 変数                | 説明                           | Type  |
> | ----------------------- | ------------------------------------- | ----- |
> | `environment`           | ワークロード ゾーンの 5 文字の識別子。 たとえば、運用環境では `PROD`、非運用環境では `NP` です。| 必須 |
> | `location`              | デプロイする Azure リージョン。     | 必須 |
> | `resource_group_name`   | 作成するリソース グループの名前 | オプション |
> | `resource_group_arm_id` | 既存のリソース グループの Azure リソース識別子 | オプション |


## <a name="network-parameters"></a>ネットワーク パラメーター

自動化フレームワークでは、新しい環境のデプロイでの仮想ネットワークとサブネットの作成 (グリーン フィールド) と既存の環境のデプロイでの既存の仮想ネットワークと既存のサブネットの使用 (ブラウン フィールド) が両方ともサポートされ、新しい環境のデプロイと既存の環境のデプロイを組み合わせることもできます。
 - グリーン フィールドのシナリオでは、仮想ネットワークのアドレス空間とサブネット アドレスのプレフィックスを指定する必要があります 
 - ブラウン フィールドのシナリオでは、仮想ネットワークとサブネットの Azure リソース識別子を指定する必要があります

仮想ネットワークのアドレス空間は、すべてのリソースをホストするのに十分な大きさであることを確認してください。

次の表に、ネットワーク パラメーターを示します。

> [!div class="mx-tdCol2BreakAll "]
> | 変数                         | 説明                                                          | Type      | Notes  |
> | -------------------------------- | -------------------------------------------------------------------- | --------- | ------ |
> | `network_name`                   | ネットワークの論理名                                      | 必須  |        |       
> | `network_arm_id`                 | 仮想ネットワークの Azure リソース識別子                | オプション  | 既存の環境のデプロイの場合 |
> | `network_address_space`          | 仮想ネットワークのアドレス範囲                            | Mandatory | 新しい環境のデプロイの場合   |
> | `admin_subnet_name`              | `admin` サブネットの名前                                       | オプション  |         |
> | `admin_subnet_address_prefix`    | `admin` サブネットのアドレス範囲                             | Mandatory | 新しい環境のデプロイの場合  |
> | `admin_subnet_arm_id`            | `admin` サブネットの Azure リソース識別子                 | Mandatory | 既存の環境のデプロイの場合 |
> | `admin_subnet_nsg_name`          | `admin` ネットワーク セキュリティ グループ名の名前                  | オプション   |         |
> | `admin_subnet_nsg_arm_id`        | `admin` ネットワーク セキュリティ グループの Azure リソース識別子 | Mandatory | 既存の環境のデプロイの場合 |
> | `db_subnet_name`                 | `db` サブネットの名前                                          | オプション  |         |
> | `db_subnet_address_prefix`       | `db` サブネットのアドレス範囲                                | Mandatory | 新しい環境のデプロイの場合  |
> | `db_subnet_arm_id`               | `db` サブネットの Azure リソース識別子                    | Mandatory | 既存の環境のデプロイの場合 |
> | `db_subnet_nsg_name`             | `db` ネットワーク セキュリティ グループ名の名前                     | オプション  |          |
> | `db_subnet_nsg_arm_id`           | `db` ネットワーク セキュリティ グループの Azure リソース識別子    | Mandatory | 既存の環境のデプロイの場合 |
> | `app_subnet_name`                | `app` サブネットの名前                                         | オプション  |          |
> | `app_subnet_address_prefix`      | `app` サブネットのアドレス範囲                               | Mandatory | 新しい環境のデプロイの場合  |
> | `app_subnet_arm_id`              | `app` サブネットの Azure リソース識別子                   | Mandatory | 既存の環境のデプロイの場合 |
> | `app_subnet_nsg_name`            | `app` ネットワーク セキュリティ グループ名の名前                    | オプション  |          |
> | `app_subnet_nsg_arm_id`          | `app` ネットワーク セキュリティ グループの Azure リソース識別子   | Mandatory | 既存の環境のデプロイの場合 |
> | `web_subnet_name`                | `web` サブネットの名前                                         | オプション  |          |
> | `web_subnet_address_prefix`      | `web` サブネットのアドレス範囲                               | Mandatory | 新しい環境のデプロイの場合  |
> | `web_subnet_arm_id`              | `web` サブネットの Azure リソース識別子                   | Mandatory | 既存の環境のデプロイの場合 |
> | `web_subnet_nsg_name`            | `web` ネットワーク セキュリティ グループ名の名前                    | オプション  |          |
> | `web_subnet_nsg_arm_id`          | `web` ネットワーク セキュリティ グループの Azure リソース識別子   | Mandatory | 既存の環境のデプロイの場合 |

## <a name="iscsi-parameters"></a>ISCSI パラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                         | 説明                                                               | Type      | Notes                                  |
> | -------------------------------- | ------------------------------------------------------------------------- | --------- | -------------------------------------- |
> | `iscsi_subnet_name`              | `iscsi` サブネットの名前                                            | オプション  |                                        |
> | `iscsi_subnet_address_prefix`    | `iscsi` サブネットのアドレス範囲                                  | Mandatory | 新しい環境のデプロイの場合        |
> | `iscsi_subnet_arm_id`            | `iscsi` サブネットの Azure リソース識別子                      | Mandatory | 既存の環境のデプロイの場合   |
> | `iscsi_subnet_nsg_name`          |  `iscsi` ネットワーク セキュリティ グループ名の名前                      | オプション  |                                        |
> | `iscsi_subnet_nsg_arm_id`        | `iscsi` ネットワーク セキュリティ グループの Azure リソース識別子      | Mandatory | 既存の環境のデプロイの場合   |
> | `iscsi_count`                    | iSCSI 仮想マシンの数                                      | オプション  |                                        |   
> | `iscsi_use_DHCP`                 | Azure サブネットによって提供される動的 IP アドレスを使用するかどうかを制御します | オプション  |                                        |
> | `iscsi_image`                    | 使用する仮想マシン イメージを定義します。以下を参照してください                       | オプション  |                                        |
> | `iscsi_authentication_type`      | ISCSI 仮想マシンの既定の認証を定義します         | オプション  |                                        |
> | `iscsi__authentication_username` | 管理者アカウントの名前                                                | オプション  |                                        |
> | `iscsi_nic_ips`                  | ISCSI 仮想マシンの IP アドレス                               | オプション  | `iscsi_use_DHCP` が定義される場合は無視されます |
 


```python 
{ 
os_type=""
source_image_id=""
publisher="SUSE"
offer="sles-sap-12-sp5"
sku="gen1"
version="latest"
}
```

### <a name="authentication-parameters"></a>認証パラメーター


次の表では、仮想マシンの認証を定義するために使用される資格情報を定義します

> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                          | Type        | 
> | ---------------------------------- | -------------------------------------| ----------- | 
> | `automation_username`              | 管理者アカウントの名前           | オプション    |
> | `automation_password`              | 管理者パスワード               | オプション    |
> | `automation_path_to_public_key`    | 既存の公開キーへのパス          | オプション    |
> | `automation_path_to_private_key`   | 既存の秘密キーへのパス         | オプション    |


## <a name="key-vault-parameters"></a>キー コンテナーのパラメーター

次の表では、キー コンテナーの情報を定義するために使用されるパラメーターを定義します


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                               | Type          | 
> | ---------------------------------- | ------------------------------------------------------------------------- | ------------- | 
> | `user_keyvault_id`                 | システム資格情報のキー コンテナーの Azure リソース識別子            | オプション      | 
> | `spn_keyvault_id`                  | デプロイ資格情報 (SPN) のキー コンテナーの Azure リソース識別子 | オプション      | 


## <a name="dns"></a>DNS


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                    | Type        | 
> | ---------------------------------- | -------------------------------------------------------------- | ----------- | 
> | `dns_label`                        | 指定した場合、プライベート DNS ゾーンの DNS 名です          | オプション    | 
> | `dns_resource_group_name`          | プライベート DNS ゾーンを含むリソース グループの名前 | オプション    | 


## <a name="azure-netapp-support"></a>Azure NetApp のサポート


> [!div class="mx-tdCol2BreakAll "]
> | 変数                           | 説明                                                            | Type         | Notes  |
> | ---------------------------------- | -----------------------------------------------------------------------| -----------  | ------ |
> | `use_ANF`                          | 指定した場合、Azure NetApp Files のアカウントと容量プールをデプロイします | オプション     | |
> | `ANF_account_arm_id`               | Azure NetApp Files アカウントの Azure リソース識別子           | オプション     | 既存の環境のデプロイの場合 |
> | `ANF_account_name`                 | Azure NetApp Files アカウントの名前                                | オプション     | |
> | `ANF_service_level`                | Azure NetApp Files 容量プールのサービス レベル                 | オプション     | |
> | `ANF_pool_size`                    | Azure NetApp Files 容量プールのサイズ (GB 単位)               | オプション     | |
> | `anf_subnet_name`                  | ANF サブネットの名前                                             | オプション     | |
> | `anf_subnet_arm_id`                | `ANF` サブネットの Azure リソース識別子                     | 必須     | 既存の環境のデプロイの場合 |
> | `anf_subnet_address_prefix`        | `ANF` サブネットのアドレス範囲                                 | 必須     | 新しい環境のデプロイの場合  |


## <a name="other-parameters"></a>その他のパラメーター


> [!div class="mx-tdCol2BreakAll "]
> | 変数                             | 説明                                                            | Type     | Notes                                 |
> | ------------------------------------ | ---------------------------------------------------------------------- | -------- | ------------------------------------- |
> | `enable_purge_control_for_keyvaults` | キー コンテナーで消去のコントロールが有効かどうかを制御するブール型のフラグ。 | オプション | テスト デプロイにのみ使用する         |
> | `use_private_endpoint`               | プライベート エンドポイントを使用するかどうかを制御するブール型のフラグ                 | オプション |                                       |
> | `diagnostics_storage_account_arm_id` | 診断ストレージ アカウントの Azure リソース識別子      | 必須 | 既存の環境のデプロイの場合  |
> | `witness_storage_account_arm_id`     | 監視ストレージ アカウントの Azure リソース識別子          | 必須 | 既存の環境のデプロイの場合  |


## <a name="next-step"></a>次の手順

> [!div class="nextstepaction"]
> [オートメーション フレームワークでの SAP システムのデプロイについて](automation-deploy-workload-zone.md)
