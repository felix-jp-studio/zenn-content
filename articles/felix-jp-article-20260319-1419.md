---
title: "Firebase×Next.jsで認証機能を30分で実装する手順"
emoji: "🔥"
type: "tech"
topics: ["nextjs", "firebase", "typescript", "react"]
published: true
---

# Firebase×Next.jsで認証機能を30分で実装する手順

「ログイン機能って難しそう…」と感じていませんか？FirebaseとNext.jsを組み合わせると、驚くほど簡単に認証機能が作れます。この記事では**Googleログイン**を例に、初心者でも迷わない手順を解説します。

## 事前準備

以下が揃っていることを確認してください。

- Node.jsがインストール済み
- Googleアカウントを持っている
- Next.jsプロジェクトが作成済み（`npx create-next-app@latest`で作成可）

## Step 1：Firebaseプロジェクトを作成する

1. [Firebase Console](https://console.firebase.google.com/) にアクセス
2. 「プロジェクトを作成」をクリック
3. プロジェクト名を入力して作成完了
4. 左メニューの「Authentication」→「始める」をクリック
5. 「Google」を有効にして保存

次に「プロジェクトの設定」からWebアプリを登録し、**firebaseConfigの情報**をコピーしておきます。

## Step 2：必要なパッケージをインストール

```bash
npm install firebase
```

## Step 3：Firebase設定ファイルを作成する

プロジェクト直下に `lib/firebase.ts` を作成します。

```typescript
// lib/firebase.ts
import { initializeApp } from "firebase/app";
import { getAuth, GoogleAuthProvider } from "firebase/auth";

const firebaseConfig = {
  apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const provider = new GoogleAuthProvider();
```

APIキーなどの機密情報は `.env.local` ファイルに記述し、Gitに含めないよう `.gitignore` に追加しましょう。

## Step 4：ログインボタンを実装する

```typescript
// app/page.tsx
"use client";

import { signInWithPopup, signOut } from "firebase/auth";
import { auth, provider } from "@/lib/firebase";
import { useState } from "react";

export default function Home() {
  const [user, setUser] = useState<any>(null);

  const handleLogin = async () => {
    const result = await signInWithPopup(auth, provider);
    setUser(result.user);
  };

  const handleLogout = async () => {
    await signOut(auth);
    setUser(null);
  };

  return (
    <div>
      {user ? (
        <>
          <p>ようこそ、{user.displayName}さん！</p>
          <button onClick={handleLogout}>ログアウト</button>
        </>
      ) : (
        <button onClick={handleLogin}>Googleでログイン</button>
      )}
    </div>
  );
}
```

## Step 5：動作確認

```bash
npm run dev
```

ブラウザで `http://localhost:3000` を開き、「Googleでログイン」ボタンを押してみましょう。ポップアップが表示され、ログインに成功すれば完