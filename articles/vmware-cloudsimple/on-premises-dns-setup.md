---
title: Azure VMware Solutions (AVS) - AVS プライベート クラウド用に DNS を構成する
description: オンプレミスのワークステーションから、AVS プライベート クラウド上の vCenter サーバーにアクセスするための DNS 名前解決を設定する方法について説明する
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 12c4362ae1b075af132d5971f4fe0461c9d91733
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083092"
---
# <a name="configure-dns-for-name-resolution-for-avs-private-cloud-vcenter-access-from-on-premises-workstations"></a>オンプレミスのワークステーションから AVS プライベート クラウド上の vCenter にアクセスできるように名前解決に DNS を構成する

オンプレミスのワークステーションから AVS プライベート クラウド上の vCenter サーバーにアクセスするには、IP アドレスだけでなくホスト名でも vCenter サーバーのアドレスを指定できるよう、DNS アドレス解決を構成する必要があります。

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-avs-private-cloud"></a>AVS プライベート クラウドの DNS サーバーの IP アドレスを取得する

1. [AVS ポータル](access-cloudsimple-portal.md)にサインインします。

2. **[リソース]**  >  **[AVS プライベート クラウド]** に移動し、接続先の AVS プライベート クラウドを選択します。

3. AVS プライベート クラウドの **[Summary]\(概要\)** ページの **[基本情報]** で、AVS プライベート クラウドの DNS サーバーの IP アドレスをコピーします。

    ![AVS プライベート クラウドの DNS サーバー](media/private-cloud-dns-server.png)


DNS の構成には、次のどちらかのオプションを使用します。

* [*.cloudsimple.io に対応するゾーンを DNS サーバー上に作成する](#create-a-zone-on-a-microsoft-windows-dns-server)
* [条件付きフォワーダーをオンプレミスの DNS サーバー上に作成して *.cloudsimple.io を解決する](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>*.cloudsimple.io に対応するゾーンを DNS サーバー上に作成する

スタブ ゾーンとしてゾーンをセットアップし、名前解決のためにプライベート クラウド上の DNS サーバーをポイントすることができます。 ここでは、BIND DNS サーバーまたは Microsoft Windows DNS サーバーの使用に関する情報を提供します。

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS サーバー上にゾーンを作成する

構成する具体的なファイルおよびパラメーターは、個々の DNS セットアップによって異なる場合があります。

たとえば、既定の BIND サーバー構成の場合、DNS サーバー上の /etc/named.conf ファイルを編集し、次のゾーン情報を追加します。

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS サーバー上にゾーンを作成する

1. DNS サーバーを右クリックして **[新しいゾーン]** を選択します。 
  
    ![新しいゾーン](media/DNS01.png)
2. **[スタブ ゾーン]** を選択して **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS02.png)
3. 環境に応じて適切なオプションを選択し、 **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS03.png)
4. **[前方参照ゾーン]** を選択し、 **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS01.png)
5. ゾーン名を入力して **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS05.png)
6. AVS ポータルから取得した、AVS プライベート クラウドの DNS サーバーの IP アドレスを入力します。

    ![新しいゾーン](media/DNS06.png)
7. 必要に応じて **[次へ]** をクリックして、ウィザードのセットアップを完了します。

## <a name="create-a-conditional-forwarder"></a>条件付きフォワーダーの作成

条件付きフォワーダーは、すべての DNS 名前解決要求を指定されたサーバーに転送します。 このセットアップでは、*.cloudsimple.io への要求は、AVS プライベート クラウド上に位置する DNS サーバーに転送されます。 次の例は、さまざまな種類の DNS サーバー上でフォワーダーを設定する方法を示しています。

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>BIND DNS サーバー上に条件付きフォワーダーを作成する

構成する具体的なファイルおよびパラメーターは、個々の DNS セットアップによって異なる場合があります。

たとえば、既定の BIND サーバー構成の場合、DNS サーバー上の /etc/named.conf ファイルを編集し、次の条件付き転送情報を追加します。

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS サーバー上に条件付きフォワーダーを作成する

1. DNS サーバー上で DNS マネージャーを開きます。
2. **[条件付きフォワーダー]** を右クリックし、新しい条件付きフォワーダーを追加するためのオプションを選択します。

    ![条件付きフォワーダー 1 の Windows DNS](media/DNS08.png)
3. AVS プライベート クラウド内の DNS サーバーの DNS ドメインと IP アドレスを入力して **[OK]** をクリックします。
