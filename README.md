# sparkjs-skill

[SparkJS](https://sparkjs.dev/) (3D Gaussian Splat rendering library) の知識を Claude Code に追加するスキルです。

## 概要

SparkJS は Three.js と統合された、ブラウザ上で 3D Gaussian Splats をレンダリング・操作するための JavaScript ライブラリです。このスキルを導入すると、Claude Code が SparkJS の API・設計パターン・最適化手法を理解した上でコード生成やデバッグを行えるようになります。

## 対応する機能

- **SparkRenderer** / **SplatMesh** / **PackedSplats** / **ExtSplats** の全 API
- `.ply`, `.spz`, `.splat`, `.ksplat`, `.sog`, `.rad` 形式のスプラットファイル読み込み
- **SDF によるスプラット編集** (球・ボックス・平面・楕円体・円柱・カプセル・円錐)
- **プロシージャルスプラット生成** (グリッド、テキスト、画像、パーティクル)
- **Level-of-Detail (LoD)** ストリーミング・チューニング
- **Dyno シェーダーグラフシステム** (117+ 関数の stdlib リファレンス付き)
- **SparkControls** (FPS / ポインタ / ゲームパッド / モバイルタッチ)
- プラットフォーム別パフォーマンス最適化ガイド

## インストール

### 方法 1: git clone (推奨)

任意の環境で以下を実行するだけで使えます:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/shi3z/sparkjs-skill.git ~/.claude/skills/sparkjs
```

### 方法 2: Claude Code のプロジェクト設定

プロジェクト単位で使いたい場合は、プロジェクトの `.claude/skills/` にクローン:

```bash
mkdir -p .claude/skills
git clone https://github.com/shi3z/sparkjs-skill.git .claude/skills/sparkjs
```

### 方法 3: 手動コピー

リポジトリから `SKILL.md` と `references/` をダウンロードして `~/.claude/skills/sparkjs/` に配置:

```
~/.claude/skills/sparkjs/
├── SKILL.md
└── references/
    ├── dyno-api.md
    └── spark-renderer-options.md
```

### アップデート

```bash
cd ~/.claude/skills/sparkjs && git pull
```

## ファイル構成

```
sparkjs/
├── SKILL.md                           # メインスキル定義
│   ├── Quick Start テンプレート
│   ├── Core API リファレンス
│   ├── SDF 編集・プロシージャル生成
│   ├── LoD・パフォーマンス・コントロール
│   └── Dyno シェーダー概要
└── references/
    ├── dyno-api.md                    # Dyno stdlib 全関数リファレンス
    └── spark-renderer-options.md      # SparkRenderer 全コンストラクタオプション
```

## 使い方

スキルをインストール後、SparkJS に関する質問やコード生成を依頼するだけで自動的にトリガーされます。

### トリガー例

- 「SparkJS で蝶のモデルを表示するページを作って」
- 「SplatMesh にレイキャストを実装して」
- 「SDF で球体領域のスプラットを削除したい」
- 「LoD ストリーミングを有効にしてパフォーマンスを改善して」
- 「Dyno でカスタムシェーダーを書いて」

## Quick Start

SparkJS の最小構成コード:

```html
<script type="importmap">
  {
    "imports": {
      "three": "https://cdnjs.cloudflare.com/ajax/libs/three.js/0.180.0/three.module.js",
      "@sparkjsdev/spark": "https://sparkjs.dev/releases/spark/2.0.0/spark.module.js"
    }
  }
</script>
<script type="module">
  import * as THREE from "three";
  import { SparkRenderer, SplatMesh } from "@sparkjsdev/spark";

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(60, innerWidth / innerHeight, 0.01, 1000);
  const renderer = new THREE.WebGLRenderer();
  renderer.setSize(innerWidth, innerHeight);
  document.body.appendChild(renderer.domElement);

  const spark = new SparkRenderer({ renderer });
  scene.add(spark);

  const splat = new SplatMesh({ url: "https://sparkjs.dev/assets/splats/butterfly.spz" });
  splat.quaternion.set(1, 0, 0, 0);
  splat.position.set(0, 0, -3);
  scene.add(splat);

  renderer.setAnimationLoop(() => renderer.render(scene, camera));
</script>
```

## ライセンス

MIT
