---
title: Azure Active Directory B2C の構成要素を使用して Angular アプリケーションで認証を有効にする
description: Angular アプリケーションでユーザーをサインインおよびサインアップする Azure Active Directory B2C の構成要素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 6c8ab2ced9dc81594e13f574060050ade1b6e09a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778846"
---
# <a name="enable-authentication-in-your-own-angular-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して独自の Angular アプリケーションで認証を有効にする

この記事では、独自の Angular シングルページ アプリケーション (SPA) に Azure Active Directory B2C (Azure AD B2C) 認証を追加する方法について説明します。 Angular アプリケーションを [MSAL for Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) 認証ライブラリと統合する方法について説明します。 

この記事は、[サンプル Angular SPA アプリケーションでの認証の構成](./configure-authentication-sample-angular-spa-app.md)に関する記事と共に使用してください。その際は、サンプルの Angular アプリを独自の Angular アプリに置き換えてください。 この記事の手順を完了すると、アプリケーションで Azure AD B2C を使用したサインインが受け入れられます。

## <a name="prerequisites"></a>前提条件

[サンプル Angular SPA アプリケーションでの認証の構成](configure-authentication-sample-angular-spa-app.md)に関する記事の前提条件と統合の手順を確認します。

## <a name="create-an-angular-app-project"></a>Angular アプリ プロジェクトの作成

既存の Angular アプリ プロジェクトを使用するか、新しいプロジェクトを作成することができます。 新しいプロジェクトを作成するには、次のコマンドを実行します。 

次のコマンドは:

1. npm パッケージ マネージャーを使用して [Angular CLI](https://angular.io/cli) をインストールします。
1. ルーティング モジュールを使用して [Angular ワークスペースを作成](https://angular.io/cli/new)します。 アプリ名は `msal-angular-tutorial` で、これは、`contoso-car-service` のような任意の有効な Angular アプリ名に変更できます。
1. アプリ ディレクトリ フォルダーに移動します。

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>依存関係をインストールする

アプリケーションに [MSAL ブラウザー](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser)と [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) ライブラリをインストールするには、コマンド シェルで次のコマンドを実行します。

```
npm install @azure/msal-browser @azure/msal-angular
```

[Angular Material コンポーネント ライブラリ](https://material.angular.io/)をインストールします (オプション、UI の場合)。

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>認証コンポーネントの追加

サンプル コードは、次のコンポーネントで構成されています。 

|コンポーネント  |型  |説明  |
|---------|---------|---------|
| auth-config.ts| 定数 | Azure AD B2C ID プロバイダーと Web API サービスに関する情報を含む構成ファイル。 Angular アプリでは、この情報を使用して Azure AD B2C との信頼関係を確立し、ユーザーのサインインとサインアウトを行い、トークンを取得して検証します。 |
| app.module.ts| [Angular モジュール](https://angular.io/guide/architecture-modules)| アプリケーションの各部品が連携するしくみについて説明します。 これは、アプリケーションのブートストラップと起動に使用されるルート モジュールです。 このチュートリアルでは、いくつかのコンポーネントを *app.module.ts* モジュールに追加し、MSAL 構成オブジェクトを使用して MSAL ライブラリを開始します。  |
| app-routing.module.ts | [Angular ルーティング モジュール](https://angular.io/tutorial/toh-pt5) | ブラウザーの URL を解釈し、対応するコンポーネントを読み込んでナビゲーションを有効にします。  このチュートリアルでは、ルーティング モジュールにいくつかのコンポーネントを追加し、[MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md) でコンポーネントを保護します。 保護されたコンポーネントにアクセスできるのは、許可されているユーザーのみです。   |
| app.component.* | [Angular コンポーネント](https://angular.io/guide/architecture-components) | `ng new` コマンドにより、ルート コンポーネントを持つ Angular プロジェクトが作成されました。 このチュートリアルでは、上部のナビゲーション バーをホストするようにアプリ コンポーネントを変更します。 ナビゲーション バーには、サインインやサインアウトなど、さまざまなボタンが含まれます *app.component.ts* クラスにより、サインインとサインアウトのイベントが処理されます。  |
| home.component.* | [Angular コンポーネント](https://angular.io/guide/architecture-components)|このチュートリアルでは、*home* コンポーネントを追加して、匿名アクセスのホーム ページをレンダリングします。 このコンポーネントは、ユーザーがサインインしているかどうかを確認する方法を示しています。  |
| profile.component.* | [Angular コンポーネント](https://angular.io/guide/architecture-components) | このチュートリアルでは、*profile* コンポーネントを追加して、ID トークン要求を読み取る方法を学びます。 |
| webapi.component.* | [Angular コンポーネント](https://angular.io/guide/architecture-components)| このチュートリアルでは、*webapi* コンポーネントを追加して、Web API を呼び出す方法を学びます。 |



次のコンポーネントをアプリに追加するには、次の Angular CLI コマンドを実行します。 `generate component` コマンドにより、次の処理が実行されます。

1. コンポーネントごとにフォルダーを作成します。 このフォルダーには、TypeScript、HTML、CSS、テスト ファイルが格納されます。 
1. `app.module.ts` および `app-routing.module.ts` ファイルを、新しいコンポーネントへの参照で更新します。 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>アプリ設定を追加する

Azure AD B2C ID プロバイダーと Web API の設定は、`auth-config.ts` ファイルに格納されています。 *src/app* フォルダーに、次のコードを含む *auth-config.ts* という名前のファイルを作成します。 次に、「[3.1 Angular サンプルを構成する](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample)」の説明に従って設定を変更します。

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,.
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="initiate-the-authentication-libraries"></a>認証ライブラリを開始する

パブリック クライアント アプリケーションは、アプリケーション シークレットを安全に維持するために信頼されていないため、クライアント シークレットも含んでいません。 *src/app* フォルダーで、*app.module.ts* を開き、次の変更を加えます。

1. MSAL および MSAL ブラウザー ライブラリをインポートします。
1. Azure AD B2C 構成モジュールをインポートします。
1. `HttpClientModule` をインポートします。 HTTP クライアントは Web API の呼び出しに使用されます。
1. Angular HTTP インターセプターをインポートします。 MSAL では、インターセプターを使用して、ベアラー トークンを HTTP の Authorization ヘッダーに挿入します。
1. 必須の Angular マテリアルを追加します。
1. 複数アカウントのパブリック クライアント アプリケーション オブジェクトを使用して MSAL のインスタンスを作成します。 MSAL の初期化では、次のものが渡されます。
    1. *auth-config.ts* 構成オブジェクト
    1. ルーティング ガード構成オブジェクト
    1. MSAL インターセプター構成オブジェクト。 インターセプター クラスは、既知の保護されたリソースに対して Angular の [HttpClient](https://angular.io/api/common/http/HttpClient) を使用する送信要求のためにトークンを自動的に取得します。
1. `HTTP_INTERCEPTORS`、および `MsalGuard` [Angular プロバイダー](https://angular.io/guide/providers)を構成します。  
1. `MsalRedirectComponent` を [Angular ブートストラップ](https://angular.io/guide/bootstrapping)に追加します。

*src/app* フォルダーで、*app.module.ts* を編集して、下のコード スニペットで示している次の変更を加えます。 変更には "*Changes start here*" (変更ここから) および "*Changes end here*" (変更ここまで) のフラグが付きます。 変更が完了すると、コードは次のコード スニペットのようになります。

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL config object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>ルートを構成する

ここでは、Angular アプリケーションのルートを構成します。 ユーザーがページ上のリンクを選択してシングルページ アプリケーション内を移動するか、アドレス バーに URL を入力すると、このルートによって URL が Angular コンポーネントにマップされます。 Angular ルーティングの [canActivate](https://angular.io/api/router/CanActivate) インターフェイスでは、MSAL Guard を使用して、ユーザーがサインインしているかどうかを確認します。 ユーザーがサインインしていない場合、MSAL ではユーザーを Azure AD B2C に誘導して認証します。

*src/app* フォルダーで、*app-routing.module.ts* を編集して、下のコード スニペットで示している次の変更を加えます。 変更には "*Changes start here*" (変更ここから) および "*Changes end here*" (変更ここまで) のフラグが付きます。 

変更が完了すると、コードは次のコード スニペットのようになります。

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>サインインとサインアウトのボタンを追加する

ここでは、サインインとサインアウトのボタンを *app* コンポーネントに追加します。 *src/app* フォルダーで、*app.component.ts* を開き、次の変更を加えます。

1. 必要なコンポーネントをインポートします。
1. [OnInit メソッド](https://angular.io/api/core/OnInit)を実装するようにクラスを変更します。 `OnInit` メソッドは、[MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) の監視可能イベント `inProgress$` をサブスクライブします。 ユーザーの操作の状態を知るには、特に、操作が完了したことを確認するには、このイベントを使用します。 MSAL アカウント オブジェクトを操作する前に、`InteractionStatus` プロパティが `InteractionStatus.None` を返すことを確認してください。 `subscribe` イベントでは、`setLoginDisplay` メソッドを呼び出して、ユーザーが認証されているかどうかを確認します。
1. クラス変数を追加します。
1. 認可フローを開始する `login` メソッドを追加します。
1. ユーザーをサインアウトさせる `logout` メソッドを追加します。 
1. ユーザーが認証されているかどうかを確認する `setLoginDisplay` メソッドを追加します。
1. `inProgress$` サブスクライブ イベントをクリーンアップするための [ngOnDestroy](https://angular.io/api/core/OnDestroy) メソッドを追加します。

変更が完了すると、コードは次のコード スニペットのようになります。

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { 
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
  /* Changes end here. */
}
```

*src/app* フォルダーで、*app.component.html* を編集して、次の変更を加えます。

1. プロファイル コンポーネントと Web API コンポーネントへのリンクを追加します。
1. クリック イベント属性が `login()` メソッドに設定されたログイン ボタンを追加します。 このボタンは、`loginDisplay` クラス変数が `false` の場合にのみ表示されます。
1. クリック イベント属性が `logout()` メソッドに設定されたログアウト ボタンを追加します。 このボタンは、`loginDisplay` クラス変数が `true` の場合にのみ表示されます。
1. [router-outlet](https://angular.io/api/router/RouterOutlet) 要素を追加します。 

変更が完了すると、コードは次のコード スニペットのようになります。

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

必要に応じて、*app.component.css* ファイルを次の CSS スニペットで更新します。 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>アプリ リダイレクトの処理 

MSAL でリダイレクトを使用する場合、[app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) ディレクティブを *index.html* に追加することが必須です。 *src* フォルダーで、*index.html* を編集します。 

変更が完了すると、コードは次のコード スニペットのようになります。

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>アプリの CSS の設定 (オプション)

*/src* フォルダーの *styles.css* ファイルを次の CSS スニペットで更新します。 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> この時点で、アプリを実行してサインイン エクスペリエンスをテストできます。 アプリケーションを実行するには、「[Angular アプリケーションの実行](#run-the-angular-application)」を参照してください。

## <a name="check-if-a-user-is-authenticated"></a>ユーザーが認証されているかどうかの確認

`home.component` では、ユーザーが認証されているかどうかを確認する方法を示します。 *src/app/home* フォルダーで、*home.component.ts* を次のコード スニペットで更新します。 


コード:

1. [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) の監視可能イベント `msalSubject$` および `inProgress$` をサブスクライブします。 
1. `msalSubject$` により、認証の結果がブラウザー コンソールに書き込まれます。
1. `inProgress$` により、ユーザーが認証されているかどうかが確認されます。 `getAllAccounts()` からは、1 つまたは複数のオブジェクトが返されます。


```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  loginDisplay = false;

  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }
}
```

*src/app/home* フォルダーで、*home.component.html* を次の HTML スニペットで更新します。 [*ngIf](https://angular.io/api/common/NgIf) ディレクティブでは、`loginDisplay` クラス変数を確認して、ウェルカム メッセージを表示するか非表示にします。

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>ID トークン要求の読み取り

`profile.component` では、ユーザーの ID トークン要求にアクセスする方法を示します。 *src/app/profile* フォルダーで、*profile.component.ts* を次のコード スニペットで更新します。 

コード:

1. 必要なコンポーネントをインポートします。
1. [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) の監視可能イベント `inProgress$` をサブスクライブします。 このイベントによってアカウントが読み込まれ、ID トークン要求が読み取られます。
1. `checkAndSetActiveAccount` メソッドでは、アクティブなアカウントを確認および設定します。 これは、アプリで複数の Azure AD B2C ユーザー フローまたはカスタム ポリシーを操作する場合によく見られます。
1. `getClaims` メソッドでは、アクティブな MSAL アカウント オブジェクトから ID トークン要求を取得します。 次に、それらを `dataSource` 配列に追加します。 この配列は、コンポーネントのテンプレートのバインドを使用してユーザーにレンダリングされます。

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

*src/app/profile* フォルダーで、*profile.component.html* を次の HTML スニペットで更新します。 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>Web API を呼び出す

[トークンベースの認可 Web API](enable-authentication-web-api.md) を呼び出すには、アプリが有効なアクセス トークンを持っている必要があります。 [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) プロバイダーは、既知の保護されたリソースに対して Angular の [HttpClient](https://angular.io/api/common/http/HttpClient) を使用する送信要求のためにトークンを自動的に取得します。

> [!IMPORTANT]
> (*app.module.ts* クラスの) MSAL 初期化メソッドでは、`protectedResourceMap` オブジェクトを使用して、必要なアプリ スコープを持つ Web API などの保護されたリソースをマップします。 コードで別の Web API を呼び出す必要がある場合は、対応するスコープを持つ Web API URI、Web API HTTP メソッドを `protectedResourceMap` オブジェクトに追加します。 詳細については、[保護されたリソース マップ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map)に関する記事を参照してください。


[HttpClient](https://angular.io/api/common/http/HttpClient) オブジェクトで Web API を呼び出すと、[MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) プロバイダーで次の手順が実行されます。

1. Web API エンドポイントで必要なアクセス許可 (スコープ) を持つアクセス トークンを取得します。 
1. HTTP 要求の Authorization ヘッダー内で、次の形式を使用してベアラー トークンとしてアクセス トークンを渡します。

```http
Authorization: Bearer <access-token>
```

`webapi.component` は、Web API を呼び出す方法を示しています。 *src/app/webapi* フォルダーで、*webapi.component.ts* を次のコード スニペットで更新します。  

コード例を次に示します。

1. Angular の [HttpClient](https://angular.io/guide/http) を使用して Web API を呼び出します。
1. `auth-config` クラスの `protectedResources.todoListApi.endpoint` を読み取ります。 この要素では、Web API URI を指定します。 MSAL インターセプターでは、Web API URI に基づいて、対応するスコープを持つアクセス トークンを取得します。 
1. Web API からプロファイルを取得し、`profile` クラス変数を設定します。

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

*src/app/webapi* フォルダーで、*webapi.component.html* を次の HTML スニペットで更新します。 コンポーネントのテンプレートにより、Web API から返された `name` がレンダリングされます。 ページの下部に、Web API のアドレスがテンプレートによってレンダリングされます。

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

必要に応じて、*webapi.component.css* ファイルを次の CSS スニペットで更新します。 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>Angular アプリケーションを実行します。


次のコマンドを実行します。

```console
npm start
```

コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

```console
Listening on port 4200...
```

> [!TIP]
> または、[VS Code デバッガー](https://code.visualstudio.com/docs/editor/debugging)を使用して `npm start` コマンドを実行します。 VS Code の組み込みデバッガーを使用すると、編集、コンパイル、デバッグのループを加速できます。

アプリケーションを表示するには、ブラウザーで `http://localhost:4200` に移動します。


## <a name="next-steps"></a>次のステップ

* [Azure AD B2C を使用した独自の Angular アプリケーションでの認証オプション](enable-authentication-angular-spa-app-options.md)を構成する
* [独自の Web API で認証を有効にする](enable-authentication-web-api.md)
