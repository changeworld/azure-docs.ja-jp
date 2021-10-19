---
title: Azure Cache for Redis で Redis のバージョンを設定する
description: Redis のバージョンを構成する方法について説明します
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728218"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>Azure Cache for Redis で Redis のバージョンを設定する
この記事では、キャッシュ インスタンスで使用する Redis ソフトウェアのバージョンを構成する方法について説明します。 Azure Cache for Redis には、Redis の最新メジャー バージョンとして、少なくとも 1 つ前のバージョンが用意されています。 新しい Redis ソフトウェアがリリースされると、これらのバージョンは定期的に更新されます。 使用可能な 2 つのバージョンからいずれかを選択できます。 現在使用しているバージョンがサポートされなくなった場合は、キャッシュが自動的に次のバージョンにアップグレードされるため、注意してください。

> [!NOTE]
> 現在 Redis 6 では、ACL と、Redis 4 と 6 のキャッシュ間の geo レプリケーションをサポートしていません。
>

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>Azure portal を使用してキャッシュを作成する
キャッシュを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[リソースの作成]** を選択します。
  
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="[Azure Cache for Redis] を選択します。":::
   
1. **[基本]** ページで、新しいキャッシュの設定を構成します。
   
    | 設定      | 推奨値  | 説明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | サブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
    | **リソース グループ** | リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
    | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
    | **場所** | 場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **キャッシュの種類** | [キャッシュのレベルとサイズ](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |
   
1. **[詳細設定]** ページで、使用する Redis のバージョンを選択します。
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis のバージョン。":::

1. **［作成］** を選択します 
   
    キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。


## <a name="create-a-cache-using-azure-powershell"></a>Azure PowerShell を使用してキャッシュを作成する

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
Azure PowerShell を使用して Azure Cache for Redis を管理する方法の詳細については、[こちら](cache-how-to-manage-redis-cache-powershell.md)を参照してください

## <a name="create-a-cache-using-azure-cli"></a>Azure CLI を使用してキャッシュを作成する

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
Azure CLI を使用して Azure Cache for Redis を管理する方法の詳細については、[こちら](cli-samples.md)を参照してください

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>既存の Redis 4 キャッシュを Redis 6 にアップグレードする
Azure Cache for Redis では、Redis キャッシュ サーバーの Redis 4 から Redis 6 へのメジャー バージョンのアップグレードをサポートしています。 アップグレードは永続的であり、接続が一時的に増加する可能性があることに注意してください。 予防の手順として、アップグレードする前に、既存の Redis 4 キャッシュからデータをエクスポートし、より低い環境で Redis 6 キャッシュを使用してクライアント アプリケーションをテストすることをお勧めします。 エクスポートする方法の詳細については、[こちら](cache-how-to-import-export-data.md)を参照してください。

> [!NOTE]
> geo レプリケーション リンクのあるキャッシュではアップグレードはサポートされないため、アップグレードする前に、キャッシュ インスタンスのリンクを手動で解除する必要があります。 
>

キャッシュをアップグレードするには、次の手順を実行します。

1. Azure portal で、**Azure Cache for Redis** を検索します。 その後、Enter キーを押すか、検索候補から選択してください。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Azure Cache for Redis を検索する。":::

1. Redis 4 から Redis 6 にアップグレードするキャッシュ インスタンスを選択します。

1. 画面の左側で、 **[詳細設定]** を選択します。 

1. キャッシュ インスタンスがアップグレードに適格である場合は、次の青いバナーが表示されます。 続行する場合は、バナー内のテキストを選択します。

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="開発者の生産性や使いやすさを向上させる機能やコマンドが追加された Redis 6 キャッシュにアップグレードできることを通知する青いバナー。キャッシュ インスタンスのアップグレードを元に戻すことはできません｡":::
    
1. アップグレードは永続的で、接続が一時的に増加する可能性があることを通知するダイアログ ボックスがポップアップ表示されます。 キャッシュ インスタンスをアップグレードする場合は、[はい] を選択します。

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="キャッシュのアップグレードに関する詳細情報が表示されたダイアログ。":::

1. アップグレードの状態を確認するには、 **[概要]** に移動します。

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="概要には、アップグレード中のキャッシュの状態が表示されます。":::

## <a name="faq"></a>よく寄せられる質問

### <a name="what-features-arent-supported-with-redis-6"></a>Redis 6 でサポートされない機能は何ですか。

現在 Redis 6 では、ACL と、Redis 4 と 6 のキャッシュ間の geo レプリケーションをサポートしていません。

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>作成後にキャッシュのバージョンを変更できますか。

既存の Redis 4 キャッシュを Redis 6 にアップグレードすることができます。詳細については、[こちら](#upgrade-an-existing-redis-4-cache-to-redis-6)を参照してください。 キャッシュ インスタンスのアップグレードは永続的であり、Redis 6 のキャッシュを Redis 4 のキャッシュにダウングレードできないことに注意してください。

## <a name="next-steps"></a>次の手順

- Redis 6 の機能の詳細については、[Redis による Redis 6.0 の概要](https://redis.com/blog/diving-into-redis-6/)に関するページを参照してください
- Azure Cache for Redis の機能について学習するには:

> [!div class="nextstepaction"]
> [Azure Cache for Redis Premium サービス レベル](cache-overview.md#service-tiers)
