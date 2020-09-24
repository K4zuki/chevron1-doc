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

GreenPAK SLG46826の基本的な機能と基板の構成について解説します。「GreenPAK勝手にアプリケーションノート」
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

::: {.table width=[0.3,0.7] noheader=true}
: SLG46826諸元（抜粋） {#tbl:slg46826-brief}

|            電源電圧(VDD)            |            2.5〜5.0 [V]            |
|:-----------------------------------:|:----------------------------------:|
|           電源電圧(VDD2)            | 1.8〜5.0 [V]（[*1](#brief-note1)） |
|                IO数                 |                 17                 |
|   最大LUT数（[*2](#brief-note2)）   |                 19                 |
|   最大DFF数（[*2](#brief-note2)）   |                 17                 |
| 最大CNT/DLY数（[*2](#brief-note2)） |                 8                  |
|           内蔵オシレータ            | 1x 2.048KHz、1x 2.048MHz、1x 25MHz |
|        アナログコンパレータ         |    2x High Speed、2x Low Power     |
|                通信                 |               I^2^C                |

:::

- [*1]{#brief-note1}: VDD2はVDDを超えない（VDD&ge;VDD2）こと
- [*2]{#brief-note2}: 単一マクロセル内のLUT/DFF/CNT/DLYは同時使用できないことに注意

さらなる詳細やデータシートはDialog社のページを参照ください（<https://www.dialog-semiconductor.com/products/slg46826>）。

### ピン配置がパッケージで真逆

同一品番でパッケージが異なるものはラインナップにいくつかありますが、SLG46826もその一つです。
ただしこのICはパッケージ間でピン配置が完全に逆順です([@tbl:slg46826-pinout])。


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

# あとがき {-}

- 原稿PDFはこのQRコードからたどってください ![](images/QRcode.png){#fig:manuscript width=30%}
- `{\large`{=latex}[Stargate SG-1]{.underline}っていう海外SFドラマシリーズ知ってる人いますか？`}`{=latex}
- 表紙の画像は
  [<https://commons.wikimedia.org/wiki/File:MilkyWay_Stargate_blank.svg>]{.underline}から拝借しました。
  [[CC BY-SA 3.0 ライセンス]{.underline}](https://creativecommons.org/licenses/by-sa/3.0/deed.en)です。

## 謝辞 {-}

「Chevron1」の企画・設計に協力してくださった諸氏に謝意を示します（敬称略）。

- そんそん(<https://twitter.com/sonson1919>)
- あおいさや(<https://twitter.com/La_zlo>)
- 余熱＠れすぽん(<https://twitter.com/yone2_net>)
- niszet(<https://twitter.com/niszet0>)
- Kashiken(<https://twitter.com/kashiken>)

::: rmnote

| address hex (decimal) | address by bit | description                                                                                                                                                                                                                                                                                                                                                     |
|:---------------------:|:---------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|      `B5` (181)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1448>',attr:'RW'},{bits:1,name:'<1449>',attr:'RW'},{bits:1,name:'<1450>',attr:'RW'},{bits:1,name:'<1451>',attr:'RW'},{bits:1,name:'<1452>',attr:'RW'},{bits:1,name:'<1453>',attr:'RW'},{bits:1,name:'<1454>',attr:'RW'},{bits:1,name:'<1455>',attr:'RW'}]}"/>               |
|                       | reg<1455>      | LDO0 Discharge resistor Enable (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                                      |
|                       |                | LDO Discharge resistor Enable (for SLG46583)                                                                                                                                                                                                                                                                                                                    |
|                       | reg<1454>      | LDO0 Over-current & Short-current Detection Enable (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                  |
|                       |                | LDO Over-current & Short-current Detection Enable (for SLG46583)                                                                                                                                                                                                                                                                                                |
|                       | reg<1453>      | LDO0 Start-up Ramping Slope Divide Enable (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                           |
|                       |                | LDO Start-up Ramping Slope Divide Enable (for SLG46583)                                                                                                                                                                                                                                                                                                         |
|                       | reg<1452>      | LDO0 PS_Mode_Gate (LDO0 turn-on/off is controlled by LDO0_EN matrix output) (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                         |
|                       |                | LDO PS_Mode_Gate (LDO0 turn-on/off is controlled by LDO0_EN matrix output) (for SLG46583)                                                                                                                                                                                                                                                                       |
|                       | reg<1451>      | LDO1 Discharge resistor Enable (for SLG46580)                                                                                                                                                                                                                                                                                                                   |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1450>      | LDO1 Over-current & Short-current Detection Enable (for SLG46580)                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1449>      | LDO1 Start-up Ramping Slope Divide Enable (for SLG46580)                                                                                                                                                                                                                                                                                                        |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1448>      | LDO1 PS_Mode_Gate (LDO1 turn-on/off is controlled by LDO1_EN matrix output) (for SLG46580)                                                                                                                                                                                                                                                                      |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `B6` (182)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1456>',attr:'RW'},{bits:1,name:'<1457>',attr:'RW'},{bits:1,name:'<1458>',attr:'RW'},{bits:1,name:'<1459>',attr:'RW'},{bits:1,name:'<1460>',attr:'RW'},{bits:1,name:'<1461>',attr:'RW'},{bits:1,name:'<1452>',attr:'RW'},{bits:1,name:'<1463>',attr:'RW'}]}"/>               |
|                       | reg<1463>      | LDO2 Discharge resistor Enable (for SLG46580)                                                                                                                                                                                                                                                                                                                   |
|                       |                | LDO1 Discharge resistor Enable (for SLG46582)                                                                                                                                                                                                                                                                                                                   |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1462>      | LDO2 Over-current & Short-current Detection Enable (for SLG46580)                                                                                                                                                                                                                                                                                               |
|                       |                | LDO1 Over-current & Short-current Detection Enable (for SLG46582)                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1461>      | LDO2 Start-up Ramping Slope Divide Enable (for SLG46580)                                                                                                                                                                                                                                                                                                        |
|                       |                | LDO1 Start-up Ramping Slope Divide Enable (for SLG46582)                                                                                                                                                                                                                                                                                                        |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1460>      | LDO2 PS_Mode_Gate (LDO2 turn-on/off is controlled by LDO2_EN matrix output) (for SLG46580)                                                                                                                                                                                                                                                                      |
|                       |                | LDO1 PS_Mode_Gate (LDO1 turn-on/off is controlled by LDO1_EN matrix output) (for SLG46582)                                                                                                                                                                                                                                                                      |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1459>      | LDO3 Discharge resistor Enable (for SLG46580)                                                                                                                                                                                                                                                                                                                   |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1458>      | LDO3 Over-current & Short-current Detection Enable (for SLG46580)                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1457>      | LDO3 Start-up Ramping Slope Divide Enable (for SLG46580)                                                                                                                                                                                                                                                                                                        |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1456>      | LDO3 PS_Mode_Gate (LDO3 turn-on/off is controlled by LDO3_EN matrix output) (for SLG46580)                                                                                                                                                                                                                                                                      |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `B7` (183)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1488>',attr:'RW'},{bits:1,name:'<1489>',attr:'RW',type:1},{bits:1,name:'<1490>',attr:'RW',type:1},{bits:1,name:'<1491>',attr:'RW'},{bits:1,name:'<1492>',attr:'RW'},{bits:1,name:'<1493>',attr:'RW'},{bits:1,name:'<1494>',attr:'RW'},{bits:1,name:'<1495>',attr:'RW'}]}"/> |
|                       | reg<1495>      | LDO0/1 VIN connection enable to ACMP0 (for SLG46580)                                                                                                                                                                                                                                                                                                            |
|                       |                | LDO0 VIN connection enable to ACMP0 (for SLG46582)                                                                                                                                                                                                                                                                                                              |
|                       |                | LDO VIN connection enable to ACMP0 (for SLG46583)                                                                                                                                                                                                                                                                                                               |
|                       | reg<1494>      | LDO2/3 VIN connection enable to ACMP1 (for SLG46580)                                                                                                                                                                                                                                                                                                            |
|                       |                | LDO1 VIN connection enable to ACMP1 (for SLG46582)                                                                                                                                                                                                                                                                                                              |
|                       |                | LDO VIN connection enable to ACMP1 (for SLG46583)                                                                                                                                                                                                                                                                                                               |
|                       | reg<1493>      | TS output connection enable to ACMP2                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1492>      | LDO0 VOUT output connection enable to ACMP3 (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                         |
|                       |                | LDO VOUT output connection enable to ACMP3 (for SLG46583)                                                                                                                                                                                                                                                                                                       |
|                       | reg<1491>      | LDO2 VOUT output connection enable to ACMP3 (for SLG46580)                                                                                                                                                                                                                                                                                                      |
|                       |                | LDO1 VOUT output connection enable to ACMP3 (for SLG46582)                                                                                                                                                                                                                                                                                                      |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1490>      | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1489>      | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1488>      | ACMP1 100 uA Current Source Enable                                                                                                                                                                                                                                                                                                                              |
|      `C6` (198)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1584>',attr:'RW'},{bits:1,name:'<1585>',attr:'RW'},{bits:3,name:'<1588:1586>',attr:'RW'},{bits:3,name:'<1591:1589>',attr:'RW'}]}"/>                                                                                                                                         |
|                       | reg<1591:1589> | LDO0/1 VDD minimum Power Selection for LDO0 (for SLG46580)                                                                                                                                                                                                                                                                                                      |
|                       |                | LDO0 VDD minimum Power Selection for LDO0 (for SLG46582)                                                                                                                                                                                                                                                                                                        |
|                       |                | LDO VDD minimum Power Selection for LDO (for SLG46583)                                                                                                                                                                                                                                                                                                          |
|                       | reg<1588:1586> | LDO0/1 VDD minimum Power Selection for LDO1 (for SLG46580)                                                                                                                                                                                                                                                                                                      |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1585>      | UVLO0_HW_enable (Enable UVLO0 output Hard-Wire connection to LDO0/1) (for SLG46580)                                                                                                                                                                                                                                                                             |
|                       |                | UVLO0_HW_enable (Enable UVLO0 output Hard-Wire connection to LDO0) (for SLG46582)                                                                                                                                                                                                                                                                               |
|                       |                | UVLO_HW_enable0 (Enable UVLO output from ACMP0 Hard-Wire connection to LDO) (for SLG46583)                                                                                                                                                                                                                                                                      |
|                       | reg<1584>      | UVLO1_HW_enable (Enable UVLO1 output Hard-Wire connection to LDO2/3) (for SLG46580)                                                                                                                                                                                                                                                                             |
|                       |                | UVLO1_HW_enable (Enable UVLO1 output Hard-Wire connection to LDO1) (for SLG46582)                                                                                                                                                                                                                                                                               |
|                       |                | UVLO_HW_enable1 (Enable UVLO output from ACMP1 Hard-Wire connection to LDO) (for SLG46583)                                                                                                                                                                                                                                                                      |
|      `C7` (199)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1592>',attr:'RW'},{bits:2,name:'<1594:1593>',attr:'RW',type:1},{bits:5,name:'<1599:1595>',attr:'RW'}]}"/>                                                                                                                                                                   |
|                       | reg<1599:1595> | Vref Selection V1 Value                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1594:1593> | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1592>      | LDO0 Start-up Ramping Slope Selection (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                               |
|                       |                | LDO Start-up Ramping Slope Selection (for SLG46583)                                                                                                                                                                                                                                                                                                             |
|      `C8` (200)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1600>',attr:'RW'},{bits:2,name:'<1602:1601>',attr:'RW',type:1},{bits:5,name:'<1607:1603>',attr:'RW'}]}"/>                                                                                                                                                                   |
|                       | reg<1607:1603> | LDO1 Vref Selection (for SLG46580)                                                                                                                                                                                                                                                                                                                              |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1602:1601> | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1600>      | LDO1 Start-up Ramping Slope Selection (for SLG46580)                                                                                                                                                                                                                                                                                                            |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `C9` (201)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1608>',attr:'RW'},{bits:2,name:'<1610:1609>',attr:'RW',type:1},{bits:5,name:'<1615:1611>',attr:'RW'}]}"/>                                                                                                                                                                   |
|                       | reg<1615:1611> | LDO2 Vref Selection (for SLG46580);                                                                                                                                                                                                                                                                                                                             |
|                       |                | LDO1 Vref Selection (for SLG46582);                                                                                                                                                                                                                                                                                                                             |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1610:1609> | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1608>      | LDO2 Start-up Ramping Slope Selection (for SLG46580)                                                                                                                                                                                                                                                                                                            |
|                       |                | LDO1 Start-up Ramping Slope Selection (for SLG46582)                                                                                                                                                                                                                                                                                                            |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|      `CA` (202)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1616>',attr:'RW'},{bits:2,name:'<1618:1617>',attr:'RW',type:1},{bits:5,name:'<1623:1619>',attr:'RW'}]}"/>                                                                                                                                                                   |
|                       | reg<1623:1619> | LDO3 Vref Selection (for SLG46580);                                                                                                                                                                                                                                                                                                                             |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1618:1617> | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1616>      | LDO3 Start-up Ramping Slope Selection (for SLG46580)                                                                                                                                                                                                                                                                                                            |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `E0` (224)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1792>',attr:'RW'},{bits:1,name:'<1793>',attr:'RW'},{bits:1,name:'<1794>',attr:'RW'},{bits:5,name:'<1599:1595>',attr:'RW'}]}"/>                                                                                                                                              |
|                       | reg<1799:1795> | LDO0 2nd Vref Selection (for SLG46580 and SLG46582)                                                                                                                                                                                                                                                                                                             |
|                       |                | LDO 2nd Vref Selection (for SLG46583)                                                                                                                                                                                                                                                                                                                           |
|                       | reg<1794>      | Mode1_EN_Gate (By this bit=1, Matrix Output: LDO MODE1_Enable for LDO0/1/2/3 will be enabled.) (for SLG46580);                                                                                                                                                                                                                                                  |
|                       |                | Mode1_EN_Gate (By this bit=1, Matrix Output: LDO MODE1_Enable for LDO0/1 will be enabled.) (for SLG46582);                                                                                                                                                                                                                                                      |
|                       |                | Mode1_EN_Gate (By this bit=1, Matrix Output: LDO MODE1_Enable for LDO will be enabled.) (for SLG46583)                                                                                                                                                                                                                                                          |
|                       | reg<1793>      | LDO0 VOUT_SEL_Gate (for SLG46580 and SLG46582);                                                                                                                                                                                                                                                                                                                 |
|                       |                | LDO VOUT_SEL_Gate (for SLG46583)                                                                                                                                                                                                                                                                                                                                |
|                       | reg<1792>      | LDO0_EN_Gate (By this bit=1, Matrix Output: LDO0_EN will be enabled.) (for SLG46580 and SLG46582);                                                                                                                                                                                                                                                              |
|                       |                | LDO_EN_Gate (By this bit=1, Matrix Output: LDO_EN will be enabled.) (for SLG46583)                                                                                                                                                                                                                                                                              |
|      `E1` (225)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1800>',attr:'RW'},{bits:1,name:'<1801>',attr:'RW'},{bits:1,name:'<1802>',attr:'RW',type:1},{bits:5,name:'<1807:1803>',attr:'RW'}]}"/>                                                                                                                                       |
|                       | reg<1807:1803> | LDO1 2nd Vref Selection (for SLG46580);                                                                                                                                                                                                                                                                                                                         |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1802>      | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1801>      | LDO1 VOUT_SEL_Gate (for SLG46580)                                                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1800>      | LDO1_EN_Gate (By this bit=1, Matrix Output: LDO1_EN will be enabled.) (for SLG46580)                                                                                                                                                                                                                                                                            |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `E2` (226)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1808>',attr:'RW'},{bits:1,name:'<1809>',attr:'RW'},{bits:1,name:'<1810>',attr:'RW',type:1},{bits:5,name:'<1815:1811>',attr:'RW'}]}"/>                                                                                                                                       |
|                       | reg<1815:1811> | LDO2 2nd Vref Selection (for SLG46580)                                                                                                                                                                                                                                                                                                                          |
|                       |                | LDO1 2nd Vref Selection (for SLG46582)                                                                                                                                                                                                                                                                                                                          |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1810>      | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1809>      | LDO2 VOUT_SEL_Gate (for SLG46580)                                                                                                                                                                                                                                                                                                                               |
|                       |                | LDO1 VOUT_SEL_Gate (for SLG46582)                                                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|                       | reg<1808>      | LDO2_EN_Gate (By this bit=1, Matrix Output: LDO2_EN will be enabled.) (for SLG46580)                                                                                                                                                                                                                                                                            |
|                       |                | LDO1_EN_Gate (By this bit=1, Matrix Output: LDO1_EN will be enabled.) (for SLG46582)                                                                                                                                                                                                                                                                            |
|                       |                | Reserved (for SLG46583)                                                                                                                                                                                                                                                                                                                                         |
|      `E3` (227)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1816>',attr:'RW'},{bits:1,name:'<1817>',attr:'RW'},{bits:1,name:'<1818>',attr:'RW',type:1},{bits:5,name:'<1823:1819>',attr:'RW'}]}"/>                                                                                                                                       |
|                       | reg<1823:1819> | LDO3 2nd Vref Selection (for SLG46580)                                                                                                                                                                                                                                                                                                                          |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1818>      | Reserved                                                                                                                                                                                                                                                                                                                                                        |
|                       | reg<1817>      | LDO3 VOUT_SEL_Gate (for SLG46580)                                                                                                                                                                                                                                                                                                                               |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|                       | reg<1816>      | LDO3_EN_Gate (By this bit=1, Matrix Output: LDO3_EN will be enabled.) (for SLG46580);                                                                                                                                                                                                                                                                           |
|                       |                | Reserved (for SLG46582 and SLG46583)                                                                                                                                                                                                                                                                                                                            |
|      `E4` (228)       |                | <img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:8,name:'<1831:1824>',attr:'RW',type:1}]}"/>                                                                                                                                                                                                                                          |
|                       | reg<1831:1824> | Reserved                                                                                                                                                                                                                                                                                                                                                        |

<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1456>',attr:'RW'},{bits:1,name:'<1457>',attr:'RW'},{bits:1,name:'<1458>',attr:'RW'},{bits:1,name:'<1459>',attr:'RW'},{bits:1,name:'<1460>',attr:'RW'},{bits:1,name:'<1461>',attr:'RW'},{bits:1,name:'<1452>',attr:'RW'},{bits:1,name:'<1463>',attr:'RW'}]}"/>
<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1488>',attr:'RW'},{bits:1,name:'<1489>',attr:'RW',type:1},{bits:1,name:'<1490>',attr:'RW',type:1},{bits:1,name:'<1491>',attr:'RW'},{bits:1,name:'<1492>',attr:'RW'},{bits:1,name:'<1493>',attr:'RW'},{bits:1,name:'<1494>',attr:'RW'},{bits:1,name:'<1495>',attr:'RW'}]}"/>
<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1800>',attr:'RW'},{bits:1,name:'<1801>',attr:'RW'},{bits:1,name:'<1802>',attr:'RW',type:1},{bits:5,name:'<1807:1803>',attr:'RW'}]}"/>
<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1808>',attr:'RW'},{bits:1,name:'<1809>',attr:'RW'},{bits:1,name:'<1810>',attr:'RW',type:1},{bits:5,name:'<1815:1811>',attr:'RW'}]}"/>
<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:1,name:'<1816>',attr:'RW'},{bits:1,name:'<1817>',attr:'RW'},{bits:1,name:'<1818>',attr:'RW',type:1},{bits:5,name:'<1823:1819>',attr:'RW'}]}"/>
<img src="https://svg.wavedrom.com/{config:{bits:'8',lanes:'1'},reg:[{bits:8,name:'<1831:1824>',attr:'RW',type:1}]}"/>

:::
