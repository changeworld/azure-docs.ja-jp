---
title: 'チュートリアル: ゾーン内のリソース レコードを参照する Azure DNS エイリアス レコードを作成する'
description: このチュートリアルでは、ゾーン内のリソース レコードを参照する Azure DNS エイリアス レコードを構成する方法を示します。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990843"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>チュートリアル: ゾーン内のリソース レコードを参照するエイリアス レコードを作成する

エイリアス レコードでは、同じ種類の別のレコード セットを参照できます。 たとえば、DNS の CNAME レコード セットを同じ種類の別の CNAME レコード セットのエイリアスにできます。 これは、動作という意味で、一部のレコード セットをエイリアスにして、一部はエイリアスにしない場合に便利です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ゾーン内のリソース レコードのエイリアス レコードを作成する
> * エイリアス レコードをテストする


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
テスト対象の Azure DNS でホストできる利用可能なドメイン名が必要です。 このドメインのネーム サーバー (NS) レコードを設定するできるなど、このドメインを完全に制御できる必要があります。

Azure DNS にドメインをホストする手順については、「[チュートリアル: Azure DNS にドメインをホストする](dns-delegate-domain-azure-dns.md)」を参照してください。


## <a name="create-an-alias-record"></a>エイリアス レコードを作成する

ゾーン内のリソース レコードを参照するエイリアス レコードを作成します。

### <a name="create-the-target-resource-record"></a>ターゲット リソース レコードを作成する
1. Azure DNS ゾーンをクリックし、ゾーンを開きます。
2. **[レコード セット]** をクリックします。
3. **[名前]** テキスト ボックスに「**server**」と入力します。
4. **[種類]** で **[A]** を選択します。
5. **[IP アドレス]** テキスト ボックスに「**10.10.10.10**」と入力します。
6. Click **OK**.

### <a name="create-the-alias-record"></a>エイリアス レコードを作成する
1. Azure DNS ゾーンをクリックし、ゾーンを開きます。
2. **[レコード セット]** をクリックします。
3. **[名前]** テキスト ボックスに「**test**」と入力します。
4. **[種類]** で **[A]** を選択します。
5. **[エイリアス レコード セット]** チェック ボックスで **[はい]** をクリックし、**[Zone record set]/(ゾーン レコード セット/)** オプションを選択します。
6. **[Zone record set]/(ゾーン レコード セット/)** で、**[server]** レコードを選択します。
7. Click **OK**.

## <a name="test-the-alias-record"></a>エイリアス レコードをテストする

1. お好きな nslookup ツールを起動します。 1 つのオプションとして、[https://network-tools.com/nslook](https://network-tools.com/nslook) を参照します。
2. A レコードに対してクエリの種類を設定し、**\<ドメイン名\> をテスト**します。 回答として **10.10.10.10** を受け取ります。
3. Azure portal で、**server** A レコードを **10.11.11.11** に変更します。
4. 数分待ってから、もう一度 nslookup を使用してレコードを**テスト**します。
5. 回答として **10.11.11.11** を受け取ります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ゾーン内の **server** および **test** リソース レコードが不要になったら、削除できます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、ゾーン内のリソース レコードを参照するエイリアス レコードを作成しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
