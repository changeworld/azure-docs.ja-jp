---
title: Azure Active Directory B2C での年齢制限の使用 | Microsoft Docs
description: アプリケーションを使用している未成年者を特定する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d24e37642a41e4d60b33f42a60d7e56cb4b35b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446726"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Azure AD B2C で年齢制限を使用する

>[!IMPORTANT]
>この機能はプライベート プレビュー段階にあります。  これが利用可能になった時点で[サービス ブログ](https://blogs.msdn.microsoft.com/azureadb2c/)で詳細を確認するか、AADB2CFeedback@microsoft.com までお問い合わせください。  これは運用環境のディレクトリでは使用しないでください。これらの新機能を使用すると、データが失われる可能性があります。また、一般公開されるまでは、動作に予期しない変更が生じる可能性もあります。  
>

##<a name="age-gating"></a>年齢制限
年齢制限により、Azure AD B2C を使用して、アプリケーションで未成年者を特定できます。  ユーザーがアプリケーションにサインインできないようにブロックするか、ユーザーの年齢グループとその保護者の同意の状態を識別する追加の要求を使用してユーザーがもう一度アプリケーションを使用できるように許可するかを選択できます。  

>[!NOTE]
>保護者の同意は、`consentProvidedForMinor` というユーザー属性で追跡されます。  このプロパティは Graph API を使用して更新できます。`legalAgeGroupClassification` を更新する際にこのフィールドが使用されます。
>

##<a name="setting-up-your-directory-for-age-gating"></a>年齢制限のディレクトリを設定する
ユーザー フローで年齢制限を使用するには、追加のプロパティが含まれるようにディレクトリを設定する必要があります。 この操作は、メニューの `Properties` から行うことができます (プライベート プレビューに参加している場合のみ使用可能です)。  
1. Azure AD B2C の拡張機能で、左側のメニューにあるテナントの **[プロパティ]** をクリックします。
2. **[Age gating]\(年齢制限\)** セクションで、**[構成]** ボタンをクリックします。
3. 操作が完了するまで待ちます。ディレクトリは年齢制限について設定されます。

##<a name="enabling-age-gating-in-your-user-flow"></a>ユーザー フローで年齢制限を有効にする
年齢制限を使用するようディレクトリが設定されたら、プレビュー バージョンのユーザー フローでこの機能を使用できます。  この機能では、既存の種類のユーザー フローとの互換性がなくなる変更が必要になります。  次の手順に従って、年齢制限を有効にします。
1. プレビューのユーザー フローを作成します。
2. 作成が完了したら、メニューの **[プロパティ]** に移動します。
3. **[Age gating]\(年齢制限\)** セクションで、切り替えを押してこの機能を有効にします。
4. その後、未成年者と見なすユーザーの管理方法を選択できます。

##<a name="what-does-enabling-age-gating-do"></a>年齢制限を有効にした場合の影響
ユーザー フローで年齢制限が有効になると、ユーザー エクスペリエンスが変化します。  ユーザーは、サインアップ時に、ユーザー フローに対して構成されたユーザー属性と共に、生年月日と居住国が求められるようになります。  サインイン時には、これまでに生年月日と居住国を入力していないユーザーは、次回サインイン時にこの情報を求められます。  これらの 2 つの値から、Azure AD B2C は、ユーザーが未成年者かどうかを特定し、`ageGroup` フィールドを更新します。この値は `null`、`Undefined`、`Minor`、`Adult`、`NotAdult` になります。  その後、`ageGroup` フィールドと `consentProvidedForMinor` フィールドは `legalAgeGroupClassification` の計算に使用されます。 

##<a name="age-gating-options"></a>年齢制限のオプション
Active Directory B2C で保護者の同意なしに未成年者をブロックまたは許可するか、アプリケーションに未成年者の扱いを決定させるかを選択することができます。  

###<a name="allowing-minors-without-parental-consent"></a>保護者の同意がない未成年者を許可する
サインアップ、サインイン、またはその両方を許可するユーザー フローでは、同意のない未成年者にアプリケーションの使用を許可することを選択できます。  保護者の同意がない未成年者は、通常どおりにサインインまたはサインアップすることが許可され、Active Directory B2C は`legalAgeGroupClassification` 要求を含む ID トークンを発行します。  この要求を使用することで、保護者の同意を得るなど、このようなユーザーのエクスペリエンスを選択できます (さらに、`consentProvidedForMinor` フィールドを更新できます)。

###<a name="blocking-minors-without-parental-consent"></a>保護者の同意がない未成年者をブロックする
サインアップ、サインイン、またはその両方を許可するユーザー フローでは、アプリケーションからの同意のない未成年者をブロックすることを選択できます。  ブロックされているユーザーを Azure AD B2C で処理するためのオプションは 2 つあります。
* JSON をアプリケーションに再度送信する - このオプションでは、未成年者がブロックされたという応答をアプリケーションに再度送信します。
* エラー ページを表示する - アプリケーションにアクセスできないことを知らせるページがユーザーに表示されます。

##<a name="known-issues"></a>既知の問題
###<a name="format-for-the-response-when-a-minor-is-blocked"></a>未成年者がブロックされる場合の応答の形式
現在、この応答の形式は正しくありません。このバグは今後の更新で解決される予定です。

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>セットアップ時に追加された特定の属性を削除するとディレクトリで年齢制限を使用できなくなる
年齢制限の設定では、`Properties` のオプションを使用してディレクトリを構成しました。  グラフから `legalCountry` または `dateOfBirth` を削除した場合、ディレクトリで年齢制限を使用できなくなります。また、これらのプロパティを再作成することはできません。

###<a name="list-of-countries-is-incomplete"></a>国の一覧が不完全である
現在、legalCountry の国の一覧は不完全なため、今後の更新で残りの国を追加する予定です。