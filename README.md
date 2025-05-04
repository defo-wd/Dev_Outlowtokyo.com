# JDM Media Site

JDM（Japanese Domestic Market）カルチャーに特化したネットメディアサイト。  
写真、記事、動画を通じてJDMの魅力を国内外に発信します。

---

## 目次

- [目的](#目的)
- [機能一覧](#機能一覧)
- [記事タイプ](#記事タイプ)
- [DBスキーマ（Prisma）](#dbスキーマprisma)
- [ページ構成（Remix）](#ページ構成remix)
- [技術スタック](#技術スタック)
- [今後の予定](#今後の予定)

---

## 目的

- JDM文化の発信（記事、写真、動画）
- 投稿はメンバー・管理者のみ、閲覧・コメントは自由
- 動的なUIと背景動画で視覚的に魅せるサイト構成

---

## 機能一覧

### コンテンツ表示
- 記事一覧・詳細表示（Column / Spotlight / Feature）
- ギャラリーページ（画像・動画のみ）

### 投稿・管理機能
- 投稿者ログイン（セッション認証）
- 記事投稿・編集（画像 / 動画対応）
- ダッシュボード（投稿管理）

### コメント
- 匿名掲示板形式（ユーザー登録なし）
- 名前（任意）＋コメント投稿

---

## 記事タイプ

| コード     | 表示名     | 説明                     |
|------------|------------|--------------------------|
| COLUMN     | Column     | テキスト中心の記事       |
| SPOTLIGHT  | Spotlight  | 写真メインの記事         |
| FEATURE    | Feature    | 動画を含む特集コンテンツ |

---

## DBスキーマ（Prisma）

### モデル定義（概要）

```prisma
model Article {
  id        String      @id @default(uuid())
  slug      String      @unique
  title     String
  content   String
  type      ArticleType
  author    User        @relation(fields: [authorId], references: [id])
  authorId  String
  media     Media[]
  tags      Tag[]       @relation("ArticleTags")
  createdAt DateTime    @default(now())
  updatedAt DateTime    @updatedAt
}

model Media {
  id         String   @id @default(uuid())
  url        String
  type       MediaType
  articleId  String
  article    Article  @relation(fields: [articleId], references: [id])
}

model User {
  id        String    @id @default(uuid())
  username  String    @unique
  email     String    @unique
  role      UserRole
  password  String
  createdAt DateTime  @default(now())
  articles  Article[]
}

model Tag {
  id        String    @id @default(uuid())
  name      String    @unique
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
