::: rmnote
![](images/MilkyWay_Stargate_blank.svg)
:::

# まえがき {.unnumbered}
## このドキュメントは何

この本は、Dialog Semiconductor社GreenPAKシリーズSLG46826[G]{.underline}（TSSOP）を同[V]{.underline}（STQFN）
のピン配置に変換し、ライタ[^gpak-dip-writer-silego][^gpak-dip-writer-ssci]に挿せるようにするDIP化基板
「Chevron1」について解説するドキュメントです。

「GreenPAK勝手に勉強会」というTwitterフレンズの集まり（サークルではない）の協力を得て
作りました。

## このドキュメントのゴール

GreenPAK SLG46826の基本的な機能と、変換基板「Chevron1」の構成について解説します。「GreenPAK勝手にアプリケーションノート」
シリーズ[^silego-trial-report][^gpak-personal-appsnote]をあらかじめ読んでいただくことをおすすめします。

[^silego-trial-report]: GreenPAK SLG46826 試してみたレポート,\
<https://github.com/K4zuki/silego-trial-report/releases/tag/build-d077fe9>
[^gpak-personal-appsnote]: GreenPAK勝手にアプリケーションノート,\
<https://github.com/K4zuki/greenpak-personal-appsnote/releases/tag/build-c55ed02>
[^gpak-dip-writer-silego]: GreenPAK DIP Development Board SLG4DVKDIP,\
<http://www.silego.com/buy/index.php?main_page=product_info&cPath=66_68&products_id=691>
[^gpak-dip-writer-ssci]: GreenPAK DIP Development Board SLG4DVKDIP, \
<https://www.switch-science.com/catalog/4111/>

## GreenPAK SLG46826について

SLG46826は、Silego Technology社（現在はDialog Semiconductor社に吸収）が開発した
PLD[^pld-stands-for]、「GreenPAK」の１品種です。PLDといっても回路規模はとても小さく、
たとえばLUTは2〜4入力のマクロセルが混在し、合計20個程度しかありません。

[^pld-stands-for]: Programmable Logic Device, プログラム可能ロジックデバイス

他のラインナップと違い、[この品種だけ]{.underline}はフラッシュROMを搭載しており、I^2^Cバスから内部回路の
書き換えが可能となっています。パッケージには0.4ミリピッチ20ピンQFNと
0.65ミリピッチ20ピンTSSOPのオプションがあります。なお、この基板ではTSSOPパッケージを採用しています。

::: {.table width=[0.1,0.4,0.4,0.1] noheader=true}
: SLG46826諸元（抜粋） {#tbl:slg46826-brief}

|     |            [**電源電圧(VDD)**]{.underline}            |            2.5〜5.0 [V]            |     |
|:---:|:-----------------------------------------------------:|:----------------------------------:|:---:|
|     |           [**電源電圧(VDD2)**]{.underline}            | 1.8〜5.0 [V]（[*1](#brief-note1)） |     |
|     |                [**IO数**]{.underline}                 |                 17                 |     |
|     |   [**最大LUT数**]{.underline}（[*2](#brief-note2)）   |                 19                 |     |
|     |   [**最大DFF数**]{.underline}（[*2](#brief-note2)）   |                 17                 |     |
|     | [**最大CNT/DLY数**]{.underline}（[*2](#brief-note2)） |                 8                  |     |
|     |           [**内蔵オシレータ**]{.underline}            | 1x 2.048KHz、1x 2.048MHz、1x 25MHz |     |
|     |        [**アナログコンパレータ**]{.underline}         |    2x High Speed、2x Low Power     |     |
|     |                [**通信**]{.underline}                 |               I^2^C                |     |

:::

- [*1]{#brief-note1}: VDD2はVDDを超えない（VDD&ge;VDD2）こと
- [*2]{#brief-note2}: 単一マクロセル内のLUT/DFF/CNT/DLYは同時使用できないことに注意

さらなる詳細やデータシートはDialog社のページを参照ください（<https://www.dialog-semiconductor.com/products/slg46826>）。

### ピン配置がパッケージで真逆

同一品番でパッケージが異なるものはラインナップにいくつかありますが、SLG46826もその一つです。
ただしこのICはパッケージ間でピン配置が完全に逆順です(次ページ[@fig:pinout-diff] / [@tbl:slg46826-pinout])。

\newpage

::: {#fig:pinout-diff}
::: {.table width=[1.0] noheader=true}

|   ![QFNパッケージ](images/slg46826-pinout-qfn.png){width=120mm}   |
|:-----------------------------------------------------------------:|
| ![TSSOPパッケージ](images/slg46826-pinout-tssop.png){width=120mm} |

:::
SLG46826のピン配置
:::

\newpage

[Pinout](data/pinout.csv){.table #tbl:slg46826-pinout alignment=CCCCC width=[0.15,0.15,0.2,0.3,0.2]}

\toc

\newpage

# 変換基板「Chevron1」
## 機能概要

*Chevron1（しぇぶろん・わん）* は、SLG46826Gを20ピン、300mil幅にDIP化し、オフィシャルDIP化基板
"SLG46826V-DIP"[^slg46826-dip]とピン互換にしたものです。ピン互換に加えて、
VDDとVDD2をブリッジするジャンパ、コンパレータ基準電圧入力（VREF）にパスコンを接続するジャンパ、
I2Cバスプルアップ用ジャンパ、I2Cスレーブアドレス設定用ジャンパ、I2C接続用パッド、
ISPライタ[^slgdvkisp]に接続できるコネクタを備えています（@fig:chevron1-pcb-outlook）。

![基板外観](images/chevron1-pcb.png){width=120mm #fig:chevron1-pcb-outlook}

[^slg46826-dip]: 20-pin DIP Proto Board,\
<http://www.silego.com/buy/index.php?main_page=product_info&cPath=66_68&products_id=721>
[^slgdvkisp]: GreenPAK Development Board for In-System Programmability,\
<http://www.silego.com/buy/index.php?main_page=product_info&cPath=66_74&products_id=727>

\newpage

![ピン配置](images/chevron1-pinout.png){width=120mm #fig:chevron1-pcb-pinout}

### VDD-VDD2ブリッジジャンパ

SLG46826は2系統の電源を取り扱えますが、まとめて1系統で使いたい場合にVDD2をVDDに直結できるジャンパを設けました。
各IOピンがどちらの電源に属しているかは[@tbl:slg46826-pinout]を参照してください。

### I^2^Cバスプルアップジャンパ

I^2^Cバス（SDA/SCL）を4.7K&Omega;でVDDに接続するジャンパです。
I^2^Cバスマスタがプルアップを用意していない場合に使います。

### I^2^Cスレーブアドレス設定ジャンパ

SLG46826はIO2/3/4/5ピンの一部または全部をI^2^Cスレーブアドレス設定に使うことができます。これらのジャンパをショートすると
それぞれのピンを4.7K&Omega;でVDDにプルアップします。

この4ピンはスレーブアドレスの上位4ビットに割り当てられます。IO5がMSB、IO3がLSBです（[@tbl:slave-address-mapping]）

::: {.table width=[0.1,0.1,0.1,0.1,0.3,0.3]}
: I^2^Cスレーブアドレス割り当て一覧 {#tbl:slave-address-mapping}

| IO5 | IO4 | IO3 | IO2 | **8bit** Address |  Note   |
|:---:|:---:|:---:|:---:|:----------------:|:-------:|
|  0  |  0  |  0  |  1  |      `0x10`      | Default |
|  0  |  0  |  1  |  0  |      `0x20`      |         |
|  0  |  0  |  1  |  1  |      `0x30`      |         |
|  0  |  1  |  0  |  0  |      `0x40`      |         |
|  :  |  :  |  :  |  :  |        :         |         |
|  1  |  1  |  0  |  0  |      `0xC0`      |         |
|  1  |  1  |  0  |  1  |      `0xD0`      |         |
|  1  |  1  |  1  |  0  |      `0xE0`      |         |

:::

### 基準電圧入力 パスコン接続ジャンパ

IO1ピンにバイパスコンデンサ0.1&micro;Fを接続するジャンパです。アナログコンパレータの＋入力に
IO1（Vref IN）を使うときに使用します。

### ISPライタ接続用コネクタ

ISPライタに付属するケーブルハーネスを使用するためのコネクタです。JSTの表面実装タイプ4ピンSHコネクタ
です。

#### **代替ISPケーブル** {.unnumbered}

ISPライタ付属ケーブルはSHではなく互換（後継？）のSRコネクタ[^isp-cable]のようです。
SRコネクタには"みみ"がついておらず、一度コネクタに挿すと抜くのに*とても*難儀し、少し不便です。
代替品として、スイッチサイエンスで売られているQwiicケーブルや秋月のコネクタ付コード
を使えます（@tbl:jst-sh-harness）。

特にスイッチサイエンスで売られている"Qwiicケーブル（Qwiic - 4ピンオス）"は片側がジャンパピンなので
DIPライタ・ISPライタのどちらにもつなげることができます。ワイヤの配色がQwiic準拠なので、
筆者はコンタクトを入れ替えて使っています。

::: {.table width=[0.25,0.22,0.6]}
: SHコネクタ互換ケーブル {#tbl:jst-sh-harness}

| ショップ           | 商品名                                                     | URL                                            |
|:-------------------|:-----------------------------------------------------------|:-----------------------------------------------|
| スイッチサイエンス | Qwiicケーブル（50mm）                                      | <https://www.switch-science.com/catalog/3542/> |
|                    | Qwiicケーブル（100mm）                                     | <https://www.switch-science.com/catalog/3543/> |
|                    | Qwiicケーブル（Qwiic - 4ピンオス）                         | <https://www.switch-science.com/catalog/3541/> |
|                    | Qwiic互換 JST SH型 4ピンコネクタ付きジャンパワイヤ（オス） | <https://www.switch-science.com/catalog/5890/> |
| 秋月               | コネクタ付コード 4P (青白黒赤)                             | <http://akizukidenshi.com/catalog/g/gC-15383/> |

:::

[^isp-cable]: この系統
（<https://www.digikey.com/product-detail/en/jst-sales-america-inc/A04SR04SR30K152B/455-3014-ND/6009390>）
ぽい

### GreenPAK Designer IDE上の扱い

SLG46826はパッケージで型番が分かれていますが、IDEが認識する[内部的なIDは同一]{.underline}なので、
新規に回路を設計するときはSTQFNパッケージを選んだほうがピン配置で混乱せずにすみます（[@fig:ide-develop]）。

![STQFN](images/ide-chip-selection.png){#fig:ide-develop}

# あとがき {-}

- 原稿PDFはこのQRコードからたどってください ![](images/QRcode.png){#fig:manuscript width=30%}
- `{\large`{=latex}[Stargate SG-1]{.underline}っていう海外SFドラマシリーズ知ってる人いますか？`}`{=latex}
- 表紙の画像は
  [<https://commons.wikimedia.org/wiki/File:MilkyWay_Stargate_blank.svg>]{.underline}から拝借しました。
  [[CC BY-SA 3.0 ライセンス]{.underline}](https://creativecommons.org/licenses/by-sa/3.0/deed.en)です。

## 謝辞 {-}

「Chevron1」の企画・設計・製造に協力してくださった「GreenPAK勝手に勉強会」諸氏に謝意を表します（敬称略）。

- そんそん(<https://twitter.com/sonson1919>)
- あおいさや(<https://twitter.com/La_zlo>)
- 余熱＠れすぽん(<https://twitter.com/yone2_net>)
- niszet(<https://twitter.com/niszet0>)
- Kashiken(<https://twitter.com/kashiken>)
