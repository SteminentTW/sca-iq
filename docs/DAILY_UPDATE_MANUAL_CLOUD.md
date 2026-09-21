# SCA-IQ／MSA-IQ 每日更新手冊（雲端 routine 版）

> 這是給**雲端 Claude Code routine** 用的版本，每次執行都是全新 clone，不會有本機檔案或狀態延續。
> 完整正本（含每週任務、變更歷史）在 Google Drive：
> `G:\My Drive\Work\SCA Type 1\01_仲恩生醫_夥伴關係與策略提案\50_SCA-IQ營運\SCA-IQ_每日更新手冊.md`
> 兩邊如有出入以 Drive 正本為準；這份雲端版只保留「每日掃描＋發布」需要的部分。
> **每週一的 email 週報不在這個 routine 的工作範圍內**——那部分留在本機另一個排程處理。

## 平台架構

兩個獨立 GitHub repo，你（routine）會拿到兩者的 clone：
- **SCA-IQ**：`SteminentTW/sca-iq`，公開網址 https://steminenttw.github.io/sca-iq/
- **MSA-IQ**：`SteminentTW/msa-iq`，公開網址 https://steminenttw.github.io/msa-iq/

每個 repo 內：`index.html`＝呈現層（**不要改**），`data/*.json`＝資料層（**你每天只改這裡**）。網頁自動讀取 `data/` 動態渲染（排序、去重、搜尋）。**你的工作＝往對應 JSON 加/改一筆＋更新 meta.json，然後 commit+push。完全不用碰 HTML。**

先確認兩個 repo clone 各自在哪個目錄（`ls` 或 `pwd` 確認），以下所有路徑都是相對「該 repo 根目錄」。

## 🔴 鐵則：每一筆資料都要「中英雙寫」

全站中英雙語，頁首「中文／EN」鈕切換全站 8 個分頁。每筆 JSON 除中文欄位外，另放一個 `en` 物件裝英文版：

```json
{ "id":"…", "tag":"分類", "body":"中文內容", "en": { "tag":"Category", "body":"English content" } }
```

- `en` 只需放「有中文的欄位」；日期、NCT 編號、網址、英文原名不用重複。
- 缺英文會 fallback 顯示中文（不會壞版），但**每次新增一定要補 `en`**。
- `links[]`／`sources[]` 若標籤有中文，`en` 裡也要放整個陣列的英文版（url 相同、只換 `label`）。

## SCA-IQ 資料檔（`data/`）

| 檔案 | 內容 | 新增方式 | 唯一鍵 |
|---|---|---|---|
| `competitors.json` | 競品全景表 | 新增/更新競品；`id` 對應 `companies.json`；每筆必須有 `ring`（`sca`/`polyq`/`ataxia`） | `id` |
| `benchmarks.json` | 細胞治療標竿（`group`：`cell`/`neuro`，`region`：`jp`/`kr`/`us`/`eu`） | 有新核准/BLA/PDUFA 等法規里程碑時更新。**只寫客觀事實，不寫「對本社的意義」** | `id` |
| `partnership.json` | 合作機會（`layer`：`rival`/`therapeutic`/`platform`/`channel`/`academic`） | 需 `market`、`priority`(1-3)、`status`、`fit`/`whyNotRival`/`forms[]`/`priorityWhy`+`en{}`；須在 `companies.json` 補對應一筆 | `id` |
| `papers.json` | 文獻雷達 | 加一筆；能辨識所屬公司時填 `company`（見 companies.json id） | `id`（`pubmed:數字` 或 `pmc:PMCxxxx`） |
| `trials.json` | 臨床試驗雷達 | 加一筆或更新既有筆 status | `nct` |
| `results.json` | 競品試驗結果與讀出（療效/安全數據、CRL/核准等） | 有公布結果時加一筆 | `id` |
| `news.json` | 本週情報摘要 | 加在陣列最前面，維持約 6 筆 | `id` |
| `changelog.json` | 更新紀錄 | 有新增才加最前面一筆 | `date` |
| `events.json` | 學會/會議雷達 | 每週約一次即可 | `id` |
| `patents.json` | 專利雷達 | 每月一次即可 | `id` |
| `exclusivity.json` | 市場獨占權雷達 | 每月一次即可 | `id` |
| `assessments.json` / `naturalhistory.json` | SCA 知識庫（量表/自然史） | 慢速參考，非每日 | `id` |
| `companies.json` | 全部公司登記表，每筆對應 `company.html?id=<id>` | 任何新公司都要加一筆：`id`/`name`/`country`/`ticker`/`mechanism`/`pipeline`/`insight`/`threat`/`url`/`match`別名陣列/`en{}` | `id` |
| `meta.json` | 頁首掃描日期＋KPI | **每天一定要更新** | — |

## 每日任務

### 1. 掃描來源

**追蹤資產起點**（清單會隨主動發現持續成長）：仲恩 Stemchymal®（本社）、Biohaven Troriluzole、Vico VO659、Arrowhead/Sarepta ARO-ATXN2（siRNA SCA2）、Biogen/Ionis BIIB132/ION260（ATXN3 ASO，2023放棄；注意 BIIB105 是 ATXN2/ALS 案非SCA）、UniQure等AAV基因療法、學名藥Riluzole。

**ClinicalTrials.gov：查五組適應症，不能只查 SCA**（`query.cond=` 各查一次）：
1. `spinocerebellar+ataxia`
2. `Friedreich+ataxia`
3. `Bulbo-Spinal+Atrophy+X-Linked`（SBMA；**不要用** `spinal+and+bulbar+muscular+atrophy`，會被前列腺癌試驗灌爆——CT.gov同義詞擴展到雄性素受體AR相關試驗；仍會混入4-5筆前列腺癌，逐筆確認適應症）
4. `polyglutamine`
5. `episodic+ataxia+OR+CACNA1A`

`https://clinicaltrials.gov/api/v2/studies?query.cond=…&sort=LastUpdatePostDate:desc&pageSize=40`

**PubMed**：E-utilities（esearch+esummary），`spinocerebellar ataxia AND (stem cell OR Stemchymal OR Troriluzole OR VO659 OR ATXN1 OR ATXN2 OR ATXN3 OR therapy)`，近7天。
**公司/產業新聞**：WebSearch「Steminent Stemchymal」「Biohaven troriluzole SCA」「Vico Therapeutics VO659」「Arrowhead ARO-ATXN2 SCA2」「Biogen BIIB132 SCA3」等，近7天。

### 1b. 主動發現新競品（每次必做，不能只盯既有清單）

1. **廣掃**：`https://clinicaltrials.gov/api/v2/studies?query.cond=spinocerebellar+ataxia&filter.overallStatus=RECRUITING%7CACTIVE_NOT_RECRUITING%7CNOT_YET_RECRUITING&sort=LastUpdatePostDate:desc&pageSize=50`；另查亞型/基因標的：`spinocerebellar ataxia type 1/2/3/6/7`、`Machado-Joseph`、`ATXN1/2/3/7`、`polyglutamine ataxia`。
2. **比對現有清單**：主辦公司+介入藥物跟 `trials.json` 比對，不在檔內的療法型試驗＝候選新競品。只收療法（藥物/生物製劑/細胞/基因/RNA），排除復健/rTMS/針灸中藥/保健品/純自然史觀察影像/純診斷工具。
3. **建檔**：新競品查證後 (a) `trials.json` 加一筆 (b) 重要主要競品同時在 `competitors.json` 加一筆（含 `en{}`）(c) `changelog.json` 加一筆「競品更新」(d) 更新 `meta.json` (e) `companies.json` 補公司 meta（含 `match` 別名陣列，只放公司名/藥名/代號，不要放疾病名稱）。
4. 也掃新聞/PubMed 找「尚未登錄CT.gov但已由新聞稿/學會摘要宣布」的新進者。

### 1b-2. 已知資產點名查核（每週一次）

疾病條件查詢會系統性漏掉：久未更新的試驗（排序排不進前40名）、登記在非適應症條件下的試驗（如健康受試者PK試驗）。對下列資產跑藥名查詢，缺的就補（健康受試者PK/食物效應試驗要收，是開發進度訊號）：

```
https://clinicaltrials.gov/api/v2/studies?query.term=<藥名>&pageSize=10&fields=NCTId,BriefTitle,OverallStatus,Phase,LeadSponsorName,InterventionName,Condition,LastUpdatePostDate
```

清單：`Stemchymal`／`19#iSCLife`／`VO659`／`ARO-ATXN2`／`Troriluzole`(`BHV-4157`)／`Levacetylleucine`(`IB1001`/`AQNEURSA`)／`SLX-100`／`Rovatirelin`(`KPS-0373`)／`BIIB132`(`ION260`)／`CRD-002`／`NLX-112`(`befiradol`)／`SLS-005`／`AJ201`(`rosolutamide`)／`AMT-130`／`DT-216P2`／`Nomlabofusp`(`CTI-1601`)／`LX2006`／`Leriglitazone`／`Vatiquinone`(`PTC743`)／`SGT-212`／`NIDO-361`

`OverallStatus` 是主辦方自行維護、常年不更新——看到「收案中」但預估完成日已過或最後更新逾一年，`status` 欄要寫明「登錄自YYYY-MM-DD起未再更新」，不要當現況陳述。

### 1c. 仲恩 BD 指定追蹤名單（分類收錄準則）

| 情況 | 放哪 | 標記 |
|---|---|---|
| 直接治療 SCA | `competitors.json` | `ring:"sca"` |
| 鄰近 polyQ（SBMA、HD） | `competitors.json` | `ring:"polyq"` |
| 鄰近共濟失調（Friedreich's等） | `competitors.json` | `ring:"ataxia"` |
| 非SCA細胞治療法規/商業化先例 | `benchmarks.json` | `group:"cell"` |
| 罕病神經開發/終點/CNS遞送標竿 | `benchmarks.json` | `group:"neuro"` |

追蹤範圍（分類參考，實際以 `competitors.json`/`benchmarks.json` 現有內容為準）：
- **SCA/共濟失調**：Biohaven、Design Therapeutics、PTC Therapeutics、Larimar、Solaxa、Seelos、Arrowhead、Sarepta、Wave Life Sciences、Cure Rare Disease、Lexeo、Vico、Neurolixis、Minoryx、安基生技AnnJi
- **細胞治療標竿**：REPROCELL、Cuorips、住友製藥/RACTHERA、Heartseed、Healios、Cellusion、Orizuru、SCM Lifescience、ENCell、MediPost、CorestemChemon、Kangstem、Anterogen、Pharmicell、S.Biomedics、CHA Biotech、Tego Science、Mesoblast、Cynata、BrainStorm、Capricor、BlueRock
- **罕病神經標竿**：Biogen、Ionis、Ultragenyx、uniQure

公司頁鐵則：不管新增競品或標竿，都要同時在 `companies.json` 補一筆，否則公司名點不開。`match` 別名只放公司名/藥名/代號，不要放疾病名稱（放SCA3會把整個亞型論文都掛到這家公司）。

`benchmarks.json` 鐵則：只記客觀事實，不寫「對本社的意義」「潛在夥伴」——公開站上這樣寫等於揭露仲恩BD意圖。查不到就寫「公開資訊有限」，不臆測。

### 2. 判斷「是否真正新」

同時滿足：①日期戳記近7天內 ②平台尚未記錄（唯一鍵比對）③有可查證原始來源連結。**不編造、不誇大**，與這些資產無關的內容一律不加。

### 3. 寫入 JSON（Read讀出現有內容 → Edit/Write加入新筆）

**papers.json**：
```json
{ "id":"pubmed:12345678","date":"2026-07-15","dateDisplay":"2026/07/15","title":"論文英文標題","journal":"期刊 · 作者等","tag":"分類標籤","note":"→一句話關聯（可空）","company":"（可選）companies.json id","highlight":false,"url":"https://pubmed.ncbi.nlm.nih.gov/12345678/","urlLabel":"論文 ↗ PubMed 12345678","en":{"tag":"…","journal":"…","note":"…","urlLabel":"…"} }
```
`highlight:true`＝對本社有利/最相關（綠色）。加入前確認 `id` 不在檔案裡。

**trials.json**：新試驗加一筆；既有試驗狀態變了改那筆 `status`：
```json
{ "nct":"NCT12345678","drug":"藥名","mechanism":"機制","phase":"Ph2","status":"最新狀態","sponsor":"主導公司","us":false,"url":"https://clinicaltrials.gov/study/NCT12345678","en":{"mechanism":"…","phase":"…","status":"…","sponsor":"…"} }
```
`us:true` 只給本社仲恩的試驗。

**results.json**（療效/安全數據/CRL/核准/期中讀出——跟trials.json的「試驗狀態」不同）：
```json
{ "id":"短id","date":"2026-07-20","dateDisplay":"2026/07","competitor":"公司","drug":"藥物","trial":"NCT+期別","sourceType":"登錄|論文|研討會摘要|新聞稿","level":"win|hot|info","readout":"實際結果與關鍵數字（只寫查得到的）","implication":"→對本社的意涵一句話","links":[{"label":"來源","url":"…"}],"en":{...} }
```
`level`：`win`=競品受挫對本社有利、`hot`=競品正面結果=威脅、`info`=中性。**療效數字/p值只寫查得到的來源數字，不杜撰；公司宣稱要標明「公司宣稱」；尚未正式讀出就寫明。**

**news.json**（加最前面，維持約6筆）：
```json
{ "id":"短id-yyyy-mm","date":"2026-07-15","dateDisplay":"2026/07","level":"win|hot|info","tag":"分類","body":"內容（可用<b>）","note":"→補充（可空）","links":[{"label":"來源 ↗","url":"…"}],"en":{...} }
```

**changelog.json**（只有當天真的有新增內容才加，當天無新情報不加）：
```json
{ "date":"2026-07-15","dateDisplay":"2026/07/15","tag":"每日掃描","level":"info","body":"新增X則科學文獻（…）／新增競品試驗（…）","link":{"label":"詳見 文獻雷達 ↗","tab":"si","anchor":"litradar"},"en":{...} }
```
`link`依內容類型：新論文→`{tab:"si",anchor:"litradar"}`；新試驗→`{tab:"si",anchor:"trialradar"}`；競品新聞→`{tab:"ov"}`。

### 3b~3d. 低頻維護（events/patents/exclusivity/assessments/naturalhistory）

- `events.json`：每週約一次，更新下屆會議日程、加競品發表（須有公開來源）、加新會議。
- `patents.json`／`exclusivity.json`：每月約一次。專利逐頁核對權利人，不臆測；到期日標「估計」。獨占權嚴格區分designation(資格) vs exclusivity(獨占)。
- `assessments.json`／`naturalhistory.json`：慢速參考知識，掃到新驗證/新自然史論文才補。準確度優先於數量，每筆附權威來源。

### 4. 每天更新 `data/meta.json`

```json
{ "lastScan":"今天日期 YYYY-MM-DD","nextScan":"明天日期 09:45","weekNewCount":本週累計新增數 }
```
即使當天無新情報，也要把 `lastScan` 改成今天。

### 5. 發布：commit + push（每天都要做，即使只改了meta.json）

在該 repo 根目錄下（雲端環境不需要 `-C` 絕對路徑，直接在 repo 目錄操作即可）：

```bash
git rev-parse --abbrev-ref HEAD
git add -A
git commit -m "每日更新 <今天字面日期>" || echo "nothing to commit"
git pull --rebase
git push
git status -sb
```

第一行要是 `main`（不是就先 `git checkout main`）；最後一行要看到 ahead 0 才算推成功。

🔴 **發布鐵則**：
1. **不要用 `&&` 串 commit 和 push**——`git commit` 無變更時回傳 exit 1，`&&`短路會讓push整個不執行，且不會有任何錯誤訊息。逐行執行或用 `|| true`。
2. **commit訊息用字面日期**，不要用指令替換產生的動態日期。
3. **push前先 `pull --rebase`**——這兩個repo可能被其他session/routine同時操作過，遠端較新時push會被拒。
4. `git commit`回報"nothing to commit"屬正常。
5. push失敗（網路等）就在`changelog.json`加一筆說明，下次重試。

🔴 **推完一定要驗「公開網址真的變了」**（`git status -sb` ahead 0 不等於網站已更新，只證明commit送到GitHub，不證明Pages已發佈）：

```bash
curl -s "https://steminenttw.github.io/sca-iq/data/meta.json?cb=1"
```
（MSA-IQ驗 `https://steminenttw.github.io/msa-iq/data/meta.json?cb=1`；`?cb=` 隨便換數字繞快取）

看到 `lastScan` 是今天才算真的發布完成。沒馬上變是正常的，等1-2分鐘再打一次；超過10分鐘還是舊日期，就在 `changelog.json` 記一筆「當日更新延後上線」。**不要用 `gh api .../pages/builds` 判斷成敗——那個API不可靠，唯一可信的是直接curl公開網址看檔案內容。**

## 6. 姊妹站 MSA-IQ（多系統萎縮症）——跑完SCA-IQ後接著做

架構與SCA-IQ完全相同：`index.html`呈現層＋`data/*.json`資料層，全站中英雙語，同樣鐵則：每筆新增都要中英雙寫、附可查證來源。

**定位（重要）**：MSA-IQ是**中立的疾病領域情報站**——仲恩只是「追蹤中的細胞治療業者之一」，**不要**寫「對本社的策略意涵」這類內部語氣。

**資料檔**（`data/`，都是 `msa-` 前綴）：

| 檔案 | 內容 | 頻率 |
|---|---|---|
| `msa-competitors.json` | 在研療法全景（`category`：`cell`/`asyn`/`immune`/`dmt`/`sympt`） | 有新療法或狀態變更時 |
| `msa-trials.json` | 臨床試驗雷達（`cell:true`標綠底） | 每日掃CT.gov |
| `msa-results.json` | 試驗結果（`level`：`pos`正面/`neg`未達終點或終止/`info`中性） | 有讀出時 |
| `msa-papers.json` | 文獻雷達 | 每日掃PubMed |
| `msa-news.json` | 總覽領域焦點（維持約5筆） | 有重大進展時 |
| `msa-assessments.json`／`msa-naturalhistory.json`／`msa-preclinical.json` | 知識庫，慢速參考 | 掃到新驗證才補 |
| `msa-events.json` | 會議雷達 | 每週一次 |
| `msa-changelog.json`／`meta.json`／`versions.json` | 更新紀錄/掃描心跳/版本 | 每日更新meta |

**每日掃描來源**：
- ClinicalTrials.gov：`https://clinicaltrials.gov/api/v2/studies?query.cond=multiple+system+atrophy&filter.overallStatus=RECRUITING%7CACTIVE_NOT_RECRUITING%7CNOT_YET_RECRUITING&sort=LastUpdatePostDate:desc&pageSize=40`
  ⚠️ 會夾帶SMA/DMD等「atrophy」字面命中的無關試驗（如salanersen、BMN 351），務必逐筆確認適應症真的是MSA才收。
- PubMed：`"multiple system atrophy"[Title]` 近7天。
- 公司/產業新聞：Alterity ATH434、Lundbeck amlenetug、Ono ONO-2808、Teva emrusolmin、Tiziana foralumab、Mayo MSC、Ionis ION464。
- 發布：同SCA-IQ §5 六行指令，在msa-iq repo目錄下跑；push前pull --rebase、最後status -sb看到ahead 0，再curl msa-iq的公開網址驗證。

## 注意事項

- 所有新增內容必須基於可查證的公開來源，附連結，不編造。
- 平台為公開資料版本、零NDA，不加入任何機密/內部資訊。
- 改的是 `data/*.json`，**不要動 `index.html`**。
- 改完JSON務必確認格式合法（逗號/引號），格式錯會讓該區塊整個載入失敗。
- 若掃描時工具/網路異常，在changelog.json加一筆簡短說明，不要留白讓人以為系統停擺。
- 完成後不需要额外報告，結果都在commit/push裡；這是無人值守執行，任何需要判斷的地方按本手冊規則自行決定，把選擇寫進changelog.json。
