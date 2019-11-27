---
title: Windows で状態を永続化する - Azure Event Grid IoT Edge | Microsoft Docs
description: Windows で状態を永続化する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991764"
---
# <a name="persist-state-in-windows"></a>Windows で状態を永続化する

Event Grid モジュールで作成したトピックとサブスクリプションは、既定でコンテナー ファイル システムに格納されます。 永続化を行わないと、モジュールが再デプロイされた場合、作成したすべてのメタデータが失われます。 複数のデプロイにわたってデータを保持するには、コンテナー ファイル システムの外部にデータを永続化する必要があります。 現在、メタデータのみが永続化されます。 イベントはメモリ内に格納されます。 Event Grid モジュールを再デプロイまたは再起動した場合、配信されていないイベントはすべて失われます。

この記事では、Windows デプロイで永続化を使用して Event Grid モジュールをデプロイするために必要な手順について説明します。

> [!NOTE]
>Event Grid モジュールは、Windows では低特権のユーザー **ContainerUser** として実行されます。

## <a name="persistence-via-volume-mount"></a>ボリューム マウントによる永続化

複数のデプロイにわたってデータを保持するために、[Docker ボリューム](https://docs.docker.com/storage/volumes/)が使用されます。 ボリュームをマウントするには、それを docker コマンドを使用して作成し、コンテナーが読み取りと書き込みをできるようにアクセス許可を付与してから、モジュールをデプロイする必要があります。 Windows では、必要なアクセス許可を持つボリュームを自動的に作成するプロビジョニングはありません。 デプロイする前に作成する必要があります。

1. 次のコマンドを実行して、ボリュームを作成します。

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    たとえば、次のように入力します。

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 次のコマンドを実行して、ボリュームがマップされているホスト ディレクトリを取得します

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    たとえば、次のように入力します。

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   サンプル出力:

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 次のように、**Users** グループを **Mountpoint** によって示されている値に追加します。
    1. エクスプローラーを起動します。
    1. **Mountpoint** によって示されているフォルダーに移動します。
    1. 右クリックし、 **[プロパティ]** を選択します。
    1. **[セキュリティ]** を選択します。
    1. [グループ名またはユーザー名] の下の **[編集]** を選択します。
    1. **[追加]** を選択し、「`Users`」と入力します。 **[名前の確認]** を選択し、 **[OK]** を選択します。
    1. *[Users のアクセス許可]* で、 **[変更]** を選択し、 **[OK]** を選択します。
1. **Binds** を使用してこのボリュームをマウントし、Azure portal から Event Grid モジュールを再デプロイします

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >バインド値の 2 番目の部分は変更しないでください。 これは、モジュール内の特定の場所を指します。 Windows 上の Event Grid モジュールの場合は、**C:\\app\\metadataDb** である必要があります。


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>ホスト ディレクトリ マウントによる永続化

代替方法として、ホスト システム上にディレクトリを作成し、そのディレクトリをマウントすることもできます。

1. 次のコマンドを実行して、ホスト ファイル システム上にディレクトリを作成します。

   ```sh
   mkdir <your-directory-name-here>
   ```

   たとえば、次のように入力します。

   ```sh
   mkdir C:\myhostdir
   ```
1. **Binds** を使用してディレクトリをマウントし、Azure portal から Event Grid モジュールを再デプロイします。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >バインド値の 2 番目の部分は変更しないでください。 これは、モジュール内の特定の場所を指します。 Windows 上の Event Grid モジュールの場合は、**C:\\app\\metadataDb** である必要があります。

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
