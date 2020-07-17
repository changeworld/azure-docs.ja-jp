---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011991"
---
.NET 向けの [Microsoft Azure App Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) クライアント ライブラリ (プレビュー) は、コードからのトークンの取得と更新のプロセスを簡略化します。 App Authentication クライアント ライブラリは、自動的に認証を管理します。 このライブラリは、ローカルでの開発中に開発者の資格情報を使用して認証を行います。 ローカルでの開発中に開発者の資格情報を使用するという方法は、Azure AD 資格情報を作成したり、開発者間で資格情報を共有したりする必要がないため、セキュリティの面で有利です。 その後、ソリューションを Azure にデプロイすると、このライブラリは、自動的にアプリケーションの資格情報を使用するように切り替わります。

Azure Storage アプリケーションで App Authentication ライブラリを使用するには、[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) から最新のプレビュー パッケージと、[.NET 用の Azure Storage 共通クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)と [.NET 用の Azure Blob Storage クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)の最新バージョンをインストールします。 次の **using** ステートメントをコードに追加します。

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
