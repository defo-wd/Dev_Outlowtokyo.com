# JDM Media OutlowTokyo.com

JDMメディア “OutlowTokyo” のWebサイト制作リポジトリ。  
ポートフォリオを兼ねるため **Public** で開発を進行中。  
個人製作の範疇なので、細かいことは気にせず自由に作ります。

---

## 目次

- [目的](#目的)
- [機能要件](#機能要件)
- [機能一覧](#機能一覧)
- [記事タイプ](#記事タイプ)
- [DBスキーマ（Prisma）](#dbスキーマprisma)
- [ページ構成（Remix）](#ページ構成remix)
- [技術スタック](#技術スタック)
- [今後の予定](#今後の予定)

---

## 目的

- ネットメディアとして、JDMカルチャーの魅力をテキスト・画像・動画で発信
- ギャラリー・投稿・コメント機能を備えた趣味特化のWebメディアを目指す
- 自分自身の表現の場・技術スタック検証・ポートフォリオに活用

---

## 機能要件

- ネットメディアっぽい記事が投稿・閲覧できること
- ログインを介さずコメントができること（掲示板形式）
- 広告をつける前提でのUIデザインとスペース確保

---

## 機能一覧

### コンテンツ表示
- 記事一覧・詳細表示（`Column`, `Spotlight`, `Feature`）
- ギャラリーページ（画像・動画を抽出表示）

### 投稿・管理機能
- 投稿者ログイン（セッションベース認証）
- 記事投稿・編集（タイトル＋本文＋画像＋動画）
- 投稿ダッシュボード（編集 / 削除）

### コメント機能
- 匿名掲示板形式（ユーザー管理なし）
- 投稿名（任意）＋本文のみ

---

## 記事タイプ（ArticleType）

| コード     | 表示名|
|------------|------------|
| COLUMN     | Column|
| SPOTLIGHT  | Spotlight|
| FEATURE    | Feature|

---

## DBスキーマ（Prisma）

```prisma
model Article {
  id        String   @id @default(uuid())
  slug      String   @unique
  title     String
  content   String
  type      ArticleType
  author    User     @relation(fields: [authorId], references: [id])
  authorId  String
  media     Media[]
  tags      Tag[]    @relation("ArticleTags")
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Media {
  id         String   @id @default(uuid())
  url        String
  type       MediaType
  articleId  String
  article    Article  @relation(fields: [articleId], references: [id])
}

model User {
  id        String   @id @default(uuid())
  username  String   @unique
  email     String   @unique
  role      UserRole
  password  String
  createdAt DateTime @default(now())
  articles  Article[]
}

model Tag {
  id        String   @id @default(uuid())
  name      String   @unique
  articles  Article[] @relation("ArticleTags")
}

enum ArticleType {
  COLUMN
  SPOTLIGHT
  FEATURE
}

enum MediaType {
  IMAGE
  VIDEO
}

enum UserRole {
  MEMBER
  ADMIN
}
```

---

## 技術スタック

### 言語・ランタイム
| 項目         | 使用技術|
|--------------|------------------|
| フロント・API共通 | TypeScript|
| ランタイム    | Node.js 20.x|

### フレームワーク・ライブラリ
| 項目               | 使用技術|
|--------------------|------------------|
| フルスタックFW      | Remix.js|
| スタイリング       | Tailwind CSS|
| 認証管理           | Remix セッション|

### データベース・ORM
| 項目          | 使用技術|
|---------------|------------------|
| RDBMS         | PostgreSQL 15.x|
| ORM / スキーマ | Prisma|

### ストレージ・外部サービス
| 項目          | 使用技術|
|---------------|------------------|
| メディア管理   | Cloudinary|
| DB + Storage   | Supabase（予定）|

### 開発・デプロイ環境
| 項目           | 使用技術|
|----------------|------------------|
| コンテナ管理    | Docker + Compose|
| 開発環境OS     | Ubuntu 22.04|
| デプロイ候補    | Vercel / Railway / Fly.io|
