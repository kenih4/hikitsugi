# 加速器運転メモ

## 加速器立ち上げ注意点
- SyDump <-> SRを切り替えるBH2の初期化を忘れずに
- 入射前にはReady GUIを確認し、MBSがLockになっているか確認
- FS測定: FB OFFしてからMeasure。測定終了後FB ONを忘れずに
- FS FBのプロットは `/home/sr/oper/fs_plot.plt` で
- 垂直Dispersion補正GUIで計算したあと、リミットが出ているSkewはマスク
- SPBPMのGUIをリブートしたら、FSのGUIでinitしないといけない
- 入射GUI
  - Multiは18番
  - 100mA積み上げ時、99.5mAと入力
- FSやnim phaseのGUIはopcon01のRFのタブにある

## 安全インターロック関連
- ルートが繋がっている状態でMBSを開けたら安全インターロック（蓄積電流は関係ない）
  - このインターロックをDisableにするのがTopup SWのようなもの
  - MBSが開いている状態でルートを繋げたら安全発報（SyDump <-> SRを切り替えるGUIは一応MBSの状態を見ている）

## RF・エネルギー
- RF±100Hzするとエネルギーが0.123%変わる
- RFは508MHz
- 周回周波数は208kHz
- RF: HVを落として一定時間経過したらLVも落ちる仕様
- RF最大5MVまで。Aモードは昔14.4MVだったが16MVに上げた。ライフを伸ばすため
- RF GUIでreset NCOはRFが立ち上がっていないことを確認してから！！！
- NCOは特に高速なデジタル処理（FPGA）と連動し、ビームの挙動に合わせて周波数を動的に追従させる

## 報告・連絡先
- 運転に係る報告の際は今後 `Accelerator_Operation@spring8.or.jp`
- SRシステムキーはマグネット関係なし

## CODBPM
- CODBPMの詳細:  
  http://saclaopr19.spring8.or.jp/~sp8/filemaker/pukiwiki-1.5.4_utf8/?CODBPM%E6%B8%AC%E5%AE%9A%E7%B3%BB

## 損失電子数
- 損失電子数は一週間で上限がある
  - 損失電子数GUIの赤線はその上限を8時間分にした値を閾値として表示している
  - SSBT2のDCCT、つまり入射効率が良ければいい
  - SSBT1とSSBT2の間でいくらロスっても大丈夫
  - `safety_topup_intlk_integrated_inj_loss_tup_shift/charge`

## 地震・周長補正
- 地震発生時は念のため周長補正OFF
- 地震などで周長補正が停止した際、手動でCODのGUIからdRF ~Hzを投げるが、この時TopUPは停止した方がよい
  - dRFを投げる時、セットで他のタイミングも投げるため

## C15のBPM画像
- Ayagiku -> 運転マニュアル -> ビデオモニターDisplay

## 光学・ID配置
- 5m直線くらいの後、BM1 BM2
- BM2の出口を区切りとする。その後の直線にIDを設置

## 真空計インターロック
- sc1, cr, ab, pb1,2がインターロックに入ってるゲージ
  - このうち2つセットポイント叩くと、RF停止、GV CLOSE
- 真空計1セルで2か所、引っ掛けると落ちる
  - インターロックに入ってる真空計はカウントの信号がある  
    `sr_vac_ivg_setpoint_*_*/count`
  - IDDの真空度はインターロックに入っていないsc2に信号を入れた

## Tune調整
- Tune調整用のQ: QP2, 3, 8, 9

## FS Feed back
- ステーション間の位相があっているとどうしても2kHz帯にシンクロトロン振動が発生してしまうので、これを抑えるのが目的

## SSBT
- SSBT2-1,2のCTはノイズに強い差動CT。バンプのノイズ対策

## バケット数
- 電子が入れられるバケット数は2436 (2x2x3x7x29)

## PDAB43IR
- PDAB43IRのLock/UnlockはSP8GUIから生えてくるGUIから実施
  - 実際のOPEN/CloseはReady GUIがやってる。Vacuumの所に表示有

## B組立搬入室
- FEのテストベンチ 甲斐

---

# MAG

- MAGのRun curr set
  - `str_set(f)` は角度[urad]
- 四極電磁石シャント抵抗を取り付け の意図
  - Qメインの補助電源としてQAがあるが、Qメインを減らすようにマイナスの流してる所もある。このような所は、わざわざQA電源で電流を調整するのでなく、Qにシャント抵抗を付けてメイン電流をこちらに流すことによりメイン電流を減らせばいい
- Skewと言えば普通はSkew_Quad。これはID GAPが閉まった時にカップリング補正GUIが変える。よって、GFOの時は決まった値がある
- `xsbt_h_4_1` はリングから電源を取ってる
  - `xsbt_h_4_1` のAUXはリングのマグネット初期化GUIで初期化終了後に電流が入る

## COD補正
- COD補正FBに使うSTは少なくHD(High definition)なので精度いい
- 手動COD補正に使うSTは多いが粗い
- Cut offを小さくするとSTの値が大きくなって無理やり補正できるがビームがどっか行く可能性あり
- また、MICADOだと使用するSTが少ない（個数は指定できる）のでSTの値が大きくなる
- リファレンスにより近づける時はCut offを小さくする
- STの蹴り角が10urad以上だと注意。蹴るときにアボートする可能性があり
  - その場合、Cut offを大きくして何回かに分けて実施する
- 10秒に一回にスタディ予定（どっかのBLで超伝導MAGの向きを変えるため）。現状は1秒周期でID25 kicker駆動時だけ10秒になる

## COD dRF
- CODセーブしてSt set
- CODセーブしてdRF set  
  と2ステップしないといけない

---

# ID

- 「FULL OPEN」と「GFO」は同じ意味で、開ける前の値を保持して「Point Obs」で戻せる
  - 「GFO Study」はStudyの時など、問答無用でさっさと開けるので早い
- ID GAPが閉まっているときのみ、BPMが2つヒットしたらシフトが立ってビームを落とす
- 定点観測用 `[ALL Gap : fixedpoint_4.txt]`
- ID08はPhaseがあって、FULL OPENにならないとPhaseは動かせない
  - なので、定点観測の時の[ALL GAP]を押すと一度ID08はFULL OPENになってPhaseが動いてから定点観測GAPになる
- Minimum GAPの確認
  - St補正テーブルのリストを見ればいい  
    `/data/bl/idtuning/setting/IDFFTablePoints.dat`

---

# 運転・バンプ関連

- 2024下半期はバンプの補正キッカーOFFで運転していた。ないほうがよかったため

## バンチ純化
- バンチ純化: スクレーパーが閉まっていないといけない
  - ビームを振動させてスクレーパーに当てて余計な成分を落とす。純化装置はC30にある
- 純化範囲の設定ファイル  
  `/data/oper/GUI_config/purity/ko_address_list`  
  現在のHmodeの純化範囲は22.5±19.5なので、3~42まで
- バンチ純度測定GUI: 閉じるとMDAQを停止する。GUI立ち上げ時は手動でMDAQ立ち上げる必要あり
- A-modeの時は純化なし
  - なので、「入射電子ビームの不純度の見積もり」を貼る
  - 電子ログノート用PC `/me/bunchPurity/InjectionPurity`
  - +26の所にノイズあり → Aモードだと+2に相当

## 加速管Q値
- 加速管のQ値（Quality factor）とは、加速空洞内で共振している電磁場のエネルギーが失われる割合を示す量。Q値が高いほど損失が少なく、空洞の品質が高い

## 積み上げ入射
- H-modeはマルチ部から94mA、次シングル部99mAにセット（シングル部は減りが早いから）
- D-modeはシングル部から。シングル部が5つあるのでガタガタにならないように
- multiは10Hz入射しかできない

## SRランチャー
- SRのランチャー 例（xfopcon-01）から適当な端末にログインして、  
  `sr_lanchergui xfopcon-01`

## St補正テーブル作成時の注意点（2023/9/29 1直のログ）
- Gap Full Openの状態でtaperを付けたため、gap2が開きすぎてアラーム
- 作成後、GFO Studyするが、この時、COD補正＆周長補正が入っていないと軌道変動が発生してアボートする可能性あり

## TopUp入射が弾切れにならない条件
- TopUp入射損失電子数が上限値を超えないという条件がある
- TopUp入射損失電子数の上限値は、1シフト（8時間）の積算値に換算すると 6.6e11 e-
- 一方で、TopUp入射損失電子数のシフト（8時間）の積算値は、ビーム寿命と入射効率が一定の場合、  
  ビーム寿命 t (時間)、入射効率 f（100%を1とする）として  
  `3e12/t（時間） x 8（時間）x（1-f）/f`　（式1）  
  という式で見積もることができる
- 加速電圧を下げるとビーム寿命tが短くなるが、式1で計算された値がTopUp入射損失電子数の上限値（8時間あたり6.6e11 e-）に比べて余裕があれば、その分が加速電圧を下げる余地ということになる
- 加速電圧を下げた時のビーム寿命の減少については、タウシェク寿命について早乙女さんが計算をまとめてくださった資料がある
- ビーム寿命は厳密にはタウシェク寿命とガス散乱寿命のトータルで決まるが、ガス散乱寿命は100時間程度と長いので、ビーム寿命の加速電圧依存性はタウシェク寿命の加速電圧依存性とさほど変わらない

---

# バンチ純度測定

## 失敗時
- TDC InitしてターミナルでInitコマンドが投げ終わってることを確認
- Auto
- address, satellite確認  
  調べ方はPARAMSET_DBのブラウズからFILL  
  MDAQ Paramset Version List
- Start

## 特定のアドレスだけ測定
- 2025/11/17 F-mode +8が純度悪化した。+8だけ測りたい場合、
  - in orderとか出てくる画面を開いて 1を入力してin orderは8以外マスク
  - メイン画面のaddressに0、satelliteにも0を入力してstart
- 各アドレスのトレンドグラフはログノートPCの `C:\me\InjectionPurity` のフォルダにある
- `/data/srmon/bl38b2/purity_tdc`

---

# FS測定
- StartしてRunningになったらStop
- Measure
- PLOT

---

# 廃棄手順
1. GAP FULL OPEN（Full OPEN or GFO Study）
2. MBS LOCK
   - Ready confirmのBeamlineで確認
3. TopUP SW OFF
4. COD, Auto Energy OFF
5. RF 10.8MV
6. 廃棄

---

# 再入射手順
1. GAP OPEN確認
2. 60mA以上であった場合、10.8MVに落として廃棄
3. マグネット初期化: [SEQUENCE]ボタン押して、[Q,Sx,St,Skew]ボタン押す
4. それが終わったら[QLSSD]ボタン押す
5. 再入射

---

# 光軸調整
1. [Gap Lock] ※COD補正を止めるから（ID43の時はしていないが。。）
2. COD補正、周長補正OFF
3. CODセーブ [名前: Before] ※COD補正OFFする前に取得。OFFしてGAP等動いて変わる可能性もあるため
4. MAG ST セーブ
5. 光軸調整GUIで角度を入力して、シミュレーション
6. BL43 光軸調整GUIでSET
7. CODセーブ [名前: After]
8. MICADOでH 1個 V 1個にして、
   - Refに[Before]
   - Targetに[After]
   - をしてEstimationして、St set（これはローカルバンプを作った時に発生してしまう漏れを治す）
9. CODセーブ [名前: h1v1]
10. [名前: h1v1]をRefにしてから、GUI[Release cod svd]で File -> Save reference time -> curr set 押すと黄色が消える
    - COD計算GUI
    - COD補正FB GUI
    - 周長補正GUI
11. FB再開
12. stパラメータをセーブ

- ※GUI[Release cod svd]で File -> Save reference time をすると、Parameter DBのRING_PARAMの「reference_orbit」に時刻が書き込まれる
- ※定点観測の時の光軸調整は光軸調整したいBLの一つ前を入力欄Cell#に入力する

---

---
# SGの制御復活方法について
  SRマスターオシレータ交換後の対処になります。
  停電明けなどにも必要になりますので、よろしくお願いします。
  SGの制御復活方法について：
  １．ホストにログイン
  　　ssh llrf@srtmg-phae-01　（パスは温泉）
  ２．/tmp/relay_com_pid*.log（最新のもの）を見てopen_countがいくつか確認する。
  　　例：
  　　ll /tmp/relay*
  　　grep count /tmp/relay_com_pid1225_160212.log
  　　…………… open count=2 ←このカウントを確認する。
  ３．tellms set/sr_tmg_e_signalgenerator/close
  　　を上記回数送って、open_count=0にする。
  ４．tellms set/sr_tmg_e_signalgenerator/init
  ５．tellms set/sr_tmg_e_signalgenerator/open
  これでmdaqでのデータ収集の復活を確認する。

---

# BL
- BL側のアラームレベルごとの対処法は
　青いファイル（BL当番マニュアル）に書いてある

---

# その他
- アンダーパスは9セル付近
- サマリウム