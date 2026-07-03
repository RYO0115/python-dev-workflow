# python-dev-workflow スキル

Python プロジェクト共通の**コーディング規約・開発環境・ワークフロー**を Claude Code のスキルとして切り出したパッケージ。
`language_learn_app` の開発ルール（`skill_app_dev.md` / `CLAUDE.md`）から、
プロジェクト固有の部分を除いた**再利用可能な汎用ルールだけ**を抽出している。

## 含まれる内容

- **開発環境**: Python 3.12+ / uv による依存・仮想環境管理、`.env` 運用
- **バージョン管理**: `pyproject.toml` 一元管理 + `bump-my-version`（major / minor / patch）
- **アーキテクチャ**: `core/` + `features/` レイヤード構成と一方向依存（循環参照禁止）
- **コメント規則**: ファイル冒頭の概要コメント、why を書く
- **テスト必須ルール**: 機能変更ごとに UI / ユニットテストを追加し `pytest` green を確認
- **ドキュメント同期**: 設計書・仕様書を同じコミットで更新
- **拡張性を損ねるハードコードの禁止**: ユーザー登録値を固定リストで決め打ちしない原則

## ディレクトリ構成

```
python-dev-workflow/
├── SKILL.md                          # スキル本体（Claude が読み込むエントリポイント）
├── README.md                         # このファイル
└── references/
    ├── architecture.md               # 依存方向・レイヤード構成の詳細
    ├── testing.md                    # テストの種類と使い分け
    └── no-hardcode-principle.md      # ハードコード禁止原則の詳細と実例
```

## インストール方法

Claude Code のスキルは、スキルディレクトリを `.claude/skills/` 配下に置くだけで認識される。
用途に応じて次の 2 通りから選ぶ。

### 方法 A: 個人スキル（全プロジェクトで有効）

自分のすべてのプロジェクトで使いたい場合は、ユーザーの `~/.claude/skills/` に置く。

```bash
# このリポジトリを置いている場所から
cp -R python-dev-workflow ~/.claude/skills/
```

更新を追従したい場合はコピーの代わりにシンボリックリンクにする:

```bash
ln -s "$(pwd)/python-dev-workflow" ~/.claude/skills/python-dev-workflow
```

### 方法 B: プロジェクトスキル（そのリポジトリだけで有効・チーム共有向け）

特定プロジェクトにだけ入れ、git 管理してチームで共有したい場合はプロジェクト直下の `.claude/skills/` に置く。

```bash
# 対象プロジェクトのルートで実行
mkdir -p .claude/skills
cp -R /path/to/python-dev-workflow .claude/skills/

git add .claude/skills/python-dev-workflow
git commit -m "Add python-dev-workflow skill"
```

### 方法 C: git clone / submodule（公開リポジトリから直接取得）

このスキルは公開リポジトリとして配布している。clone すれば `git pull` で更新も追従できる。

```bash
# 個人スキルとして全プロジェクトで使う（~/.claude/skills 配下に clone）
git clone https://github.com/RYO0115/python-dev-workflow.git ~/.claude/skills/python-dev-workflow

# 更新するとき
git -C ~/.claude/skills/python-dev-workflow pull
```

特定プロジェクトに組み込んでチーム共有する場合は submodule にする:

```bash
# 対象プロジェクトのルートで実行
git submodule add https://github.com/RYO0115/python-dev-workflow.git .claude/skills/python-dev-workflow
git commit -m "Add python-dev-workflow skill as submodule"
```

### インストールの確認

Claude Code を（再）起動し、対象プロジェクトで次を実行して一覧に表示されれば成功:

```
/skills
```

または実装依頼時に「python-dev-workflow スキルを使って」と伝えると明示的に呼び出せる。
`description` に uv / pyproject.toml / pytest / アーキテクチャ等のキーワードを含めているため、
Python 実装タスクでは自動的に参照されることも多い。

## 使い方

インストール後は特別な操作は不要。Python プロジェクトで実装・修正・レビューを依頼すると、
Claude が本スキルの規約（テスト必須・依存方向・ハードコード禁止など）に沿って作業する。

プロジェクト固有のルール（そのアプリ特有のディレクトリ名・機能・API など）は、
各プロジェクトの `CLAUDE.md` に書く。**プロジェクト固有 > 本スキル** の優先順位で運用する。

## カスタマイズ

- 依存ツールが uv でない場合（poetry / pip-tools 等）は `SKILL.md` の「1. 開発環境」を書き換える。
- バージョン運用が bump-my-version でない場合は「2. バージョン管理」を差し替える。
- 不要なセクションは削除してよい。SKILL.md 冒頭の `description` は、
  自動で呼び出される精度に効くので、含まれる内容に合わせて更新すること。
