# サムネイル画像フォールバック機能

## 概要

Notionのページに`FeaturedImage`が設定されていない場合、タグに応じたデフォルトのサムネイル画像を自動的に表示する機能です。

## 画像の配置

以下のディレクトリに画像を配置してください：

```
public/images/thumbnails/
├── default.jpg     # 汎用デフォルト画像（必須）
├── dtm.jpg         # DTMタグ用（オプション）
├── news.jpg        # お知らせタグ用（オプション）
├── tips.jpg        # 小ネタタグ用（オプション）
└── dev.jpg         # 開発ログタグ用（オプション）
```

### 画像の命名規則

- **default.jpg** (必須): タグがない場合や、タグに対応する画像がない場合に使用される汎用画像
- **タグ別画像** (オプション): 各タグに対応する画像

## 動作仕様

1. **FeaturedImageがある場合**: 通常通りNotionの画像を表示
2. **FeaturedImageがない場合**:
   - 記事の最初のタグを確認
   - そのタグに対応する画像があれば使用
   - 対応する画像がない、またはタグがない場合は`default.jpg`を使用

## タグと画像のマッピング

現在のマッピングは以下の通りです（`src/lib/blog-helpers.ts`の`getFallbackImage`関数で定義）：

| タグ | 画像ファイル |
|------|------------|
| DTM | dtm.jpg |
| お知らせ | news.jpg |
| 小ネタ | tips.jpg |
| 開発ログ | dev.jpg |
| その他/未定義 | default.jpg |

## カスタマイズ方法

新しいタグに対応する画像を追加する場合：

1. `public/images/thumbnails/`に新しい画像を配置
2. `src/lib/blog-helpers.ts`の`getFallbackImage`関数内の`tagImageMap`に追加

### 例：「レビュー」タグを追加する場合

```typescript
const tagImageMap: { [key: string]: string } = {
  'DTM': '/images/thumbnails/dtm.jpg',
  'お知らせ': '/images/thumbnails/news.jpg',
  '小ネタ': '/images/thumbnails/tips.jpg',
  '開発ログ': '/images/thumbnails/dev.jpg',
  'レビュー': '/images/thumbnails/review.jpg', // ← 追加
}
```

## 推奨画像サイズ

- **推奨サイズ**: 1200x630px (OGP標準サイズ)
- **最小サイズ**: 800x400px
- **フォーマット**: JPEG、PNG、WebP
- **ファイルサイズ**: 500KB以下を推奨

## 注意事項

- `default.jpg`は必ず配置してください。この画像がないとフォールバックが正しく動作しません。
- タグ別の画像はオプションです。配置しない場合は`default.jpg`が使用されます。
- 開発環境（`DEV`モード）では、Notionの元のURLを直接参照します。
- 本番環境では、ローカルにダウンロードされた画像または上記のフォールバック画像を使用します。

## 関連ファイル

- **`src/lib/blog-helpers.ts`**: `getFallbackImage()`関数の実装
- **`src/components/PostFeaturedImage.astro`**: フォールバック処理を含むコンポーネント
- **`src/integrations/featured-image-downloader.ts`**: Notion画像のダウンロード処理

