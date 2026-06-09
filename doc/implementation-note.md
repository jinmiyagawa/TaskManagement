# Trello風タスク管理アプリ 開発者向け実装メモ v0.1

## 1. 技術構成

```text
Next.js
TypeScript
Tailwind CSS
Supabase
GitHub
```

## 2. Claude Codeへの前提指示

Claude Codeには、最初から実装させず、まず計画を出させる。

```text
あなたはNext.js / TypeScript / Supabase / Reactの学習をサポートするシニアエンジニアです。

これから、Trello風の個人用タスク管理アプリを作ります。
目的はアプリ開発学習です。

実装後、Gitでコミットし、GitHubへpushするところまで行いたいです。

ただし、まずは一気に実装せず、以下の順で進めてください。

1. 現在のプロジェクト構成を確認
2. 要件・設計を読んで実装計画を作成
3. まだコードを書かず、実装ステップを提案
4. 私が確認したあと、ステップごとに実装
5. 実装後に動作確認方法を説明
6. 問題なければcommit
7. GitHubへpush

重要:
- 初心者にも理解しやすいように説明してください
- MVPに不要な機能は追加しないでください
- コードを分割しすぎないでください
- ただし最低限、読みやすいコンポーネント構成にしてください
- UI表示は日本語にしてください
- コード内の変数名・関数名・型名は英語にしてください
- SupabaseのURLやanon keyなどの秘密情報はGitHubにpushしないでください
- .env.local はコミットしないでください
- .env.example を作成してください
- 実装前に、変更予定ファイルを説明してください
- 実装後に、作成・変更したファイルと役割を説明してください
```

## 3. 想定ディレクトリ構成

```text
app/
  page.tsx

components/
  Board.tsx
  Column.tsx
  TaskCard.tsx
  TaskForm.tsx

lib/
  supabase.ts
  taskRepository.ts

types/
  task.ts

.env.example
```

## 4. TypeScript型案

```ts
export type Priority = "low" | "normal" | "high" | "urgent";

export type ColumnKey = "todo" | "doing" | "done";

export type Task = {
  id: string;
  title: string;
  dueDate: string | null;
  priority: Priority;
  columnKey: ColumnKey;
  orderIndex: number;
  createdAt: string;
  updatedAt: string;
};

export type TaskRow = {
  id: string;
  title: string;
  due_date: string | null;
  priority: Priority;
  column_key: ColumnKey;
  order_index: number;
  created_at: string;
  updated_at: string;
};
```

## 5. Supabase環境変数

`.env.local` に以下を設定する。

```env
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
```

GitHubには `.env.local` をpushしない。

`.env.example` を作成する。

```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

## 6. 実装ステップ案

### Step 1: 現状確認

- プロジェクト構成を確認
- Next.js / TypeScript / Tailwind CSS の状態を確認
- Git状態を確認
- `.gitignore` に `.env.local` が含まれるか確認

完了条件:

- 現在の構成と不足点が説明されている

### Step 2: Supabase接続準備

- `@supabase/supabase-js` を導入
- `lib/supabase.ts` を作成
- `.env.example` を作成
- `.env.local` はGit管理外にする

完了条件:

- Supabaseクライアントを作成できている
- 秘密情報がGit管理外になっている

### Step 3: 型定義・固定値定義

- `types/task.ts` を作成
- `Priority`
- `ColumnKey`
- `Task`
- `TaskRow`
- `COLUMNS`
- `PRIORITIES`

完了条件:

- タスクとカラムの型が定義されている

### Step 4: DB操作層作成

- `lib/taskRepository.ts` を作成
- タスク取得
- タスク追加
- タスク編集
- タスク削除
- タスク移動

完了条件:

- DB操作がコンポーネントから分離されている

### Step 5: 画面表示

以下のコンポーネントを作成する。

- `Board`
- `Column`
- `TaskCard`
- `TaskForm`

完了条件:

- DBから取得したタスクが3カラムに表示される

### Step 6: タスク追加

- 各カラムから追加フォームを開く
- 入力チェック
- DB保存
- 表示更新

完了条件:

- 任意のカラムにタスクを追加できる

### Step 7: タスク編集

- カード内で編集フォームに切り替える
- 入力チェック
- DB更新
- 表示更新

完了条件:

- タイトル、期限、優先度を編集できる

### Step 8: タスク削除

- 確認ダイアログ
- DB削除
- 表示更新

完了条件:

- 確認後にタスクを削除できる

### Step 9: タスク移動

- カラムごとの移動ボタン
- `column_key` 更新
- `order_index` 更新
- 表示更新

完了条件:

- Todo ⇄ Doing ⇄ Done の移動ができる

### Step 10: 期限切れ表示

以下の場合に `期限切れ` と表示する。

- `due_date` が今日より前
- `column_key` が `done` ではない

完了条件:

- 期限切れ条件が画面に反映される

### Step 11: 動作確認

以下を確認する。

- 初期データが表示される
- タスク追加できる
- タスク編集できる
- タスク削除できる
- タスク移動できる
- 期限切れ表示される
- ページ更新後もDBから表示される
- `.env.local` がGit管理対象外である

### Step 12: Git commit / push

実装完了後、以下を行う。

```bash
git status
git add .
git commit -m "Add Trello-style task board MVP"
git push
```

ただし、commit前に必ず以下を確認する。

```bash
git status
```

`.env.local` が含まれていた場合は絶対にcommitしない。

## 7. Claude Codeへの最初の実行指示

3つのMarkdownファイルをプロジェクトの `docs/` 配下に配置したあと、Claude Codeに以下を送る。

```text
docs/requirements.md、docs/basic-design.md、docs/implementation-note.md を読んでください。

まだアプリ本体のコードは実装しないでください。

まず以下を実施してください。

1. 現在のプロジェクト構成を確認
2. Gitの状態を確認
3. 要件に対して実装ステップを整理
4. 変更予定ファイルを一覧化
5. 初心者がつまずきそうなポイントを説明
6. commit / push 前に確認すべき注意点を説明

その後、私の確認を待ってください。
```
