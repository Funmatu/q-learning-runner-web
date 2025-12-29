# Marathon Runner - AI Edition

![License](https://img.shields.io/badge/license-MIT-blue.svg) ![Language](https://img.shields.io/badge/language-JavaScript-yellow.svg) ![UI](https://img.shields.io/badge/Tailwind_CSS-v3.0+-38bdf8?logo=tailwind-css&logoColor=white) ![Graphic](https://img.shields.io/badge/Canvas-2D_API-E34F26?logo=html5&logoColor=white)

> **自律型AI（強化学習「Q-Learning」）を搭載した、サイバーパンク・無限ランナーゲームの技術デモンストレーション。**

本プロジェクトは、HTML5 Canvasと純粋なJavaScript（Vanilla JS）を用いて構築された、物理シミュレーションおよび強化学習（Q-Learning）のエージェント学習プラットフォームです。外部ライブラリに依存せず、ブラウザ上でリアルタイムにAIが学習・適応するプロセスを視覚化します。

## 🔗 Live Demo
[View Demo on GitHub Pages](https://funmatu.github.io/q-learning-runner-web/)

---

## 1. プロジェクト概要

`Marathon Runner`は、障害物を回避し続ける無限ランナーゲームに、**Q-Learning（強化学習）エージェント**を統合したものです。ユーザーによるマニュアル操作モードと、AIによる自動学習モード（AUTO Mode）をシームレスに切り替えることが可能です。

### 開発の目的

* **強化学習の視覚化:** 報酬系に基づくエージェントの意思決定プロセスのデモ。
* **軽量物理エンジンの実装:** 矩形衝突判定と重力加速度による挙動の再現。
* **状態空間の離散化手法の提示:** 連続的なゲーム空間をAIが理解可能な「状態」へ変換する技術の提示。

---

## 2. 技術アーキテクチャ

### 2.1 ソフトウェア・スタック

* **Frontend:** HTML5 / CSS3 (Tailwind CSS)
* **Rendering:** Canvas API (2D Context)
* **AI Engine:** Vanilla JavaScript (Q-Learning Algorithm)
* **Audio:** Web Audio API (Synthesized Square/Sawtooth waves)

### 2.2 システム構成



本システムは、以下の3つの主要コンポーネントで構成される「エージェント-環境」ループに基づいています。

1. **Environment (ゲームエンジン):** プレイヤーの物理挙動、障害物の生成、衝突判定、スコアリングを管理。
2. **State Observer (状態観測):** ゲーム内の座標データを離散化し、AIが処理可能な文字列形式の「状態（State）」を生成。
3. **RL Agent (強化学習エージェント):** 状態に基づき「待機」または「ジャンプ」を選択し、Q学習テーブルを更新。

---

## 3. 強化学習の実装詳細

本プロジェクトの核となるのは、`QAgent`クラスに実装されたQ-Learningアルゴリズムです。

### 3.1 Q学習の数理モデル

エージェントは以下のQ値更新式（ベルマン方程式の簡略版）に基づき学習を行います。

$$Q(s, a) \leftarrow Q(s, a) + \alpha \left[ r + \gamma \max_{a'} Q(s', a') - Q(s, a) \right]$$

ここで：

* **$s$**: 現在の状態
* **$a$**: 選択された行動（Wait / Jump）
* **$r$**: 報酬（生存 = +1, 衝突 = -100）
* **$\alpha$**: 学習率（Learning Rate: 0.2）
* **$\gamma$**: 割引率（Discount Factor: 0.95）

### 3.2 状態空間の離散化 (Discretization)

生の座標データをそのまま扱うと状態空間が広大になりすぎる（次元の呪い）ため、以下の関数で情報を圧縮しています。

* **距離:** 50px単位でグループ化
* **障害物の幅/高さ:** 20px単位でグループ化
* **接地判定:** 0 or 1（Boolean）
* **速度:** 整数値に丸め込み

これにより、限られた試行回数（Episode）で効率的な学習を実現しています。

### 3.3 探索と利用 (Epsilon-Greedy法)

エージェントは `epsilon = 0.1`（10%の確率でランダム行動）を採用し、学習済みの知識を利用しつつ、新たな回避タイミングを探索し続けます。

---

## 4. ゲーム物理とレンダリング

### 4.1 物理シミュレーション

* **重力:** 毎フレーム $v_y += 0.8$ の加速度を適用。
* **ジャンプ:** 上向きに $-16$ の初速度を与え、放物線運動を実現。
* **可変難易度:** 走行距離に応じて `speed` が `0.0008` ずつ増加（最大 `16`）。

### 4.2 視覚効果

* **ネオン・グロー:** `shadowBlur` と `shadowColor` を用いた発光エフェクト。
* **パーティクル・システム:** 衝突時に `Particle` クラスを生成し、物理演算による飛散とフェードアウトを実行。
* **パララックス背景:** 星の動きによる擬似的な奥行き表現。

---

## 5. セットアップと使用方法

### 5.1 実行方法

本プロジェクトは単一のHTMLファイルで完結しているため、環境構築は不要です。

1. リポジトリをクローンまたはダウンロードします。
2. `index.html` を任意のモダンブラウザで開きます。

### 5.2 操作方法

| 入力 | マニュアルモード | AUTO（AI）モード |
| --- | --- | --- |
| **Spaceキー / タップ** | ジャンプ | (無効) |
| **トグルスイッチ** | AIモードへ切り替え | マニュアルモードへ復帰 |

---

## 6. 技術的な課題と今後の展望（Roadmap）

現在の実装には以下の改善の余地があります。

* [ ] **Deep Q-Network (DQN) への拡張:** 現在のQ-Table方式から、TensorFlow.jsを用いたニューラルネットワーク実装への移行。
* [ ] **報酬設計の最適化:** 「ギリギリで避けた場合に高い報酬を与える」といったカリキュラム学習の導入。
* [ ] **状態空間の拡張:** 複数の障害物が連続する場合の認識能力の向上。
* [ ] **学習データの保存:** 学習済みのQ-TableをLocalStorageやJSONでエクスポート/インポートする機能。

---

## 7. ライセンス

[MIT License](LICENSE)

Copyright (c) 2025 Funmatu
