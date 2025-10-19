# analtics_projects
各種分析PJ


## ■ repo 直下で共有するもの（ルートに 1 組だけ）

* `.devcontainer/`
  Dev Container の設定フォルダ。ここに `devcontainer.json`（と必要なら `Dockerfile` 参照）を置いて、箱をプロジェクト単位で固定する。([Visual Studio Code][1])
* `Dockerfile`
  Dev Container のビルドに使う共通 OS 仕様。ケースごとに複製せず、ルートで共通運用。
* `compose.yml`（または `compose.yaml`）
  開発用 DB／キャッシュなど補助サービスの起動定義。Compose の既定の探索先は作業ディレクトリ直下。([Docker Documentation][2])
* `.github/`
  CI・PR テンプレートなど共通ワークフロー。ケースごとに増やすと保守がばらける。
* `docs/`
  ワークスペース全体の説明・規約・運用ドキュメント。ケース固有のメモは各ケース側の README に寄せる。
* `scripts/`
  雛形生成や共通ユーティリティ等の“横断的”スクリプト。ケース固有ならケース側に置くが、**画像の `scripts/` は共通として管理**する前提にする。
* `.gitignore`
  ルートの無視規則。`**/.venv`, `**/.env`, `cases/**/data/*`, `cases/**/outputs/*` などをここで一括管理。 `.gitignore` の役割は Git 公式に定義がある。([Git][3])
* `LICENSE`
  ライセンスはリポジトリ単位。
* `README.md`
  ワークスペース全体の README（各ケースは別途ケース側に README を置く）。

---

## ■ `template_case/` 配下に格納し、ケースごとに複製・変更して使うもの

* `.streamlit/`
  Streamlit の設定はアプリ（＝ケース）に依存しやすい。ケース側に含めて複製する。
* `app/`
  Streamlit や簡易 UI など、ケースの成果物アプリ。ケース単位で増減・改修する前提。
* `data/`
  生データの置き場（**Git 管理外**が原則）。必要なら `.gitkeep` のみを置き、実体は外出しや Git LFS を利用。LFS の運用は GitHub 公式/プロジェクト公式を参照。([GitHub Docs][4])
* `model/`
  学習済みモデルや中間モデル（**通常は Git 管理外**。必要に応じて LFS）。
* `notebook/`
  ケースのノートブックは固有なのでケース側。
* `outputs/`
  中間生成物・図表（**Git 管理外**が原則。再生成可能ならコミットしない）。
* `src/`
  分析ロジック／ユーティリティの実装。ケース固有の変更が入りやすいのでケース側。
* `tests/`
  そのケースの検証コード。ケース側に保持。
* `.env.example`
  ケースで必要な環境変数のサンプル。実体の `.env` は**コミットしない**。
* `pyproject.toml`
  **ケースの依存を独立させるためにケース側に持つ。** uv はプロジェクト（＝そのフォルダ）単位で `uv.lock` と環境を管理する。([Astral Docs][5])
* `uv.lock`
  上と同じ理由でケース側。`uv sync` でロックに従って `.venv` を再現する。([Astral Docs][6])

> 補足
> 画像に含まれている `Dockerfile` と `compose.yml` は **ルート共有**に寄せる設計へ変更している。理由は、Dev Container と補助サービスは「箱の仕様」なのでリポジトリで 1 つに統一したほうが再現性とビルド時間の両面で有利だから。Dev Container の探索場所と Compose の既定位置はドキュメント上もルートを前提に記述されている。([Visual Studio Code][1])

---

### 運用のメモ（最低限）

* ルートに `template_case/` を作り、上記「ケース側」の構成をそのまま格納しておく。新しい分析は `template_case` を `cases/yyy-mm-foo/` にコピーして開始。
* 依存はケース直下で `uv sync` を実行し、`.venv` はケース内に作る（**Git 管理外**）。uv は「ロックと同期」をプロジェクト単位で行う前提。([Astral Docs][6])
* 大きなファイルは LFS を使うか、外部ストレージに逃がす。ルートの `.gitignore` にワイルドカードで漏れなく書く。([GitHub Docs][4])

この振り分けにしておくと、箱（Dev Container）と補助サービスは常に同一、ケースごとの依存や実装は完全に独立、という二層分離が守れる。結果として、起動も再現も速く、ケースの量産も破綻しない。

[1]: https://code.visualstudio.com/docs/devcontainers/containers?utm_source=chatgpt.com "Developing inside a Container - Visual Studio Code"
[2]: https://docs.docker.com/compose/intro/compose-application-model/?utm_source=chatgpt.com "How Compose works | Docker Docs"
[3]: https://git-scm.com/docs/gitignore?utm_source=chatgpt.com "gitignore Documentation"
[4]: https://docs.github.com/repositories/working-with-files/managing-large-files/about-git-large-file-storage?utm_source=chatgpt.com "About Git Large File Storage"
[5]: https://docs.astral.sh/uv/guides/projects/?utm_source=chatgpt.com "Working on projects | uv - Astral Docs"
[6]: https://docs.astral.sh/uv/concepts/projects/sync/?utm_source=chatgpt.com "Locking and syncing | uv"
