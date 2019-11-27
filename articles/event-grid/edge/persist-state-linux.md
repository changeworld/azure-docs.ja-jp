---
title: Linux で状態を永続化する - Azure Event Grid IoT Edge | Microsoft Docs
description: Linux でメタデータを永続化します
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100329"
---
# <a name="persist-state-in-linux"></a>Linux で状態を永続化する

Event Grid モジュールで作成したトピックとサブスクリプションは、既定でコンテナー ファイル システムに格納されます。 作成したメタデータが永続化されていない場合、モジュールを再デプロイするとすべて失われます。 現在、メタデータのみが永続化されます。 イベントはメモリ内に格納されます。 Event Grid モジュールを再デプロイまたは再起動した場合、配信されていないイベントはすべて失われます。

この記事では、Linux デプロイで永続化を使用して Event Grid モジュールをデプロイする手順について説明します。

> [!NOTE]
>Event Grid モジュールは、UID `2000` と名前 `eventgriduser` を備えた低特権のユーザーとして実行されます。

## <a name="persistence-via-volume-mount"></a>ボリューム マウントによる永続化

 [Docker ボリューム](https://docs.docker.com/storage/volumes/)を使用して、デプロイ間でデータを保持します。 Event Grid モジュールをデプロイする際に、Docker で自動的に名前付きボリュームを作成することができます。 このオプションは、最も簡単なオプションです。 次のように、**Binds** セクションで、作成するボリュームの名前を指定できます。

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>バインド値の 2 番目の部分は変更しないでください。 これはモジュール内の特定の場所を指します。 Linux の Event Grid モジュールでは、これは **/app/metadataDb** である必要があります。

たとえば次の構成では、**egmetadataDbVol** というボリュームが作成され、ここでメタデータが永続化されます。

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

または、Docker クライアント コマンドを使用して Docker ボリュームを作成することもできます。 

## <a name="persistence-via-host-directory-mount"></a>ホスト ディレクトリ マウントによる永続化

Docker ボリュームの代わりに、ホスト フォルダーをマウントするオプションもあります。

1. まず、次のコマンドを実行して、名前が **eventgriduser**、ID が **2000** というユーザーをホスト マシン上に作成します。

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 次のコマンドを実行して、ホスト ファイル システム上にディレクトリを作成します。

   ```sh
   md <your-directory-name-here>
   ```

    たとえば、次のコマンドを実行すると、**myhostdir** という名前のディレクトリが作成されます。

    ```sh
    md /myhostdir
    ```
1. 次に、下のコマンドを実行して、**eventgriduser** をこのフォルダーの所有者にします。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    たとえば、次のように入力します。

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **Binds** を使用してディレクトリをマウントし、Azure portal から Event Grid モジュールを再デプロイします。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    たとえば、次のように入力します。

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >バインド値の 2 番目の部分は変更しないでください。 これはモジュール内の特定の場所を指します。 Linux の Event Grid モジュールでは、これは **/app/metadata** である必要があります。
