---
title: '方法: アプリケーションによってサポートされるアカウントの種類を変更する | Azure'
titleSuffix: Microsoft identity platform
description: このハウツー記事では、アプリケーションにアクセスできるユーザー (アカウント) を変更する目的で Microsoft ID プラットフォームに登録されているアプリケーションを構成します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079793"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>アプリケーションでサポートされているアカウントを変更する方法

アプリケーションを Microsoft ID プラットフォームに登録するときに、アプリケーションにアクセスできるユーザー (アカウントの種類) を指定しました。 たとえば、組織内のアカウントを指定した場合、それは "*シングルテナント*" アプリです。 また、(自分の組織を含む) 任意の組織内のアカウントを指定した場合、それは "*マルチテナント*" アプリです。

後続のセクションでは、アプリケーションにアクセスできるユーザー (アカウントの種類) を変更する目的で、Azure portal でアプリの登録を変更する方法を学習します。

## <a name="prerequisites"></a>前提条件

* [Azure AD テナントに登録されているアプリケーション](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>異なるアカウントをサポートするようにアプリケーションの登録を変更する

既存のアプリ登録でサポートされているアカウントの種類に別の設定を指定するには:

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で、 **[アプリの登録]** を選択してから、自分のアプリケーションを選択します。
1. ここで、対象のアプリケーションを使用できるユーザーを指定します。これは、"*サインインの対象ユーザー*" と呼ばれる場合もあります。

    | サポートされているアカウントの種類 | 説明 |
    |-------------------------|-------------|
    | **この組織のディレクトリ内のアカウントのみ** | "*ご利用の*" テナント内のユーザー (またはゲスト) によってのみ使用されるアプリケーションをビルドしている場合、このオプションを選択します。<br><br>これは、多くの場合、"*基幹業務*" (LOB) アプリケーションと呼ばれ、Microsoft ID プラットフォームの **シングルテナント** アプリケーションです。 |
    | **任意の組織のディレクトリ内のアカウント** | "*任意*" の Azure AD テナント内のユーザーがアプリケーションを使用できるようにする場合は、このオプションを選択します。 このオプションは、たとえば、複数の組織に提供する目的でサービスとしてのソフトウェア (SaaS) アプリケーションをビルドしている場合に適しています。<br><br>Microsoft ID プラットフォームでは、これは、**マルチテナント** アプリケーションと呼ばれます。 |
1. **[保存]** を選択します。

### <a name="why-changing-to-multi-tenant-can-fail"></a>マルチテナントへの変更が失敗する理由

アプリケーション ID URI (アプリ ID URI) の名前の競合が原因で、アプリの登録をシングルテナントからマルチテナントに切り替える操作が失敗することがあります。 `https://contoso.onmicrosoft.com/myapp` は、アプリ ID URI の一例です。

アプリ ID URI は、プロトコル メッセージでアプリケーションを識別する手段の 1 つです。 シングル テナント アプリケーションの場合、アプリ ID URI はそのテナント内でのみ一意である必要があります。 マルチテナント アプリケーションの場合、Azure AD が全テナントから該当するアプリを特定できるように、アプリ ID URI がグローバルで一意になっている必要があります。 グローバルな一意性を確保するには、アプリ ID URI のホスト名が Azure AD テナントの[検証済み発行元ドメイン](howto-configure-publisher-domain.md)のいずれかと一致している必要があります。

たとえば、テナントの名前が *contoso.onmicrosoft.com* の場合、`https://contoso.onmicrosoft.com/myapp` は有効なアプリ ID URI です。 また、テナントの検証済みドメインが *contoso.com* の場合、有効なアプリ ID URI は `https://contoso.com/myapp` のようになります。 アプリ ID URI が 2 番目のパターン `https://contoso.com/myapp` に従っていない場合は、アプリ登録のマルチテナントへの変換は失敗します。

検証済み発行元ドメインの構成の詳細については、[検証済みドメインの構成](howto-configure-publisher-domain.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[シングルテナントからマルチテナントにアプリを変換する](howto-convert-app-to-be-multi-tenant.md)ための要件を確認します。
