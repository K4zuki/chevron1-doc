
# まえがき {.unnumbered}
#### **このドキュメントは何** {.unnumbered}

この本は、Dialog Semiconductor社GreenPAKシリーズSLG46826[G]{.underline}（TSSOP）を同[V]{.underline}（STQFN）
のピン配置に変換し、ライタ[^gpak-dip-writer-silego][^gpak-dip-writer-ssci]に挿せるようにするDIP化基板
「Chevron1」について解説するドキュメントです。

「GreenPAK勝手に勉強会」というTwitterフレンズの集まり（サークルではない）の協力を得て
作りました。

#### **このドキュメントのゴール** {.unnumbered}

GreenPAK SLG46826の基本的な機能と基板の構成について解説します。

<!--[^silego-trial-report]: https://github.com/K4zuki/silego-trial-report/releases/tag/build-d077fe9-->
[^gpak-dip-writer-silego]: GreenPAK DIP Development Board SLG4DVKDIP,\
<http://www.silego.com/buy/index.php?main_page=product_info&cPath=66_68&products_id=691>
[^gpak-dip-writer-ssci]: GreenPAK DIP Development Board SLG4DVKDIP, <https://www.switch-science.com/catalog/4111/>

\toc

# GreenPAK SLG46826とは

SLG46826は、Silego Technology社（現在はDialog Semiconductor社に吸収）が開発した
プログラム可能ロジックデバイス（PLD）の１品種です。
PLDといっても回路規模はとても小さく、たとえばLUTブロックは20個程度しかありません。さらにそのLUTも
2〜4入力の素子が混在しています。

他のラインナップと違い、[この品種だけ]{.underline}はフラッシュROMを搭載しており、I^2^Cバスから内部回路の
書き換えが可能となっています。パッケージには0.4ミリピッチ20ピンQFNと
0.65ミリピッチ20ピンTSSOPのオプションがあります。なお、この基板ではTSSOPパッケージを採用しています。

::: {.table width=[0.3,0.7] noheader=true}
: SLG46826諸元（抜粋） {#tbl:slg46826-brief}

|                開発                 | Silego Technology (Dialog Semiconductor) |
|:-----------------------------------:|:----------------------------------------:|
|           電源電圧(VDD1)            |               2.5〜5.0 [V]               |
|           電源電圧(VDD2)            |    1.8〜5.0 [V]（[*1](#brief-note1)）    |
|                IO数                 |                    17                    |
|   最大LUT数（[*2](#brief-note2)）   |                    19                    |
|   最大DFF数（[*2](#brief-note2)）   |                    17                    |
| 最大CNT/DLY数（[*2](#brief-note2)） |                    8                     |
|           内蔵オシレータ            |    1x 2.048KHz、1x 2.048MHz、1x 25MHz    |
|        アナログコンパレータ         |       2x High Speed、2x Low Power        |
|                通信                 |                  I^2^C                   |

:::

- [*1]{#brief-note1}: VDD2はVDD1を超えないこと
- [*2]{#brief-note2}: LUT/DFF/CNT/DLYは同時使用できない

SLG46826についてもっと知りたい方はDialog社のページを参照ください（<https://www.dialog-semiconductor.com/products/slg46826>）。

\newpage

**おわかりいただけるだろうか**

::: {.table width=[1.0] noheader=true}

|  ![](images/slg46826-pinout-qfn.png){width=120mm}  |
|:--------------------------------------------------:|
| ![](images/slg46826-pinout-tssop.png){width=120mm} |

:::

##### 謎の半導体メーカーD社

# 回路図
# 基板図
##### Top view
##### Bottom view
