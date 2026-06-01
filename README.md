<div id="top"></div>

# MLX90632_rigid

MLX90632を用いた非接触温度計測用リジッド基板である．
耳装着型デバイスへの組み込みを想定し，鼓膜近傍温度および周辺温度の取得を目的として設計した．

## 使用技術一覧

<p style="display: inline">
  <img src="https://img.shields.io/badge/-KiCad-314CB0.svg?logo=kicad&style=for-the-badge&logoColor=white">
  <img src="https://img.shields.io/badge/-MLX90632-00AEEF.svg?style=for-the-badge">
  <img src="https://img.shields.io/badge/-I2C-555555.svg?style=for-the-badge">
  <img src="https://img.shields.io/badge/-JLCPCB-FDDB27.svg?style=for-the-badge&logoColor=black">
</p>

## 目次

1. [概要](#概要)
2. [基板仕様](#基板仕様)
3. [回路構成](#回路構成)
4. [ディレクトリ構成](#ディレクトリ構成)
5. [Git管理方針](#git管理方針)
6. [製造データ](#製造データ)
7. [設計上の注意](#設計上の注意)

## 概要

本リポジトリは，MLX90632を搭載したリジッド基板のKiCadプロジェクトを管理するためのものである．
MLX90632は，小型の非接触赤外線温度センサであり，センサ内部のEEPROMに保存された校正定数と測定データを用いて，外部マイコン側で対象物温度およびセンサ温度を算出する．

本基板では，耳装着型デバイスへの組み込みを想定し，MLX90632を小型リジッド基板上に実装する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 基板仕様

| 項目        | 内容                     |
| --------- | ---------------------- |
| 基板名       | MLX90632_rigid         |
| センサ       | MLX90632               |
| 通信方式      | I2C                    |
| 標準I2Cアドレス | 0x3A                   |
| 電源電圧      | 3.3 V                  |
| 視野角       | 50°                    |
| 用途        | 非接触温度計測，鼓膜近傍温度推定，熱環境評価 |
| 設計ツール     | KiCad                  |

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 回路構成

本基板は，MLX90632と外部マイコンをI2Cで接続する構成である．

```text
MCU
├── SDA
└── SCL

MLX90632
├── Thermopile
├── Ambient Temperature Sensor
├── ADC
├── RAM
└── EEPROM
```

MLX90632では，センサ内部のRAMに保存された測定データとEEPROM内の校正定数を用いて，外部マイコン側で温度を計算する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## ディレクトリ構成

```text
.
├── .git/
├── .gitignore
├── .history/                         # VSCode等の履歴フォルダ，Git管理対象外
├── jlcpcb/                           # JLCPCB発注用データ
├── MLX90632_rigid-backups/           # KiCadバックアップ，Git管理対象外
├── fp-info-cache                     # KiCadキャッシュ，Git管理対象外
├── MLX90632_rigid.kicad_pcb          # PCBレイアウト
├── MLX90632_rigid.kicad_prl          # KiCadローカル設定，Git管理対象外
├── MLX90632_rigid.kicad_pro          # KiCadプロジェクト
└── MLX90632_rigid.kicad_sch          # 回路図
```

基本的に，Gitで管理する主要ファイルは以下である．

```text
MLX90632_rigid.kicad_pro
MLX90632_rigid.kicad_sch
MLX90632_rigid.kicad_pcb
.gitignore
README.md
```

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## Git管理方針

本リポジトリでは，KiCadプロジェクト本体をGitで管理し，自動生成ファイルや個人環境依存ファイルは管理対象外とする．

主に以下をGit管理対象外とする．

```text
*.kicad_prl
fp-info-cache
*-backups/
*_backups/
*.bak
*-bak
_autosave-*
*.lck
.history/
```

また，製造出力ファイルも基本的には自動生成物として扱う．

```text
*.gbr
*.drl
*.pos
*.rpt
*.zip
```

本プロジェクトでは，自作シンボルおよび自作フットプリントを `kicad_my_library` リポジトリで管理している．  
KiCadで本プロジェクトを開く前に，`kicad_my_library` をcloneし，KiCadのライブラリ設定に登録する必要がある．

```powershell
git clone https://github.com/Cream-Pan/kicad_my_library.git
```

登録するライブラリは以下である．

```text
Footprint Library:
kicad_my_library/footprints/MyLibrary.pretty

Symbol Library:
kicad_my_library/symbols/MyLibrary.kicad_sym
```

KiCad上でのライブラリNicknameは以下に統一する．
```text
MyLibrary
```

Nicknameを変更すると，MyLibrary:C0603 のような既存の参照が壊れる可能性がある．


発注時点の製造データを残す場合は，必要に応じてGitHub Releasesや別フォルダで管理する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 製造データ

`jlcpcb` フォルダには，JLCPCBでの基板製造および部品実装を想定したデータを配置する．

主な出力対象は以下である．

* Gerber
* Drill
* BOM
* CPL

現状の `.gitignore` では，`.gbr`，`.drl`，`.zip` などの製造出力はGit管理対象外としている．
そのため，発注データをGitHub上に残したい場合は，`.gitignore` の例外設定またはGitHub Releasesでの管理を検討する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 設計上の注意

### 1．視野角

MLX90632は50°のFOVを持つため，筐体やカバーがセンサの視野を遮らないようにする．
特に耳装着型デバイスに組み込む場合は，センサ前面に樹脂や筐体内壁が入り込まないように設計する．

### 2．熱設計

MLX90632は非接触温度センサであり，センサ周辺の熱勾配や周辺部品の発熱が測定値に影響する．
そのため，以下に注意する．

* 発熱する部品をセンサ近傍に配置しない
* センサ周辺に急激な温度勾配を作らない
* センサ前面に汚れや遮蔽物を置かない
* 必要に応じて熱源からの距離や基板スリットを検討する

### 3．デカップリング

MLX90632の近傍にデカップリングコンデンサを配置する．
特に電源ノイズは赤外線温度計測の安定性に影響するため，VDDおよびGNDへの配線を短くする．

### 4．I2C通信

MLX90632はI2Cで通信する．
SDAおよびSCLの配線長，プルアップ抵抗，バス容量に注意する．
複数センサを同一バス上で扱う場合は，I2Cアドレスの設定にも注意する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

開発者情報
Name: Takato Ishii

Portfolio: https://takato-ishii.vercel.app/
