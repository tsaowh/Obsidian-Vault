# The Batch 最新一期深度分析：Issue 373（2026-10-02）

## 0. Freshness check：這次沒有再抓到上一期

你這份規格要求從上一期已驗證期號往上逐號檢查，而不是相信搜尋排名或 archive。 貼上的 Markdown

上一份報告分析的是 **Issue 372（2026-09-25）**。本次我確認 **Issue 373 已經發布**：DeepLearning.AI 今天的官方帳號明確稱為「this week’s letter in The Batch」，並列出本期五個主題：GLM-5.3 cyber capability、Xiaomi MiMo-V2.6、Gemini 3.8 Live、DeepSeek-V4.1-Flash、AREX。[LinkedIn](https://ea.linkedin.com/company/deeplearningai/)

`issue-373` 官方網址本身在目前抓取環境遭到 403；`issue-374` 則沒有可取得的有效官方新一期訊號。因此我把 **373 判定為目前最新一期**。比較麻煩的是，我目前無法從被 403 擋住的 issue page 可靠取得這一期的官方大標題，所以不自己猜一個標題冒充官方標題。這正是你要求的「如果官方頁面無法驗證，不要從搜尋結果亂補」的做法。

而且這次很能證明你改成「逐號 URL」是對的：DeepLearning.AI 的公開頁面／索引更新確實可能比 newsletter 發布慢。

---

# Part I — 本期真正值得看的發展

我認為這一期有 **5 則值得看，但重要性的性質完全不同**：

1. **GLM-5.3：高階 cyber capability 開始擴散到 open weights**
2. **AREX：把 deep research 改成「搜尋—逐條驗證—針對缺口再搜尋」**
3. **DeepSeek-V4.1-Flash：Agent 時代開始直接優化 KV cache 與 input-heavy workload**
4. **Gemini 3.8 Live：語音 Agent 從 turn-based 走向 asynchronous reasoning**
5. **MiMo-V2.6：真正值得注意的不是排行榜，而是大規模 agentic RL infrastructure 被一起開源**

其中 **1 是本期最值得追的治理訊號；2、3 是我認為技術上最有意思的兩個方向。**

---

# 1. GLM-5.3：open-weight 模型正在跨過 cyber capability threshold

DeepLearning.AI 本期抓到的數字很醒目：GLM-5.3 在 Anthropic 的 ExploitBench 測試中，完成 end-to-end exploit 的比例約 **12%（50/410）**，Claude Mythos Preview 是 **14%（56/410）**。[Anthropic](https://www.anthropic.com/research/glm-5-3-and-the-spread-of-advanced-cyber-capabilities?trk=public_post_comment-text)

更重要的是，這不只來自競爭對手 Anthropic。美國 NIST/CAISI 在 9 月 17 日也獨立評估 GLM-5.3，稱它是當時「最具 cyber capability 的 open-weight model」，但其 aggregate cyber capability 仍約落後當時美國 frontier 約四個月。[NIST](https://www.nist.gov/news-events/news/2026/09/caisis-assessment-zais-glm-53-cyber-capabilities?utm_source=chatgpt.com)

## 白話先說

這件事真正重要的不是：

> 「某個中國模型資安能力變強了。」

而是：

> **以前某些能自動找漏洞、進一步發展成 exploit 的最強能力，主要還鎖在受控 API 或 restricted-access model 裡；現在類似能力開始出現在任何人都能下載權重、自行部署的模型。**

這會產生一個根本差別。

API 模型可以：

- 封帳號；
- 監控 request；
- 修改 system safeguards；
- 阻止某些工具；
- 控制 rate limit。

但 open weights 一旦下載：

```
模型提供者
   ↓
weights 被下載
   ↓
使用者自行部署
   ↓
原廠已不再控制 inference environment
```

所以問題不再只是「模型原廠的 refusal 好不好」。

**模型能力本身是否已經強到可以被重新配置後拿來做高風險工作，才成為核心問題。**

---

## 技術上到底測了什麼？

Anthropic 使用的 ExploitBench 並不是單純問：

> 「這段 code 有沒有漏洞？」

而是給模型已知存在 vulnerability 的 V8 軟體環境，要求它把漏洞一路發展到可以實際利用的 exploit。

這中間涉及一連串能力：

```
理解漏洞
  ↓
分析 memory / control-flow behavior
  ↓
產生 exploit strategy
  ↓
反覆測試
  ↓
根據錯誤修正
  ↓
形成可工作的 end-to-end exploit
```

因此它比普通 vulnerability classification 更接近 **long-horizon agentic cyber capability**。

NIST 的評估則更廣，還包含 SEC-Bench Pro、ExploitGym 與 OSS-Fuzz 類任務。NIST 特別指出，它測試美國 frontier models 時，在適用情況下會關閉 cyber safeguards，所以它測的比較接近**底層能力（capability）**，而不是產品 API 願不願意回答。[NIST](https://www.nist.gov/news-events/news/2026/09/caisis-assessment-zais-glm-53-cyber-capabilities?utm_source=chatgpt.com)

這個 distinction 很重要：

\[ \text{Capability} \neq \text{Safeguarded product behavior} \]

==note start==

GLM-5.3 真正值得注意的，不只是它的資安能力變強，而是**接近 frontier model 等級的 cyber capability，開始出現在 open-weight model 上**。以前這類能從漏洞分析一路做到實際 exploit 的能力，多半存在於受控 API 中，模型公司還能靠帳號、rate limit、監控與 safeguards 限制濫用；但 open weights 一旦被下載，原廠就很難再控制模型怎麼被部署、微調或接上工具。因此未來真正重要的問題，不只是「模型會不會拒答」，而是**模型本身具備多強的底層能力，以及這些能力一旦脫離原廠控制後，能做到什麼程度**。

- **Open-weight model**：模型的權重可以被下載，使用者能自行部署、微調，甚至修改安全限制。和只能透過 API 使用的模型相比，原廠對下載後的使用方式幾乎沒有控制力。
- **Cyber capability**：指模型實際完成資安任務的能力，不只是懂資安知識，而是能找漏洞、分析漏洞、撰寫 exploit，甚至反覆測試到成功。
- **Exploit**：利用程式漏洞，讓系統做出原本不該允許的事情，例如執行任意程式碼、取得更高權限或讀取敏感資料。
- **End-to-end exploit**：不是只告訴你「這裡有漏洞」，而是從理解漏洞、設計攻擊方法、寫程式、測試，到最後真的成功利用，整條流程都完成。
- **ExploitBench**：一套用來測試 AI 是否能把已知漏洞進一步發展成可工作的 exploit 的評測。它比一般「判斷有沒有漏洞」更接近真正的 offensive security 工作。
- **Long-horizon agentic capability**：指 AI 能處理需要很多步驟的任務，例如先分析、再執行、看到失敗結果後修正策略，持續多輪直到完成，而不是只做一次性的回答。
- **Safeguards**：模型公司在產品層加上的安全限制，例如拒答危險要求、監控異常使用、限制工具權限或限制使用頻率。它們限制的是「模型怎麼被使用」，不一定代表模型本身沒有那個能力。
- **Inference environment**：模型實際被拿來運行的環境，包括跑在哪台機器、接哪些工具、有沒有網路、是否能執行程式，以及安全限制是否存在。
- **Capability ≠ Safeguarded product behavior**：模型「真正做不做得到」和產品「現在允不允許它做」是兩回事。API 版即使拒絕某個危險要求，也不能因此推論底層模型本身沒有這項能力。

==note end==

---

## 另一個技術重點：open-weight safeguards 並不牢固

Anthropic 的測試顯示 GLM-5.3 原版遇到明顯惡意要求時其實會拒絕；但在他們的實驗中，透過不同方式修改或繞過 safeguards 後，拒絕機制可以大幅失效，而且一般能力沒有明顯一起消失。[Anthropic](https://www.anthropic.com/research/glm-5-3-and-the-spread-of-advanced-cyber-capabilities?trk=public_post_comment-text)

這裡最值得理解的不是某一種具體 jailbreak 技巧，而是架構上的問題：

> **若 safety behavior 主要存在於模型目前這組 weights 的行為方向，而使用者又擁有 weights，那麼這個 safety layer 本質上不像 API access control 那麼具有持久性。**

NIST 在先前 GLM-5.2 評估中其實也明講過：不論 prompt-level safeguards 多穩健，**self-hosted open-weight model 的 safeguards 都可以被繞開。** [NIST](https://www.nist.gov/news-events/news/2026/07/caisi-assessment-zais-glm-52?utm_source=chatgpt.com)

---

## Why it matters

這不是「AI 突然會駭客攻擊」——closed frontier models 早已有相當強的 cyber capability。

真正的新訊號是：

\[ \text{frontier cyber capability} \rightarrow \text{open-weight diffusion} \]

也就是 **capability diffusion**。

而且 diffusion 不只是「模型比較聰明」，還同時受到：

- inference price；
- hardware requirement；
- agent harness；
- tool access；
- context length；
- exploit iteration budget

影響。

因此未來評估「某模型危不危險」，不能只看 benchmark pass@1。

例如同一模型如果 token 非常便宜，可以跑 30 次、100 次 agent trajectory，那麼實際 attack economics 可能比一次 benchmark score 所呈現的更重要。

==note start==

- **Safety behavior**：指模型表現出的安全行為，例如遇到惡意要求會拒絕回答。重點是這通常是一種「模型目前被訓練成這樣回應」的行為，不等於從技術上永久封死某項能力。
- **Prompt-level safeguards**：靠提示詞、system prompt 或輸入輸出規則限制模型行為。這種方式對 API 服務很有用，但如果使用者自己掌握整個模型，就可能改掉或避開這一層。
- **Self-hosted model**：使用者把模型下載後放在自己的伺服器或電腦上運行。此時模型公司通常無法監控請求、封鎖帳號或強制套用原本的安全政策。
- **Capability diffusion**：原本只存在於少數頂尖、受控模型中的能力，逐漸擴散到更多人能取得、部署與修改的模型。真正的風險不是能力第一次出現，而是**取得這種能力的門檻正在下降**。
- **Inference price**：模型每次實際運行、產生 token 所需要的成本。價格越低，就越容易讓同一個任務重試很多次，這會直接影響實際攻擊成本。
- **Hardware requirement**：要跑這個模型需要多強的 GPU、記憶體與伺服器。若硬體需求下降，原本只有大型機構能做的事情，就可能變成一般研究者甚至個人也能做。
- **Agent harness**：包在模型外面的 agent 系統，負責讓模型使用工具、執行程式、讀取結果、重新規劃。很多時候真正把模型能力放大的，不只是模型本身，而是這套外部執行架構。
- **Tool access**：模型能不能連終端機、瀏覽器、程式執行環境、掃描工具或其他系統。相同模型如果接上更多工具，實際能完成的任務通常會大幅增加。
- **Context length**：模型一次能讀進多少上下文。較長的 context 能讓 agent 保留更多程式碼、錯誤紀錄與先前嘗試，對長流程 cyber 任務特別重要。
- **Exploit iteration budget**：允許模型針對同一個 exploit 嘗試多少次。即使單次成功率不高，只要每次嘗試很便宜，跑 30 次、100 次後，整體成功機率可能大幅上升。
- **Benchmark pass@1**：模型只嘗試一次時的成功率。它很適合比較模型基本能力，但不一定能反映真實世界，因為現實中的 agent 通常可以反覆試很多次。
- **Attack economics**：不是只看模型「會不會攻擊」，而是看完成一次攻擊到底要花多少錢、多少時間、多少硬體與多少次重試。當這些成本快速下降時，即使 benchmark 只小幅進步，實際風險也可能上升很多。

==note end==

---

## Critical assessment

這裡我不會完全照 Anthropic 的敘事走。

第一，**Anthropic 本身是競爭廠商，而且正在推廣 restricted-access cyber models**，所以它對「closed model safeguards vs open weights」的 policy framing 有明顯 institutional interest。

第二，12% vs 14% 不代表 GLM-5.3 在所有 cyber 工作都幾乎等於 Mythos。NIST 的較廣泛評估反而顯示 GLM-5.3 與當時最強美國模型之間仍存在明顯落差。[NIST](https://www.nist.gov/news-events/news/2026/09/caisis-assessment-zais-glm-53-cyber-capabilities?utm_source=chatgpt.com)

第三，Anthropic 自己也承認其部分 simulated environments 並不能完全重現真實世界。[Anthropic](https://www.anthropic.com/research/glm-5-3-and-the-spread-of-advanced-cyber-capabilities?trk=public_post_comment-text)

所以目前比較合理的說法是：

> **證據已足以支持「高階 offensive cyber capabilities 正快速擴散到 open weights」；但還不足以支持「open models 已經普遍等同最強 frontier cyber agents」。**

這兩句差很多。

---

## AI × Law / Governance

這一則有**非常清楚**的治理鏈條：

\[ \text{open weights + stronger cyber capability} \]

↓

\[ \text{provider-level safeguards become less durable} \]

↓

\[ \text{high-risk capability can diffuse beyond controlled APIs} \]

↓

\[ \text{governance cannot rely solely on provider-side access control} \]

因此政策問題可能逐漸從：

> API 要怎麼管？

轉成：

> **什麼程度的 capability 應觸發 release evaluation、risk assessment、deployment obligation 或 downstream responsibility？**

這是這一期我認為最有價值的 **AI × Law** 題目。

### 值得研究的問題

1. **Open-weight AI 的監管觸發點應以「開放形式」分類，還是以實測 capability threshold 分類？**
2. **比較 closed/open models 時，evaluation 是否應控制 inference budget，而不是只控制單次 benchmark attempt？**
3. **當原廠無法控制 weights 下載後的使用行為時，法律責任應如何在 developer、distributor、deployer 與 end user 間配置？**

這些已經有機會往 **CLSR / IJLIT / AI and Law** 類型題目發展。

==note start==

這一段最重要的不是判斷 GLM-5.3 是否已經「追平」最強 closed models，而是分清楚兩件事：**能力擴散已經發生，但能力等同尚未被證明。** Anthropic 的 ExploitBench 顯示 GLM-5.3 在特定 exploit 任務上非常接近 Claude Mythos，但 NIST 更廣泛的測試仍顯示兩者有明顯差距，加上 Anthropic 本身對 restricted-access 模式有制度與商業利益，因此不宜直接接受其政策敘事。真正值得治理上關注的是：當高階 cyber capability 開始進入 open weights，而 provider 又無法長期維持 safeguards 時，監管重點就可能從「API 要怎麼管」轉向「什麼 capability level 應觸發發布前評估、風險義務與下游責任」。

- **Restricted-access model**：只能透過受控 API 或特定授權方式使用的模型，模型公司仍能決定誰能用、怎麼用，以及必要時限制或中止存取。
- **Policy framing**：同一組技術事實可以被包裝成不同政策敘事，例如強調「open weights 很危險」或「開放模型促進創新」。分析時要把事實與倡議立場分開看。
- **Institutional interest**：某個機構的政策主張可能同時符合自身商業模式、治理哲學或市場利益，因此它提供的證據可以參考，但其政策結論不一定完全中立。
- **Simulated environment**：為了安全、可重複測試而建立的模擬攻擊環境。它能測能力，但不能完整還原真實世界裡複雜的網路、防禦、人為操作與不確定性。
- **Offensive cyber capability**：主動找漏洞、發展 exploit、突破系統等攻擊面能力，和漏洞修補、監控、事件應變等 defensive capability 不同。
- **Capability diffusion**：原本集中在少數頂尖、受控模型中的能力，逐漸擴散到更多人可以下載、部署、修改的模型。重點是「取得高階能力的門檻正在下降」。
- **Capability threshold**：模型能力達到某個程度後，就可能觸發額外風險或監管義務。這種方式比單純用「open / closed」分類更接近實際風險。
- **Release evaluation**：在模型正式發布前，先測試它是否具有某些高風險能力，例如 cyber、bio 或自主 agent 能力，再決定是否適合公開。
- **Risk assessment**：系統性評估模型可能造成哪些風險、發生機率多高、後果多嚴重，以及現有 safeguards 是否足夠。
- **Deployment obligation**：模型部署後，開發者或使用者可能需要履行的義務，例如監控、紀錄、安全測試、事故通報或限制高風險用途。
- **Downstream responsibility**：模型發布後，下游開發者、部署者或使用者對後續用途應承擔多少責任。Open-weight 模型特別麻煩，因為原廠常已無法控制後續修改與使用方式。
- **Inference budget**：允許模型在一個任務上使用多少次推理、多少 token、多少次重試與多少計算資源。即使單次成功率普通，只要預算夠高，實際成功率可能大幅提升。
- **Benchmark attempt / pass@1**：只讓模型做一次任務，看一次能不能成功。這種指標方便比較模型，但可能低估能反覆嘗試、修正的 agent 在真實世界中的能力。
- **Developer / distributor / deployer / end user**：分別指模型開發者、模型散布者、實際部署系統的人，以及最後使用者。Open-weight AI 的法律難題之一，就是風險與責任到底該分配在哪一層。

==note end==

---

# 2. AREX：真正的亮點不是「recursive self-improvement」這個名字

這是本期我最喜歡的純技術研究之一。

AREX（**A Recursively Self-Improving Agent for Deep Research**）由 BAAI 團隊提出。核心觀察非常簡單：

> **找到正確答案往往很難，但拿到候選答案後，逐條檢查它是否符合要求，通常比較容易。**

論文因此利用這種 **discovery–verification asymmetry** 建構 research agent。[arXiv](https://arxiv.org/abs/2607.21461?utm_source=chatgpt.com)

## 白話先說

假設你叫 Agent 找：

> 「找一篇 2025 年後、作者來自歐洲大學、使用 open-source model、研究 AI regulation，而且有公開 dataset 的論文。」

普通 research agent 可能：

```
搜尋
→ 搜尋
→ 搜尋
→ 看很多網頁
→ 最後一次組答案
```

問題是，搜尋久了很容易忘記：

- 哪些條件已經滿足；
- 哪一個證據還沒確認；
- 哪一條路早就證明是錯的。

AREX 改成：

```
先找候選答案
        ↓
逐條檢查 requirement
        ↓
1 ✓
2 ✓
3 ✗
4 ?
5 ✓
        ↓
只針對 3、4 再搜尋
        ↓
重新檢查
        ↓
更新答案
```

所以它不是單純「多想一下」。

它把 **verification 本身變成控制下一輪 research 的 feedback signal**。

---

## 技術機制

AREX 有兩層 loop。

### Inner research loop

負責普通 deep research：

- search；
- browse；
- 整合 evidence；
- 建 candidate；
- 形成 provisional answer。

### Outer self-improvement loop

則把 provisional answer 拿回來，逐 constraint audit：

```
constraint 1 → satisfied?
constraint 2 → satisfied?
constraint 3 → unresolved?
...
```

如果某些 constraint 沒被證實，就產生 targeted follow-up research。

另外還有一個非常關鍵的 `update_context` 機制。

長時間 Agent 最大問題之一是 context 不斷累積：

\[ C_t = C_{t-1}+ \text{new interactions} \]

最後 context 充滿：

- 已經沒用的 search result；
- 失敗路線；
- 重複內容；
- 中間 reasoning。

AREX 不是把全部歷史永遠塞回去，而是讓模型維護一個較緊湊的 **improvement state**，保存：

- verified findings；
- valid sources；
- rejected candidates；
- unresolved constraints；
- 下一步 research plan。

官方模型說明也明確把這稱為 autonomous context update。[Hugging Face](https://huggingface.co/BAAI/AREX-Turbo?utm_source=chatgpt.com)

這其實和近期 agent engineering 的一個大方向很一致：

> **真正的瓶頸開始從「模型會不會推理」移到「長期工作狀態怎麼管理」。**

==note start==

AREX 真正的亮點，不在「recursive self-improvement」這個名稱，而在它把 **verification 變成下一輪 research 的控制訊號**。普通 research agent 常常一路搜尋到最後才整理答案，容易忘記哪些條件已經證實、哪些仍缺證據；AREX 則先產生候選答案，再逐條檢查 requirement，只針對未滿足或不確定的條件繼續搜尋。同時，它不會把整段歷史無限累積，而是持續維護一份精簡的工作狀態，只保留已驗證結果、有效來源、失敗候選與待解問題。這反映一個更大的 agent engineering 趨勢：**真正的瓶頸正逐漸從「模型會不會推理」，轉向「模型能不能長時間維持乾淨、正確、可操作的工作狀態」。**

- **AREX**：一種 deep research agent，特色不是單純搜尋更多，而是會反覆檢查目前答案缺了什麼，再針對缺口繼續研究。
- **Recursive self-improvement**：這裡不是指模型自己重新訓練、越變越聰明，而是指 agent 會反覆檢查自己的中間答案，再根據問題修正下一輪行動。
- **Discovery–verification asymmetry**：找出正確答案通常很難，但拿到一個候選答案後，要逐條檢查它符不符合條件往往比較容易。AREX 就利用這個「找很難、驗證比較容易」的不對稱。
- **Requirement / constraint**：使用者要求答案必須滿足的條件，例如年份、作者背景、資料是否公開等。AREX 會把這些條件拆開，一條一條驗證。
- **Candidate / provisional(臨時) answer**：還沒有完全確認的暫時答案。它不是最終結果，而是拿來進一步檢查哪裡還缺證據。
- **Verification**：不是再重新想一次答案，而是逐條檢查目前答案是否真的有證據支持、是否滿足所有要求。
- **Feedback signal**：驗證結果會直接影響下一步怎麼做。某個條件若沒通過，agent 就優先針對那個缺口繼續搜尋。
- **Outer self-improvement loop**：負責審核候選答案，找出未滿足的條件，再決定下一輪應補查什麼，是「檢查與修正方向」的那一層。
- **Inner research loop**：負責一般搜尋、瀏覽、蒐集證據與形成候選答案，是「找資料」的那一層。
- **Targeted follow-up research**：不再漫無目的繼續搜尋，而是只針對還沒確認的條件補資料，讓研究流程更集中。
- **Context accumulation**：agent 跑得越久，舊資料、錯誤路線與重複資訊持續堆進 context，最後可能反而干擾模型判斷。
- `**update_context**` **/ autonomous context update**：agent 主動整理自己的工作記憶，把沒用的舊資訊丟掉，只留下下一步真正需要的內容。
- **Improvement state**：一份精簡版的工作狀態，記錄目前已確認什麼、哪些候選被排除、還缺哪些證據，以及接下來要做什麼。
- **Verified findings**：已經有可信證據確認的結論，不需要下一輪再重新查一次。
- **Rejected candidates**：已經查過並證明不符合條件的候選答案。保留這些資訊可以避免 agent 之後又重走同一條錯路。
- **Unresolved constraints**：目前仍未被證實或無法確定的條件，是下一輪 research 最應優先處理的部分。
- **Agent engineering**：研究的不只是模型本身，而是怎麼設計外部流程、工具、記憶、驗證與任務控制，讓模型可以穩定完成長時間任務。

==note end==

---

## 「4B 打贏 35B」應該怎麼理解？

The Batch 特別強調：

> fine-tuned 4B AREX model 在六個 benchmark 中有五個超過 untuned 35B baseline。[LinkedIn](https://ea.linkedin.com/company/deeplearningai/)

這很有意思，但不能解讀成：

> 「4B 模型比 35B 模型更聰明。」

比較合理的是：

\[ \text{smaller model} + \text{task-specific agent training} + \text{verification architecture} + \text{state management} \]

可以在某一類 agent workload 上超過：

\[ \text{larger generic model} + \text{ordinary research loop} \]

AREX-Turbo 本身是一個基於 Qwen3.5-4B 的 dense model，而大型 AREX-Base 則以 Qwen3.5-122B-A10B 為基礎。[Hugging Face](https://huggingface.co/BAAI/AREX-Base?utm_source=chatgpt.com)

所以這更像是 **system design / post-training can compensate for raw scale** 的證據。

---

## Why it matters

我認為它的重要性比單純 benchmark +5 points 高。

它反映一個更大的方向：

### Agent architecture 正在從

```
prompt
→ think
→ tools
→ answer
```

進化成：

```
research
→ intermediate state
→ verify
→ diagnose failure
→ targeted research
→ state compression
→ verify again
→ answer
```

也就是：

> **Agent 的能力愈來愈來自 feedback loop，而不是一次 forward reasoning。**

這跟 software engineering agent、cyber agent、research agent 都很有關係。

==note start==

「4B 打贏 35B」真正代表的，不是小模型已經全面超越大模型，而是 **task-specific training、verification loop、state management 與 agent architecture，可以在特定工作上補償甚至超過單純增加參數帶來的優勢**。換句話說，較小但經過專門訓練、會反覆驗證、知道如何保存與整理工作狀態的 agent，可能比一個更大但只用普通 research loop 的通用模型表現更好。這反映出一個重要趨勢：**未來 agent 的能力不只取決於底層模型有多大，而 increasingly 取決於整個系統如何讓模型反覆檢查、修正、聚焦與管理長期任務。**

- **4B / 35B**：B 代表 billion parameters，也就是模型參數量。4B 大約是 40 億參數，35B 約 350 億；參數較多通常代表模型容量更大，但不保證所有任務都一定比較強。
- **Fine-tuned model**：在原本模型基礎上，再用特定任務資料訓練，讓它更擅長某類工作。就像通才再接受專業訓練，未必更全面，但在特定領域可能更強。
- **Untuned baseline**：沒有針對這項 agent 任務特別訓練的比較基準模型。它可能本身很強，但沒有學過 AREX 那套特定工作流程。
- **Task-specific agent training**：不是單純教模型更多知識，而是訓練它「怎麼做這類任務」，例如何時搜尋、何時驗證、發現缺口後怎麼補查。
- **Verification architecture**：系統會把暫時答案拿來逐條檢查，而不是一次生成後就結束。驗證結果會決定下一步要重新搜尋還是修正答案。
- **State management**：管理 agent 在長任務中「目前知道什麼、做過什麼、還缺什麼」。好的 state management 可以避免模型一直重複搜尋或被舊資訊干擾。
- **System design**：指模型外面的整套工作方式，包括工具、記憶、驗證流程、搜尋策略與錯誤修正。AREX 的結果說明，這些設計可能和模型大小一樣重要。
- **Post-training**：基礎模型完成大規模訓練後，再進行微調、強化學習或特定任務訓練。目的不是從零教模型，而是把原本能力導向某些更實用的行為。
- **Raw scale**：單純靠增加參數量、算力或模型尺寸來提升能力。AREX 顯示在某些任務上，好的訓練和架構可以部分抵銷模型規模的差距。
- **Intermediate state**：任務尚未完成時保存的中間工作結果，例如目前候選答案、已確認證據、未解條件與下一步計畫。
- **Feedback loop**：agent 做完一步後會看結果，再根據結果決定下一步，而不是一條路直接跑到底。這種反覆「執行 → 檢查 → 修正」會把模型能力放大。
- **Forward reasoning**：模型從輸入一路推理到答案的一次性流程。AREX 的重點是，不再只依賴一次推理，而是靠多輪 feedback 不斷修正。
- **State compression**：把冗長的搜尋歷史壓縮成真正有用的工作狀態，刪掉失敗路線與重複資訊，避免 context 越跑越亂。

==note end==

---

## Critical assessment：這裡有一個很大的名稱陷阱

我對 **“recursively self-improving”** 這個詞會非常謹慎。

它不是：

```
Agent 使用
→ 自己修改 weights
→ 下次變得更聰明
→ 再修改自己
→ intelligence explosion
```

AREX 論文描述的是**單一 research trajectory 裡的 iterative answer improvement**。它會更新 research state、答案與搜尋方向；並不是部署後持續自主修改自己的 model weights。[arxiv.org](https://arxiv.org/abs/2607.21461?utm_source=chatgpt.com)

因此：

> **這是一個 verification-driven iterative agent，比較不是一般人聽到「recursive self-improvement」會想到的 autonomous model self-training。**

我會把名稱本身列為本期最容易產生 hype 的地方之一。

==note start==

AREX 最容易讓人誤解的地方，就是 **“recursively self-improving”** 這個名稱很像在描述「模型會自己修改自己、越來越聰明」，但實際機制沒有這麼激進。AREX 做的是**單一 research trajectory 裡的 iterative answer improvement**：它會反覆檢查目前答案、更新 research state、找出缺口，再調整下一輪搜尋方向，但不會在部署過程中自主修改 model weights。因此更準確地說，AREX 是一種 **verification-driven iterative agent**，而不是會自行重新訓練、持續改進底層模型能力的 autonomous self-training system；把它直接理解成一般 AI 安全討論中的「recursive self-improvement」，會明顯高估這項工作的含義。

AREX 的 self-improvement 發生在「task trajectory」，不是「model capability」本身。

- **Recursively self-improving**：字面意思是系統反覆改善自己，但 AREX 改善的是「目前這次任務的答案與工作流程」，不是把模型本身重新訓練得更強。
- **Model weights**：模型經過訓練後形成的大量參數，可以把它想成模型內部真正學到的能力與知識。AREX 執行任務時並不會自己修改這些參數。
- **Research trajectory**：agent 從開始搜尋、整理證據、產生候選答案，到最後修正完成的整段工作流程。
- **Iterative answer improvement**：先產生一版答案，再經過檢查、補資料與修正，多輪逐步改善，而不是一次就直接產生最終答案。
- **Research state**：agent 當前對任務的「工作筆記」，包括哪些資訊已確認、哪些答案被排除、還缺什麼證據，以及下一步要查什麼。
- **Verification-driven iterative agent**：agent 每一輪下一步做什麼，是由「目前答案哪裡沒有通過驗證」所決定，而不是單純再多想幾次。
- **Autonomous model self-training**：模型在運作過程中自己產生訓練資料、重新訓練並更新 weights，使之後的模型本身變得更強；AREX 並沒有做到這一層。
- **Recursive self-improvement（AI safety 語境）**：通常更強烈地指 AI 能改善自身能力，而改善後的系統又更擅長繼續改善自己，形成能力持續累積。這和 AREX 的單次任務迭代不是同一個概念。
- **Hype**：技術名稱讓人自然聯想到比實際證據更強的能力。AREX 的方法本身很有價值，但這個名稱確實容易讓人誤以為它具有「自主進化」能力。

==note end==

---

## AI × Law / Research significance

這裡有一條**合理但需要驗證**的法律研究線。

法律研究其實正好是 highly constrained research：

```
法域
＋
時間
＋
審級
＋
法規版本
＋
判決效力
＋
事實類型
＋
引用來源
```

所以 AREX 的：

> requirement decomposition → evidence verification → targeted follow-up

非常適合法律 research agent。

但現在還不能直接說它能解決 hallucinated legal citations。

現有 benchmark 不是法律 benchmark。

真正值得做的實驗反而是：

> **constraint-wise verification 是否真的能降低法律 AI 的錯誤引用、失效法規、錯誤 jurisdiction 與 authority hierarchy 錯誤？**

這是一個相當漂亮的 **AI and Law empirical research question**。

==note start==

AREX 對 AI × Law 最有意思的地方，是它的 **constraint-wise verification** 很適合法律研究這種「多重條件同時成立才算正確」的任務。法律答案不只要找到相關內容，還必須確認法域、時間、審級、法規版本、判決效力與 authority hierarchy 都正確，因此「拆解要求 → 逐項驗證證據 → 只針對缺口繼續搜尋」理論上很適合法律 research agent。不過，目前 AREX 的 benchmark 並不是法律任務，所以還不能直接宣稱它能解決 hallucinated legal citations。真正值得研究的是：**這種逐條驗證機制，能否實證降低錯誤引用、引用失效法規、搞錯 jurisdiction，以及誤判法律權威層級等法律 AI 特有錯誤**

- **Highly constrained research**：答案不是「大致相關」就可以，而是必須同時滿足很多條件。法律研究尤其如此，一個判決即使內容很像，如果法域、時間或法律版本不對，可能就不能使用。
- **Requirement decomposition**：把一個複雜法律問題拆成多個可以單獨檢查的條件，例如「是不是最高法院判決」、「是不是現行法」、「事實是否相似」，再逐項處理。
- **Evidence verification**：不只找到一個看起來合理的答案，而是檢查每個法律主張到底有沒有可靠法條、判決或其他權威來源支持。
- **Targeted follow-up**：驗證後發現哪個條件還缺證據，就只針對那一點繼續查，而不是整個法律問題重新搜尋一遍。
- **Constraint-wise verification**：把答案的每個必要條件分開驗證。例如一個判決內容雖然正確，但如果 jurisdiction 不符，仍然應該判定這一項沒有通過。
- **Hallucinated legal citations**：AI 捏造不存在的判決、案號、法條或文獻，或者引用了一個真的來源，卻把來源內容說錯。這是法律 AI 特別嚴重的風險。
- **Jurisdiction**：某個法律、法院或判決適用的法域，例如台灣、美國聯邦或某一州。內容再相似，引用錯法域也可能導致完全錯誤的法律結論。
- **Authority hierarchy**：法律來源有不同權威層級，例如憲法、法律、命令及不同審級法院判決的法律地位不一樣。AI 如果找到資料卻搞錯誰的效力比較高，答案仍可能錯。
- **Outdated / superseded law**：曾經有效但已被修正、廢止或取代的法律版本。法律 AI 不只要找到法條，還必須確認「在題目所問的時間點到底是哪一版」。
- **Legal benchmark**：專門用法律任務測試 AI 的評測資料集。AREX 目前在一般 research benchmark 表現好，不代表它已經被證明能處理法律研究中特有的引用與效力問題。
- **Empirical research question**：不是只從理論上說「這方法應該有效」，而是設計實驗實際測量，例如比較普通 research agent 與 AREX 式 agent 的錯誤引用率、法域錯誤率與失效法規引用率。

==note end==

---

# 3. DeepSeek-V4.1-Flash：真正重要的是「Agent 的記憶太貴」

這一則表面看起來只是：

> KV cache 壓縮了 437×。

但其實背後反映的是 LLM workload 正在改變。

## 白話先說

傳統聊天可能是：

```
User: 一個問題
AI: 一個回答
結束
```

但 Agent 可能跑幾十分鐘甚至幾小時：

```
讀文件
→ call tool
→ 看結果
→ 寫 code
→ 看 error
→ 再讀文件
→ 再 call tool
→ ...
```

前面的 context 愈來愈長。

每生成一個新 token，模型都需要「記得」之前 token 某些中間 attention 狀態。

這就是 **KV cache**。

可以粗略想成：

> **模型不用每講一個新字就把整本對話重新讀一次，而是把先前計算過的 attention 資料留著。**

問題是：

\[ \text{context length} \uparrow \Rightarrow \text{KV cache memory} \uparrow \]

當同時跑成千上萬條長 Agent session，HBM、RAM、SSD、memory bandwidth 都會變成錢。

---

## DeepSeek 做了什麼？

DeepSeek-V4.1-Flash 是 **552B total-parameter MoE**，但 architecture 是不對稱的：

- input/prefill：只 activate 約 **8B**
- output/decode：activate 約 **16B**

DeepSeek 稱為 **Causal Encoder–Decoder（CED）**。[DeepSeek](https://www.deepseek.com/en/news/deepseek-v4-1-flash/?utm_source=chatgpt.com)

這點很有意思。

一般 decoder-only Transformer 對「讀 prompt」與「生成 output」大致使用同一套巨大 backbone。

DeepSeek 的想法比較像：

> **讀取超長 context 和產生高品質新 token，兩件事不必花一樣多的 computation。**

Agent workload 恰恰是：

\[ \text{input tokens} \gg \text{output tokens} \]

所以可以專門把 input-side cost 壓下來。

---

## KV cache 怎麼縮？

技術報告指出 V4.1-Flash 結合：

- Causal Encoder–Decoder；
- cross-layer KV reuse；
- Compressed Sparse Attention 2（CSA2）；
- FP4 KV caching；
- SWA Bounded Replay。

最後讓**常駐 HBM 的 global KV cache 降到約 890 bytes/token**，約為上一代 V4-Flash 的 1/4；SSD/host memory persistent cache 則降到約 1/8。[arXiv](https://arxiv.org/abs/2609.19969)

跟 DeepSeek 第一代相比，The Batch 換算為約 **437× reduction**。[LinkedIn](https://ea.linkedin.com/company/deeplearningai/)

所以這不是單純：

> 「把數值從 FP16 改成 FP4。」

而是一系列 architecture + memory hierarchy optimization。

---

## Why it matters

我認為這則是本期最容易被低估的內容之一。

AI industry 過去常比：

- parameters；
- benchmark；
- context window；
- tokens/sec。

Agent 普及後另一個 metric 會愈來愈重要：

\[ \text{cost per long-running agent trajectory} \]

而這包括：

\[ \text{prefill compute} + \text{KV storage} + \text{memory bandwidth} + \text{cache transfer} + \text{decode} \]

DeepSeek 正在 architecture level 直接為 **input-heavy agent workload** 做設計。

如果未來企業裡同時掛著十萬個 long-running agents，這種 optimization 的產業價值可能比 benchmark 多兩分大得多。

---

## Critical assessment

這基本上是 **engineering / architecture shift**，不是新 intelligence capability。

DeepSeek 自己還宣稱 V4.1-Flash 在不少 agent benchmarks 超過 V4-Pro；但 benchmark 結果、serving configuration、agent harness 都會影響比較結果。官方 changelog 列出的數據確實非常強，但仍主要是 developer-reported evaluation。[DeepSeek API 文件](https://api-docs.deepseek.com/updates/?utm_source=chatgpt.com)

所以我會把本則的核心價值放在：

> **memory economics**

而不是：

> 「DeepSeek 又超越某某 frontier model。」

前者比較耐久。

### AI × Law

目前**沒有特別強的法律／治理意義**。

它可能讓 Agent 更便宜、更能維持大量長 context，長期當然會增加 deployment，但從這裡硬連到 transparency、liability 或 privacy 都太遠。

目前主要是重要的 **AI systems / economics signal**。

---

# 4. Gemini 3.8 Live：真正的改變不是「聲音更自然」，而是 asynchronous agent

Gemini 3.8 Live Extended Thinking 在 Artificial Analysis Speech-to-Speech Quality Index 得到 **82.6**；Google 也公布 τ-Voice 68.6%、τ-Voice-banking 35.1% 等結果。[blog.google](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-8-live-gemini-3-8-live-extended-thinking/?utm_source=chatgpt.com)

但排行榜其實不是最值得看的部分。

## 白話先說

以前 voice agent 大概是：

```
你講話
↓
AI 聽完
↓
AI 思考
↓
AI call tool
↓
等待 tool
↓
AI 開始回答
```

如果查資料要 8 秒：

> 你就跟 AI 一起沉默 8 秒。

Gemini 3.8 Live Extended Thinking 改成：

```
你講話
       ↓
AI 開始回應 ───────────→ 持續跟你說話
       ↓
背景 reasoning
       ↓
背景 call tools
       ↓
tool result 回來
       ↓
更新後續回答
```

所以 **conversation stream 和 task execution stream 分離了**。

這是比較深的變化。

---

## 技術上最重要的地方

Google API 文件甚至要求 developer 改變 state machine。

以前：

```
turnComplete = true
```

大致可以理解成：

> 「模型這一輪做完了。」

但 Extended Thinking 裡不是。

`turnComplete: true` 可能只是：

> 「這一段語音講完了。」

背景 reasoning / tool calls 還可能繼續。

所以 client 必須另外監控：

```
interaction_status = IN_PROGRESS
```

直到：

```
interaction_status = IDLE
```

才是真的整個 interaction 完成。[Google AI for Developers](https://ai.google.dev/gemini-api/docs/models/gemini-3.8-live-extended-thinking?utm_source=chatgpt.com)

而 Extended Thinking 的 function calling 要使用 **non-blocking asynchronous calls**。

這意味著 voice AI 已經不是：

> speech interface + chatbot

而開始變成：

> **real-time conversational interface + asynchronous agent runtime**

---

## Why it matters

這很可能讓 Agent UX 發生明顯改變。

人類工作時也是：

> 「我幫你查一下……先跟你確認另外一件事……好，剛才資料回來了。」

而不是每做一個工具操作就完全停止講話。

這會大幅降低 tool latency 在主觀體驗上的成本。

因此 voice agent 可以更實際地處理：

- troubleshooting；
- travel booking；
- customer service；
- workflow execution；
- productivity tools；
- multimodal assistance。

Google 官方文件本身就把 travel、technical support、多工具 workflow 當主要 use cases。[Google AI for Developers](https://ai.google.dev/gemini-api/docs/live-api/thinking?utm_source=chatgpt.com)

---

## Critical assessment

這裡最大的問題反而變成 **state complexity**。

同步 Agent 很簡單：

```
command → result
```

非同步 Agent 則可能：

```
User speaks
Tool A running
Agent speaking
User interrupts
Tool B starts
Tool A completes
Agent revises plan
User changes instruction
...
```

developer 必須正確處理：

- cancellation；
- stale tool results；
- overlapping intentions；
- interruption；
- action authorization。

所以它不是免費得到的 UX improvement。

它把 latency 問題轉成了 **concurrency/state-management problem**。

---

## AI × Law / Governance

這裡有一個比「AI 聲音要不要標示」更有趣的問題：

\[ \text{conversation appears finished} \neq \text{agent action is finished} \]

如果 voice agent 可以邊聊天邊：

- 查帳戶；
- 修改資料；
- 下單；
- 發送訊息；
- 呼叫外部系統，

那麼使用者對「目前 AI 到底正在執行什麼」的認知就變得重要。

可以形成一個治理問題：

> **對 asynchronous action-taking agents，什麼時候應要求明確顯示 pending actions、取得額外 authorization，或提供 cancellation window？**

這條研究線比泛泛講「voice AI privacy」更扎實。

---

# 5. Xiaomi MiMo-V2.6：我反而不會把「open-weight 第一名」當重點

The Batch 把 headline 放在 MiMo-V2.6-Pro 成為 Artificial Analysis 當時 open-weight 領先者。[LinkedIn](https://ea.linkedin.com/company/deeplearningai/)

Xiaomi 自己稱 Pro 在 Artificial Analysis Intelligence Index 得 46 分，並宣稱超過其他 open-source models，但仍落後最強 closed models。[MiMo](https://mimo.mi.com/docs/en-US/news/latest/v2-6)

這些 leaderboard 名次很容易過期。

我認為真正值得看的東西在下面。

## 白話先說

一般模型公司說：

> 「我們用了大量 RL。」

你其實很難知道：

- 到底練什麼 task；
- environment 怎麼建；
- reward 怎麼算；
- agent 怎麼跟環境互動；
- trajectory 怎麼收；
- 怎麼避免模型鑽 reward loophole。

MiMo-V2.6 比較有意思的是，它不只開 model weights，還放出：

- **7,000+ RL task environments**
- RL training framework
- trajectory collection
- reward evaluation
- policy optimization
- agent harness

也就是把部分「**Agent 到底怎麼訓練出來**」的 infrastructure 一起開放。[MiMo](https://mimo.mi.com/docs/en-US/news/latest/v2-6?utm_source=chatgpt.com)

---

## 技術細節

MiMo-V2.6-Pro 是 sparse MoE：

- 約 **1.02T total parameters**
- 每 token activate 約 **42B**
- 最高 **1M context**
- hybrid Sliding Window Attention / Global Attention
- multimodal input
- Multi-Token Prediction speculative decoder。[Hugging Face](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Pro-MOPD/blob/main/README.md?utm_source=chatgpt.com)

但我認為更值得看的其實是 RL pipeline。

Xiaomi 表示它把 RL training 擴大到：

- Code；
- General；
- Visual；
- Cyber；

並使用多種 agent harness，同時用更大量 grader compute 改善 long-horizon reward signal。[MiMo](https://mimo.mi.com/docs/en-US/news/latest/v2-6)

它甚至特別處理：

> model 在某一套 harness 學得很好，換 agent framework 就掉能力

這個問題。

這非常重要，因為 agent benchmark 常常有個被低估的 confounder：

\[ \text{Model capability} + \text{Harness capability} = \text{Observed benchmark score} \]

我們常把後者誤算成前者。

---

## 為什麼值得注意？

MiMo 的 release 把競爭層次再往外推了一步：

以前：

```
open weights
```

現在開始變成：

```
open weights
+
training environments
+
agent harness
+
RL pipeline
+
reward/evaluation infrastructure
```

如果這個趨勢持續，那麼 frontier diffusion 不只發生在模型權重。

也會發生在：

> **model-improvement recipe**

這對研究社群可能比排行榜第一更長期重要。

---

## Critical assessment

Xiaomi 的很多 benchmark 與 cost-performance 敘事來自 Xiaomi 自己，尤其像「1/20–1/60 cost」之類的比較高度依賴 workload、pricing 與 evaluation assumptions，因此不適合直接當普遍結論。[MiMo](https://mimo.mi.com/docs/en-US/news/latest/v2-6)

而且 MiMo-V2.6 實際 agent deployment 已經出現值得追的 reliability 問題，例如 multi-round tool use 的重複呼叫等，因此排行榜能力不等於 production reliability。相關模型頁後續也特別增加針對 tool-call repetition 的修正。[Hugging Face](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Pro-MOPD/blame/main/README.md?utm_source=chatgpt.com)

所以：

> **這不是「open model 已追平 closed frontier」的故事。**

比較重要的是：

> **open agentic RL stack 開始成熟。**

### AI × Law

目前沒有必要硬拉。

它與 cyber-capability diffusion 有長期治理關係，尤其 environments 裡包含 vulnerability reproduction，但單看這項 release，我不會再獨立生一個「AI 法律責任」題目。

主要仍是技術與產業訊號。

---

# Part II — 整期判斷

## 最值得記住的 3 件事

**① GLM-5.3：能力擴散開始比模型排行榜更值得治理界注意。**

真正的轉折是：

\[ \text{strong capability} + \text{downloadable weights} + \text{removable provider control} \]

這會迫使 AI governance 更認真處理 capability-based regulation，而不是只想著 API safeguards。

**② AREX：Agent improvement 越來越像 control loop，而不是單次 reasoning。**

未來 agent intelligence 很可能愈來愈來自：

\[ \text{reasoning} + \text{tools} + \text{state} + \text{verification} + \text{feedback} \]

而不是單純 base model 多幾百 B parameters。

**③ DeepSeek-V4.1-Flash：Agent economics 正反過來改變 Transformer architecture。**

當 workload 從「短問短答」變成長時間 Agent，prefill、KV cache、context storage 就從 implementation detail 變成核心 architecture target。[arXiv](https://arxiv.org/abs/2609.19969)

---

## 本期最具技術意義

我會選 **AREX + DeepSeek-V4.1-Flash 兩條不同層次的訊號**：

- AREX：**agent-level architecture**
- DeepSeek：**model/system-level architecture**

AREX 在問：

> Agent 怎樣才不會做著做著迷路？

DeepSeek 在問：

> Agent 如果一直不結束，我們怎麼付得起它的 context？

它們其實都在處理同一個大趨勢：

> **long-horizon AI。**

---

## 本期最重要的 AI × Law / Governance 發展

很明確是 **GLM-5.3 / open-weight cyber capability diffusion**。

而且這次不是硬湊法律連結。

它真的形成完整 chain：

\[ \text{technical capability} \rightarrow \text{open-weight diffusion} \rightarrow \text{provider control weakens} \rightarrow \text{real-world misuse/defense availability changes} \rightarrow \text{release & capability governance problem} \]

這是值得持續追的研究主線。

---

## 最需要防止 hype 的一則

**AREX 的 “recursive self-improvement” 名稱。**

技術本身我認為很不錯。

但不要把它想成：

> 「AI 已經開始自主遞迴修改自己、越變越聰明。」

現在 demonstrated 的是：

> **within-task recursive research refinement。**

兩者不是一回事。[arXiv](https://arxiv.org/abs/2607.21461?utm_source=chatgpt.com)

---

# 這一期透露的更大方向

把五則放在一起，其實有一條非常清楚的主線：

```
2024–2025
更大的模型
更多 reasoning

        ↓

2026

如何讓 Agent
持續工作？
↓
AREX：驗證與狀態管理

如何讓 Agent
跑得起？
↓
DeepSeek：KV cache / prefill economics

如何讓 Agent
跟人即時合作？
↓
Gemini：async voice reasoning

如何讓 Agent
學會真實任務？
↓
MiMo：large-scale environment RL

當 Agent 能力擴散後
怎麼治理？
↓
GLM-5.3：open-weight cyber capability
```

所以我會把本期的核心概括為：

> **AI competition 正從「誰有最聰明的模型」往「誰能建立最好、最便宜、最持久、最能自我檢查的 agent loop」移動。**

這比這一期任何單一 leaderboard 名次都值得記。

---

# Part III — English terminology supplement

這部分依你的要求縮短，主體仍是技術分析。 貼上的 Markdown

|Expression|意思與語感|Example|
|---|---|---|
|**close the capability gap**|縮小能力差距；政策、科技分析很常用|Open-weight models are rapidly **closing the capability gap** with controlled frontier systems.|
|**cross a capability threshold**|跨過具有實質意義的能力門檻|The model may have **crossed a capability threshold** in autonomous exploit development.|
|**constraint-wise verification**|將要求拆開逐項驗證|AREX uses **constraint-wise verification** to identify unresolved claims.|
|**targeted follow-up research**|針對缺口進一步查證|The agent launches **targeted follow-up research** instead of restarting from scratch.|
|**long-horizon workload**|需長時間、多步驟運作的工作負載|KV-cache costs become significant in **long-horizon workloads**.|
|**input-heavy workload**|輸入遠多於輸出的工作模式|Agentic systems are often **input-heavy workloads** because their histories keep growing.|
|**background reasoning**|背景推理，尤其適合 async agents|The voice agent can perform **background reasoning** while continuing the conversation.|
|**non-blocking tool call**|不阻塞主流程的工具呼叫|A **non-blocking tool call** allows the dialogue to continue while an API request runs.|
|**capability diffusion**|某項能力由少數 frontier systems 向更多模型／使用者擴散|**Capability diffusion** complicates governance based solely on provider access controls.|
|**deployment economics**|部署成本與效益結構|Cache compression can materially change the **deployment economics** of large-scale agents.|

### Retrieval

1. 「縮小與 frontier models 的能力差距」→ **close the ______ gap**
2. 「跨過一個重要能力門檻」→ **cross a ______ ______**
3. 「逐項條件驗證」→ **______-wise verification**
4. 「針對尚未解決部分進一步研究」→ **targeted ______ research**
5. 「長時間、多步驟工作負載」→ **long-______ workload**
6. 「背景推理」→ **background ______**
7. 「不阻塞的工具呼叫」→ **non-______ tool call**
8. 「能力擴散」→ **capability ______**

---

**Answers:**

1. capability
2. capability threshold
3. constraint
4. follow-up
5. horizon
6. reasoning
7. blocking
8. diffusion

整體而言，**Issue 373 比上一期更值得你從 AI × Law 角度保存**：不是因為每一則都有法律意義，而是 GLM-5.3 這一條已經把「open weights → safeguard persistence → cyber capability diffusion → governance architecture」串成一個相當成熟的研究問題；其他四則則很好地補上 Agent 技術正在往哪裡演進的背景。這也符合你原本要求的原則：先判斷技術本身重不重要，再判斷法律意義，不反過來硬套。 貼上的 Markdown