# Fork Notes

このリポジトリは [tranhuycong/space-saver](https://github.com/tranhuycong/space-saver) の fork。社内利用向けに、自分でソースを読んで安全性を確認した上でビルドして使うためのもの。配布されているバイナリをそのまま使わない方針。

## なぜ fork したか

元のリポジトリは public だが、配布物（DMG、Homebrew Cask、Gumroad 経由のバイナリ）が誰によってどうビルドされたものか確認できない。ソースコードを自分で読み、自分でビルドした版を社内で使うために fork した。

## 重要：ベースは `v1.0.2` タグ、`upstream/main` ではない

最初は `upstream/main` から fork したが、ビルドすると README のスクリーンショットと違う UI が出てくる「壊れた」状態だった。

調査の結果、**実際にリリース・配布されているのは v1.0.2 のバイナリ**で、`upstream/main` は v1.0.7 タグ以降の未リリース変更（Firebase 追加、プロジェクト名 SpaceSaver → Spacey へのリネーム、UI 大幅再構築）が積まれた、Author 自身の手元にしかない開発中ブランチだった。

### この結論に至った根拠

1. `v1.0.7` タグの README が download link として v1.0.2.dmg を指している
2. `appcast.xml`（Sparkle の更新フィード）も `sparkle:version="1.0.1"` で止まっており、それ以降のリリース告知はない
3. README に貼られている screenshots は 2024-11-08 にリポに追加されており、これは v1.0.1/v1.0.2 と同時期。v1.0.7（2025-01-03）の UI ではない
4. `v1.0.7` タグの中の `MARKETING_VERSION` は `1.0.5` で、**タグ名と内部バージョンが一致していない**

つまり、上流のタグ運用は信頼できない。「最新タグ = 最新リリース」という当たり前の前提が成立していない。

### 対策

- このリポジトリの `main` は `v1.0.2` タグを起点にしている
- 将来 upstream に追従する時は、`upstream/main` を盲信せず、「**実際に配布されているバージョンはどれか？**」を README / appcast / screenshots / Homebrew Cask の formula などから確認する

## このリポジトリで上流に対して加えた変更

`v1.0.2` 自体が必要十分にクリーン（Firebase なし・Sparkle SDK なし）だったので、加えた変更は最小：

| 変更 | 理由 |
|---|---|
| `CLAUDE.md` 追加 | ブランチと worktree の運用ルールを明文化 |
| `appcast.xml` 削除 | Sparkle SDK 自体が組み込まれていないので、ファイルだけが orphan として残っていた |
| `DEVELOPMENT_TEAM`、`PRODUCT_BUNDLE_IDENTIFIER` を自分のものに差し替え | 上流の Apple Developer Team では署名できないため |

詳細は `git log v1.0.2..HEAD` で。

## ビルドと配布

- **ビルド**: `SpaceSaver.xcodeproj` を Xcode で開いて `Cmd + B`。Apple ID を Xcode に追加しておけば、自動で Mac Development 証明書が取得される
- **配布**: 手動。DMG または zip を社内 Slack や Google Drive に置いて共有
- **自動更新は無効**。Sparkle SDK 自体が入っていない（v1.0.2 時点で組み込まれていなかった）

少人数なので、新しい版が出たら手動で「新版置いたよ」で十分回る想定。Sparkle 等の自動更新を再導入するのは必要になってからで良い。

## 過去の試行錯誤（archive タグ）

最初に `upstream/main` ベースで Firebase 除去・Sparkle 除去・ID 差し替えを試した時の結果が、`archive/v1.0.7-base-attempt` というタグで残してある。何か参照したくなったら `git checkout archive/v1.0.7-base-attempt` で見られる。

## ライセンス

上流の通り **GPLv2**。社内配布は GPL の「distribution」に該当しないので追加義務なし。社外公開する場面が出てきた時のために、この fork は public のままにしておく（ソース公開義務を自然に満たすため）。
