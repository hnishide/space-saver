# SpaceSaver Fork 作業ルール

このリポジトリは tranhuycong/space-saver の fork で、社内利用向けにカスタマイズしている。リポジトリは public なので、コミットする内容は公開可能なものに限る。

## ブランチと worktree

独立した作業単位はそれぞれ別ブランチを切り、ブランチごとに worktree を作って作業する。

### ブランチ命名

Conventional Commits 形式のプレフィックス + kebab-case。

- `chore/...` — 依存除去、クリーンアップ、ユーザー可視な挙動変更を伴わないリファクタ
- `feature/...` — 新機能
- `fix/...` — バグ修正

### worktree の配置

worktree はリポジトリの隣の `space-saver.worktrees/` ディレクトリ以下に、**ブランチ名と同じパス**で配置する（プレフィックスもサブディレクトリとして展開する）。

```
<ghq-root>/github.com/<user>/
├── space-saver/                          ← main checkout
└── space-saver.worktrees/
    └── <prefix>/
        └── <branch-last-segment>/        ← <prefix>/<branch-last-segment> の worktree
```

例: `chore/remove-firebase` の worktree を作る場合：

```bash
git worktree add -b chore/remove-firebase \
  ../space-saver.worktrees/chore/remove-firebase
```

作業が完了したら worktree は削除する。**ブランチは削除せず残す**（履歴をたどりやすくするため）。

```bash
git worktree remove ../space-saver.worktrees/chore/remove-firebase
# ブランチ自体は残す
```

### この配置にする理由

このディレクトリは ghq 配下（`host/user/repo` 規約）にある。worktree をリポジトリの兄弟に置くと `ghq list` の出力が汚れる。リポジトリ内部に置くと git のインデックスや checkout 状態が混乱する。リポジトリ名の末尾に `.worktrees` をつけたディレクトリを兄弟に作り、その下にブランチ名と同じパスで配置するのが折衷案として一番きれい。
