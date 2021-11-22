---
title: Azure Spring Cloud で独自の永続ストレージを有効にする方法 | Microsoft Docs
description: 独自のストレージを永続ストレージとして Azure Spring Cloud に持ち込む方法
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 25af6e4fe8ab2aa097812e504b27e8023ee30fc9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402145"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud で独自の永続ストレージを有効にする方法

**この記事の適用対象:** ✔️ Java ✔️ C#

この記事では、Azure Spring Cloud で独自の永続ストレージを有効にする方法について説明します。

Azure Spring Cloud で組み込みの永続ストレージを使用すると、アプリケーションによって生成された成果物が Azure ストレージ アカウントにアップロードされます。 Microsoft は、これらの成果物の保存時の暗号化ポリシーと有効期間の管理ポリシーを制御します。 

Bring Your Own Storage を使用すると、これらの成果物は、ユーザーが制御するストレージ アカウントにアップロードされます。 つまり、保存時の暗号化ポリシー、有効期間の管理ポリシー、ネットワーク アクセスをユーザーが制御することになります。 ただし、そのストレージ アカウントに関連するコストについては、お客様が責任を負うものとします。

## <a name="prerequisites"></a>前提条件

* 既存の Azure ストレージ アカウントと、事前に作成した Azure ファイル共有。 Azure でストレージ アカウントとファイル共有を作成する方法については、「[Azure ファイル共有を作成する](../storage/files/storage-how-to-create-file-share.md)」を参照してください。
* Azure CLI 用の [Azure Spring Cloud 拡張機能](/cli/azure/azure-cli-extensions-overview)

## <a name="use-the-azure-cli-to-enable-your-own-extra-persistent-storage"></a>Azure CLI を使用して独自の追加永続ストレージを有効にする

次の手順に従って、Azure CLI を使用して独自のストレージを有効にすることができます。

1. 次のコマンドを使用して、Azure ストレージ アカウントを Azure Spring Cloud インスタンスのストレージ リソースとしてバインドします。

    ```azurecli
   az spring-cloud storage add --storage-type StorageAccount --account-name <account-name> --account-key <account-key>  -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
    ```

1. 次のコマンドを使用して、独自の永続ストレージを備えたアプリを作成します。

    ```azurecli
    az spring-cloud app create -n <app-name> -g <resource-group-name> -s <spring-instance-name> --persistent-storage <path-to-JSON-file>
    ```

    create コマンドの `--persistent-storage` パラメーターに渡される JSON ファイルのサンプルを次に示します。

    ```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
    ```

1. 必要に応じて、次のコマンドを使用して、既存のアプリに永続ストレージを追加します。

    ```azurecli
    az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
    ```

1. 必要に応じて、次のコマンドを使用して、特定のストレージ リソースの既存の永続ストレージをすべて一覧表示します。

   ```azurecli
   az spring-cloud storage list-persistent-storage -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
   ```

## <a name="use-best-practices"></a>ベスト プラクティスを使用する

以下のベスト プラクティスは、Azure Spring Cloud に独自の永続ストレージを追加するときに使用します。

- 待ち時間に関する潜在的な問題を回避するには、Azure Spring Cloud インスタンスと Azure ストレージ アカウントを同じ Azure リージョンに配置します。

- Azure ストレージ アカウントでは、使用中のアカウント キーが再生成されないようにします。 ストレージ アカウントには、2 つの異なるキーが含まれています。 段階的なアプローチを使用して、キーの再生成時に、アプリケーションが独自の永続ストレージを引き続き使用できるようにします。 

   たとえば、key1 を使用してストレージ アカウントを Azure Spring Cloud にバインドした場合は、次の手順を使用します。

   1. key2 を再生成します。
   1. 再生成された key2 を使用するように、ストレージ リソースのアカウント キーを更新します。
   1. このストレージ リソースから永続ストレージがマウントされているアプリケーションを再起動します (```az spring-cloud storage list-persistent-storage``` を使用して、関連するすべてのアプリケーションを一覧表示できます)。
   1. key1 を再生成します。

- Azure ストレージ アカウントまたは Azure ファイル共有を削除する場合は、アプリケーション内の対応するストレージ リソースまたは永続ストレージを削除して、エラーが発生しないようにします。

## <a name="faqs"></a>FAQ

Azure Spring Cloud で独自の永続ストレージを使用することに関して、よくあるご質問 (FAQ) を次に示します。

- 組み込みの永続ストレージを有効にした後、独自のストレージを追加の永続ストレージとして有効にした場合、データはストレージ アカウントに移行されますか。

   "*いいえ。ただし、Microsoft では、ご自身で移行を行う際に役立つドキュメントを間もなく提供する予定です。* "

- 予約済みマウント パスとは何ですか。

   "*以下のマウント パスは、Azure Spring Cloud サービスによって予約されています。* "
   - */tmp*
   - */persistent*
   - */secrets*
   - */app-insights/agents*
   - */etc/azure-spring-cloud/certs*
   - */app-insights/agents/settings*
   - */app-lifecycle/settings*

- どのようなマウント オプションを使用できますか。

   "*現在、次のマウント オプションがサポートされています。* "
   - `uid`
   - `gid`
   - `file_mode`
   - `dir_mode`
   
   " *`mountOptions` プロパティは省略可能です。上記のマウント オプションの既定値は、["uid=0", "gid=0", "file_mode=0777", "dir_mode=0777"] です。* "

## <a name="next-steps"></a>次の手順

* [Logback を使用して独自の永続ストレージにログを書き込む方法](./how-to-write-log-to-custom-persistent-storage.md)について学習してください。
* [アプリケーションを手動でスケーリングする](./how-to-scale-manual.md)方法について学習してください。