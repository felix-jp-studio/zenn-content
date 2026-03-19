【タイトル】
Firebase×Next.jsで認証機能を30分で実装する5ステップ

【note投稿文】

【はじめに】

「ログイン機能ってどうやって作るの？」

これはプログラミングを始めてHTMLやCSSを覚えた後、多くの人がぶつかる最初の壁です。認証機能はセキュリティに関わる部分なので、自前で作ろうとすると非常に複雑になります。パスワードの暗号化、セッション管理、トークンの扱い…考えるだけで頭が痛くなりますよね。

でも安心してください。FirebaseとNext.jsを組み合わせれば、そういった難しい部分をすべてお任せしながら、わずか30分で動く認証機能が作れます。今回はその具体的な手順を5ステップで解説します。


【そもそもFirebaseって何？】

FirebaseはGoogleが提供するバックエンドサービスです。データベース、ストレージ、そして今回使う「認証機能」など、アプリ開発に必要な機能がひとまとめになっています。面倒なサーバー構築なしに使えるので、フロントエンド寄りの初心者にとって非常に相性がいいサービスです。


【5ステップで実装する手順】

・ステップ1：Firebaseプロジェクトを作成する
Firebaseのコンソール（console.firebase.google.com）にアクセスし、Googleアカウントでログイン。「プロジェクトを追加」からプロジェクト名を入力して作成します。作成後、「Authentication」メニューから「メール/パスワード」のログイン方法を有効にしましょう。

・ステップ2：Next.jsプロジェクトを用意する
ターミナルで「npx create-next-app@latest my-app」を実行してプロジェクトを作成します。TypeScriptを使う設定を選ぶと後々便利です。作成できたら「cd my-app」でディレクトリに移動してください。

・ステップ3：Firebase SDKをインストールして初期化する
「npm install firebase」でSDKをインストールします。その後、プロジェクトルートに「lib/firebase.ts」というファイルを作り、FirebaseコンソールのSDK設定画面からコピーした設定コードを貼り付けて初期化します。APIキーなどの情報は「.env.local」に移して管理するのが安全です。

・ステップ4：サインアップとログインのページを作る
「app/signup/page.tsx」と「app/login/page.tsx」をそれぞれ作成します。フォームの入力値を取得し、Firebaseの「createUserWithEmailAndPassword」（新規登録）と「signInWithEmailAndPassword」（ログイン）の関数を呼び出すだけで動作します。エラー処理も忘れずに追加しましょう。

・ステップ5：ログイン状態を管理してページを保護する
「onAuthStateChanged」という関数を使うと、ログイン状態の変化をリアルタイムに検知できます。これをContextやカスタムフックとして管理することで、未ログインのユーザーをログインページへリダイレクトする処理が簡単に書けます。


【まとめ】

認証機能は難しそうに見えて、Firebaseを使えば驚くほどシンプルに作れます。今回の5ステップをまとめると、

・Firebaseプロジェクトを作成して認証を有効化
・Next.jsプロジェクトの用意とSDKのインストール
・Firebase初期化ファイルの作成
・サインアップ・ログインページの実装
・ログイン状態の管理とページ保護

この流れを一度体験すると、アプリ開発の全体像がぐっとつかみやすくなります。ぜひ手を動かしながら試してみてください。

詳しい手順はこちら → https://felixstudio0.gumroad.com

---

【zenn投稿文】
---
title: "Firebase×Next.jsで認証機能を30分で実装する5ステップ"
emoji: "🔐"
type: "tech"
topics: ["nextjs", "firebase", "typescript"]
published: true
---

## はじめに

「ログイン機能ってどうやって作るの？」

これはHTMLやCSSを覚えた後、多くの人がぶつかる最初の壁です。認証機能はセキュリティに関わるため、自前で作ろうとすると非常に複雑になります。

FirebaseとNext.jsを組み合わせれば、そういった難しい部分をすべてお任せしながら、**わずか30分で動く認証機能**が作れます。

---

## そもそもFirebaseとは？

FirebaseはGoogleが提供するBaaS（Backend as a Service）です。認証・データベース・ストレージなどの機能がひとまとめになっており、サーバー構築不要で利用できます。

---

## 5ステップで実装する手順

### ステップ1：Firebaseプロジェクトを作成する

[Firebaseコンソール](https://console.firebase.google.com)にアクセスし、プロジェクトを作成します。「Authentication」→「Sign-in method」から「メール/パスワード」を有効にしてください。

### ステップ2：Next.jsプロジェクトを用意する

```bash
npx create-next-app@latest my-app --typescript
cd my-app
```

### ステップ3：Firebase SDKをインストールして初期化する

```bash
npm install firebase
```

`lib/firebase.ts` を作成して初期化します。

```typescript
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```

APIキーは `.env.local` で管理しましょう。

### ステップ4：サインアップ・ログインページを作る

```typescript
import { createUserWithEmailAndPassword } from "firebase/auth";
import { auth } from "@/lib/firebase";

const signup = async (email: string, password: string) => {
  try {
    await createUserWithEmailAndPassword(auth, email, password);
  } catch (error) {
    console.error(error);
  }
};
```

ログインは `signInWithEmailAndPassword` に置き換えるだけです。

### ステップ5：ログイン状態を管理してページを保護する

```typescript
import { onAuthStateChanged } from "firebase/auth";
import { useEffect, useState } from "react";
import { auth } from "@/lib/firebase";

export const useAuth = () => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (currentUser) => {
      setUser(currentUser);
    });
    return () => unsubscribe();
  }, []);

  return { user };
};
```

このフックを使って未ログインユーザーをリダイレクトできます。

---

## まとめ

| ステップ | 内容 |
|---|---|
| 1 | Firebaseプロジェクト作成・認証有効化 |
| 2 | Next.jsプロジェクトの用意 |
| 3 | SDK導入と初期化 |
| 4 | サインアップ・ログインページの実装 |
| 5 | ログイン状態の管理とページ保護 |

認証機能は難しそうに見えて、Firebaseを使えば驚くほどシンプルに実装できます。ぜひ手を動かしながら試してみてください。

詳しい手順はこちら → https://felixstudio0.gumroad.com

---

【X投稿文】
Firebase×Next.jsで
認証機能が30分で作れます。

むずかしいのは
パスワード暗号化やセッション管理…

全部Firebaseにお任せすれば
初心者でも5ステップで実装できる✅

---

【Noteへの貼り付け手順】
1. https://note.com/felixjpstudio を開く
2. 「投稿」→「テキスト」をクリック
3. 上記タイトルをタイトル欄に貼り付ける
4. 上記本文を本文欄に貼り付ける
5. 「公開設定」→「全員に公開」→「公開する」をクリック

【X投稿手順】
1. https://x.com を開く
2. 「ポスト」をクリック
3. 上記X投稿文を貼り付ける
4. 末尾にNoteのURLを追加する
5. 「ポストする」をクリック