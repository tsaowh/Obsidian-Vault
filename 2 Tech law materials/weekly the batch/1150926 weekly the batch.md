## 先做 freshness check

我先確認最新一期，而不是直接沿用前一期。

目前可驗證到 **The Batch 最新一期為 2026 年 9 月 25 日**，標題是 **“Opus Stalks the Frontier, Jev Classifies Everything, Running Two Models in One Agent”**。搜尋結果同時列出前一期 **9 月 18 日的 “Meta’s Agent Security, The Navier-Stokes Controversy, Fraud on Claude”**，因此可以確認這次不是重複分析上一週。:chatgpt-content-reference{index="0"}

你貼的規則要求「先白話、再深入技術」，而且不要硬把每一則 AI 新聞拉去談法律；下面我會照這個結構。:chatgpt-content-reference{index="1"}

---

# Part I — 本期重點深度分析

這一期我認為真正值得深入看的有三條主線：

1. **Claude Opus 5.5：重點不是單純又一個更強模型，而是 frontier capability 的成本正在快速下降**
2. **Jev：試圖把「LLM 什麼都生成文字」拆成「專門負責做決策的模型」**
3. **Multi-model agent：AI 系統開始從「挑一個最強模型」轉向「一個 workflow 裡讓不同模型分工」**

其中第 2、3 點，其實比「某模型 benchmark 又多幾分」更值得長期追蹤。

---

# 1. Claude Opus 5.5：frontier intelligence 變便宜了

Anthropic 在 9 月 22 日推出 Claude Opus 5.5。官方定位很有意思：它不是宣稱單純暴力堆算力，而是強調 **同級甚至更高能力，用更少 token、更少 agent steps、更低成本完成工作**。:chatgpt-content-reference{index="2"}

## 白話先說：它實際上在做什麼？

以前如果你叫 AI agent 做一個大型工作，例如：

> 「把這個 20 萬行的程式碼專案全部檢查一次、修改問題、跑測試，再確認修改沒有破壞原本功能。」

問題不只是「模型聰不聰明」。

模型可能：

- 看錯地方；
- 改了又改；
- 重複呼叫工具；
- 走錯路再回頭；
- 花很多 token 重新讀已經看過的東西；
- 做了 50 步，其實 20 步就能做完。

Opus 5.5 的重要訊號是：

> **模型不只是更會答題，而是更有效率地完成一整串工作。**

這對 agent 特別重要。

聊天模型多講 30% 的字，頂多比較貴；但 agent 如果每一個任務多做 30% 的 tool calls、shell commands、搜尋、讀檔、修改、重新驗證，成本與時間會一路累積。

所以真正值得注意的不是：

> 66.4% 比 52.3% 高。

而是：

> **它開始能用更短的 execution trajectory 完成同一個複雜任務。**

---

## 技術深入：為什麼這比 benchmark 加幾分更重要？

Anthropic 公布的幾個數字很有代表性。

在 **Terminal-Bench 4.0** 上：

- Opus 5.5：66.4%
- Fable 5.1：55.8%
- Opus 5：52.3%

在 **FrontierCode v1.1**：

- Opus 5.5：54.4%
- GPT-6 Astra：53.3%
- Opus 5：48.0%

而 **OSWorld 2.0** computer-use benchmark 為 81.8%。:chatgpt-content-reference{index="3"}

但要先理解這些 benchmark 到底在測什麼。

### Terminal-Bench

不是「給模型一道程式題」。

它讓 agent 進到 terminal environment，必須自己：

1. 理解任務；
2. 查看檔案；
3. 執行 command；
4. 找問題；
5. 修改；
6. 測試；
7. 根據結果繼續修正。

因此測的是：

**planning + tool use + state tracking + debugging + persistence**

的組合能力。

這跟傳統 MMLU 類的問答 benchmark 差很多。

---

### FrontierCode

它問的也不是：

> 「這段 code 看起來對不對？」

而比較接近：

> **Agent 做出的修改，是否真的有品質到可以 merge。**

這開始接近實際軟體工程，而不只是 code generation。

Anthropic甚至明確指出，在 default effort 下，Opus 5.5 的 FrontierCode 成績已高於 GPT-6 Astra 的最高成績，而單一任務成本約只有其五分之一。:chatgpt-content-reference{index="4"}

---

## 真正有意思的技術變化：trajectory efficiency

Anthropic列出的實務案例其實比 benchmark 更值得看。

例如：

- 一個 20 萬行 codebase audit，Opus 5.5 少於 3 小時完成；
- Opus 5 超過 20 小時，而且耗用約 2.5 倍 token；
- HAProxy C → Rust migration，Opus 5.5 約 9.5 小時，而 Fable 5.1 約 12 小時；
- 某些實測中，相較 Opus 5，tool calls 約少 40%，token 約少一半。:chatgpt-content-reference{index="5"}

這暗示一件重要的事情：

### AI agent 的 scaling 不再只是「每一步變聰明」

另一條路是：

**讓整條 reasoning/action trajectory 變短。**

可以寫成：

$$
\text{Task cost}
\approx
\sum_{t=1}^{N}
(
\text{model tokens}_t +
\text{tool cost}_t +
\text{latency}_t
)
$$

過去主要努力降低每一個 $t$ 的成本。

現在另一個非常重要的 optimization target 是降低：

$$
N
$$

也就是：

> **完成任務到底需要幾步。**

這會是 agent engineering 很重要的一條線。
==note start==

這段真正重要的不是「Opus 5.5 benchmark 又高了幾分」，而是 **frontier model 開始能用更少步驟、更少 token、更少工具呼叫完成同樣複雜的 agent 任務**。對一般聊天模型來說，多講一些話只是多花一點 token；但對 agent 而言，每多一步都可能包含搜尋、讀檔、執行 command、修改程式、重新測試與驗證，因此步驟一多，成本與時間會快速累積。Opus 5.5 顯示的新方向，是不只讓「每一步更聰明」，還讓整條 **reasoning/action trajectory 更短、更有效率**。這代表未來 agent engineering 的重要目標之一，可能不只是降低單次推論成本，而是直接減少完成一個任務所需要的總步數 (N)。

- **Tool call**：AI 呼叫外部工具的動作，例如搜尋網路、執行 shell command、讀取檔案或呼叫 API。
- **Reasoning/action trajectory**：AI 在「思考 → 採取行動 → 看結果 → 再思考」之間形成的一連串步驟。
- **Trajectory efficiency**：完成同一件事時，能不能用更少步驟、更少 token、更少工具呼叫達成。
- **Terminal environment**：可以直接操作作業系統 command line 的環境，agent 必須真的執行指令，而不是只回答文字。
- **State tracking**：記住目前做到哪裡、哪些事情已完成、哪些修改造成什麼結果，避免反覆做同樣的事。
- **Scaling**：增加某種資源或改善某個維度，讓 AI 的整體能力提升。
- **Optimization target**：系統刻意想改善的指標；這裡的新目標之一就是降低完成任務所需要的總步數 (N)。
- **Terminal-Bench 4.0**：測試 AI agent 能不能真的在 command line 裡完成複雜、多步驟的電腦工作，而不是只回答一道程式題。任務可能要求它自己查看檔案、執行指令、修改程式、跑測試並根據結果繼續處理。[TERMINAL-BENCH](https://www.tbench.ai/news/announcement?utm_source=chatgpt.com)
- **FrontierCode 1.1**：測試 AI 修改程式碼後，成果是否真的好到**專案維護者願意 merge**；不只看程式能不能跑，也看 correctness、test quality、修改範圍、程式風格與是否符合原專案規範。[Cognition](https://cognition.com/frontiercode?utm_source=chatgpt.com)
- **Codebase audit**：替整個軟體專案做「全面體檢」，系統性找出 bug、安全問題、架構問題、測試不足或其他技術風險；Anthropic 的案例更進一步，是讓 AI **audit 完還直接修正問題**。[Anthropic](https://www.anthropic.com/claude-opus-5-5?campaign=19995287&trk=public_post_comment-text&utm_source=chatgpt.com)
- **Persistence**：agent 遇到失敗後是否會繼續嘗試、重新規劃，而不是直接放棄。**這是描述 agent 行為的一般術語，不是 Terminal-Bench 4.0 的正式獨立評分項目。** Terminal-Bench 的長任務可能間接考驗這種能力。

==note end==

---

## 為什麼重要？

我會把這視為 **engineering shift，而不是單純 benchmark improvement**。

因為 agent 真正大量部署時，限制因素可能未必是：

> 「模型到底有沒有 95 IQ 還是 105 IQ？」

而是：

> 「完成一件工作需要 80 次 model call 還是 30 次？」

當 frontier-level intelligence 的 **cost per completed task** 持續下降，原本只有非常高價值工作才值得交給 agent 的邊界會往下移。

這跟雲端運算早期很像：

真正改變市場的往往不是 CPU benchmark 多 15%，而是：

> 原本太貴不能做的工作，現在值得做了。

==note start==

這段真正重要的是：**Agent 的商業價值，不只取決於模型「有多聰明」，更取決於完成一件工作的總成本有多低。** 如果同樣一個複雜任務，過去需要 80 次 model call、很多重試與工具操作，現在只需要 30 次就能完成，那即使模型能力只小幅提升，整體經濟性也可能大幅改善。這代表 AI agent 的進步正在從單純追求 benchmark 分數，轉向追求 **cost per completed task**。一旦完成任務的成本持續下降，原本「太貴、不值得自動化」的工作也會開始變得划算，這才可能真正擴大 agent 的部署範圍。

- **Engineering shift**：工程方向的轉變；重點不再只是把模型本身做得更強，而是讓整個系統更省、更快、更有效率。
- **Benchmark improvement**：基準測試分數變高；代表模型在某些測試上進步，但不一定等於實際使用成本或效率也明顯改善。
- **Frontier-level intelligence**：接近目前最頂尖模型水準的能力。
- **Cost per completed task**：完整完成一件工作所花的總成本，不只是一次 API 呼叫價格，而是整個任務所有模型呼叫、工具使用、重試與等待加總。
- **Deployment**：實際把 AI 放進真實工作流程中使用，而不是只停留在實驗或 demo。
- **Economic viability**：經濟上是否划算；也就是「這件事交給 AI 做，值不值得付這個成本」。
- **Automation boundary**：自動化邊界；哪些工作值得交給 AI 自動做、哪些工作因為太貴或太不穩定仍要人工處理。
- **Market expansion**：市場擴張；當成本下降後，更多原本不值得使用 AI 的任務也開始適合導入。

==note end==

---

## Critical assessment

這裡要稍微踩煞車。

第一，很多數據仍然來自 Anthropic 自己的 benchmark 或 early-access customers。

例如大型 code migration、企業 evaluation 都是很有價值的 evidence，但不是完全獨立 controlled experiment。

第二，Anthropic 自己也明確承認：

> frontier models 到這個能力水準之後，小幅 benchmark 差距已越來越不能代表實際使用差距。:chatgpt-content-reference{index="6"}

這反而是很健康的提醒。

第三，benchmark harness 很重要。

例如：

- tool configuration；
- reasoning effort；
- fallback model；
- context budget；
- retry policy；

都會顯著影響 agent benchmark。

所以「66.4 > 57.9」不能簡化成：

> Claude 一定比另一個模型強。

真正需要觀察的是：

**在固定 workload 下的 success × cost × latency × reliability。**

==note start==

**Opus 5.5 的成績看起來很強，但不能只看 benchmark 分數就直接判斷它在所有真實工作上一定更好。** 因為不少證據仍來自 Anthropic 自己的測試或 early-access customers，雖然有參考價值，但不等於完全獨立、可控制變因的實驗；而且 agent benchmark 對測試環境非常敏感，像是用了哪些工具、給模型多少 reasoning effort、context 多大、失敗後能不能 retry，甚至有沒有 fallback model，都可能改變最後分數。所以真正值得看的，不是單純「66.4 比 57.9 高」，而是：**在同一種實際工作下，哪個系統能以更低成本、更短時間、更高成功率與更穩定的表現把任務完成。**

- **Early-access customers**：提早取得新模型使用權的客戶；他們的實測很有參考價值，但通常不是嚴格控制條件的學術實驗。
- **Controlled experiment**：控制實驗；盡量讓其他條件保持一致，只改變要比較的因素，才能更有把握判斷差異是由什麼造成的。
- **Benchmark gap**：不同模型在 benchmark 上的分數差距；分數差一點，不一定代表真實使用體驗也差很多。
- **Benchmark harness**：執行 benchmark 的整套測試環境與規則，包括模型能用什麼工具、多少 context、可以重試幾次、推理設定等。
- **Tool configuration**：測試時允許模型使用哪些工具，以及工具怎麼設定。
- **Reasoning effort**：允許模型投入多少推理資源；想得更久、用更多計算，通常可能提高表現，但也會增加成本與延遲。
- **Fallback model**：主要模型失敗時，改由另一個模型接手處理。
- **Context budget**：一次任務允許模型看到多少上下文資訊；太少可能漏資訊，太多則可能增加成本與干擾。
- **Retry policy**：模型失敗後可以重試幾次、在什麼條件下重試的規則。
- **Reliability**：在不同案例、不同時間重複執行時，能不能穩定成功，而不是偶爾表現很好。
- **Success × cost × latency × reliability**：不是只看一個分數，而是同時看「做不做得成、花多少錢、要多久、穩不穩定」；這比單一 benchmark 更接近真實部署價值。

==note end==

---

## AI × Law / governance

這一則目前 **沒有必要硬拉成 AI 法律研究題目**。

比較合理的鏈條只有：

> ==agent 成本下降==  
> → ==更長時間、更自主==的 AI workflow 更容易部署  
> → ==AI 開始執行更多具實際效果的行為==  
> → ==才會逐漸產生 oversight / accountability 問題。==

但是現在若立刻延伸成「AI 責任法」之類，還太泛。

比較值得你記住的是：

> ==**agentic autonomy 的治理問題，未來很可能不是因為模型突然出現全新能力，而是因為既有能力突然便宜到可以大量部署。**==

這個角度反而比較有研究價值。

---

# 2. Jev：AI 不一定要「生成答案」

這是我認為本期 **概念上最值得記住** 的技術。

TypeSafe AI 在 9 月 15 日公開 Jev，把它稱為第一個 **System One Model**。

核心想法非常簡單：

> 很多我們現在叫 LLM 做的事情，其實根本不需要它「寫東西」。 :chatgpt-content-reference{index="7"}


---

## 白話先說：它實際上在做什麼？

假設你的系統收到一封 email：

> 「我的帳單有問題，已經被重複扣款兩次。」

你真正想要的可能只是：

```text
billing_problem
```

或：

```text
priority = high
```

但現在常見做法是：

1. 把 email 丟給 GPT / Claude；
2. 模型開始一個 token、一個 token 生成；
3. 回傳：

> "Based on the user's message, this appears to be a billing-related issue..."

4. 再把這串文字 parse 成 JSON；
5. 檢查 JSON 格式；
6. 最後才得到：

```json
{"category":"billing"}
```

其實非常繞。

Jev 的想法是：

> **不要叫生成式模型「寫答案」。直接叫模型在你事先定義的選項中做決策。**

輸入：

```text
email + possible categories
```

輸出直接是：

```text
billing: 0.93
technical: 0.04
account: 0.03
```

不是 prose。

==note start==

這段的核心是：**很多 AI 任務其實不是「生成」，而是「判斷」。** 例如 email 分類、風險判定、模型路由、是否需要人工審查，最後真正需要的往往只是一個選項或分數；但現在常見做法卻是先讓 GPT / Claude 生成一大段文字，再把文字解析成結構化結果。Jev 的想法是把這條路徑縮短：**如果答案空間本來就已經事先定義好，就不要讓模型自由寫作，而是直接在候選選項之間做決策並輸出分數或機率。** 這樣可能更快、更便宜，也更容易控制輸出格式與後續系統流程。

- **Jev**：TypeSafe AI 提出的模型，重點不是自由生成文字，而是直接在預先定義的選項中做判斷。
- **System One Model**：TypeSafe AI 對 Jev 使用的名稱；強調快速、直接做決策，而不是進行長篇生成式推理。    
- **Predefined options**：預先定義的選項；例如 `billing`、`technical`、`account`，模型只能從這些類別中選。
- **Score / probability**：分數／機率；模型不是只回答「billing」，而是可能回傳 `billing: 0.93`，表示它對這個判斷的信心較高。
- **Prose**：一般自然語言段落。Jev 的重點就是很多任務其實不需要先生成 prose。
- **Parse**：解析；把模型產生的文字重新拆解成程式可以使用的結構化資料。
- **JSON**：常見的結構化資料格式，例如 `{"category":"billing"}`，方便程式直接讀取。
- **Structured output**：結構化輸出；答案有固定格式，讓後續系統可以直接處理，不必再猜模型文字的意思。
- **Output space**：模型可能輸出的答案範圍。如果 output space 本來就只有幾個固定選項，就未必需要完整的生成式模型。

==note end==

---

# 技術深入：autoregressive generation vs parallel structured decision

普通 Transformer-based LLM 通常是：

$$
P(x_1,x_2,\ldots,x_n)
=
\prod_t P(x_t|x_{<t})
$$

也就是：

**一個 token 接一個 token 地生成。**

即使最終只是要：

```text
yes
```

模型仍在使用 generative decoding machinery。

Jev 則刻意限制 output space。

TypeSafe 的描述是：

> unstructured state in → typed probabilistic decisions out

也就是：

**輸入可以很自由，輸出則必須落在預先定義好的 typed solution space。** :chatgpt-content-reference{index="8"}

例如：

### Choice

```text
A / B / C / D
```

### Score

```text
1–5
```

### Binary probability

$$
P(\text{fraud}) = 0.87
$$

因此 model output 可以 **parallel sampling**，而不是 autoregressive token generation。TypeSafe稱其訓練方法為：

**Reinforcement Learning for Calibrated Decisions（RLCD）**。:chatgpt-content-reference{index="9"}

---

# Calibration 是這裡真正重要的概念

Jev最值得注意的未必是 classification。

分類模型早就存在幾十年了。

真正有意思的是它把：

> **general-purpose semantic understanding**

和：

> **calibrated structured decision**

結合。

Calibration 的意思是：

如果模型說：

$$
P=0.9
$$

那麼大量這種「0.9 信心」的答案中，理想上約 90% 應該正確。

這跟很多 LLM 的：

> 「我有 95% confidence」

完全不是同一回事。

LLM 自己口頭說「95% confident」，通常沒有良好的 statistical calibration。

如果 probability 真能校準，你就可以寫：

```text
if confidence > .95:
    auto_approve()
elif confidence > .70:
    send_to_small_llm()
else:
    send_to_human()
```

這才是真正適合 software automation 的 interface。

==note start==

Jev 的技術重點不是「又做了一個分類器」，而是**把大型模型的語意理解能力，和可以直接交給軟體使用的結構化、可校準決策結合起來**。一般 LLM 採用 autoregressive generation，即使最後只需要回答 `yes`，仍然要一個 token 接一個 token 地生成；Jev 則事先限制答案只能落在特定的選項、分數或機率範圍內，因此可以直接做 structured decision，而不必先生成文字再解析。更重要的是 **calibration**：如果模型給某類答案 90% 的信心，理想上長期來看這些答案真的應該約有 90% 正確。這使模型輸出的 probability 不只是「自稱有多有把握」，而可以直接變成自動化系統的控制訊號，例如高信心就自動處理、中等信心交給其他模型、低信心送人工審查。這才是 Jev 對 agent 與 software automation 最有意思的地方。

**LLM self-reported confidence = 語言上的自我描述**  
**calibrated probability = 經大量歷史結果驗證後，有統計意義的機率**

**Jev 的目標是 calibrated decision，不代表它在所有 domain 都已被證明完美校準。** 真正部署前，還是要看不同資料分布、domain shift、OOD 情況下 calibration 是否維持。
**Domain shift** = 還是同一種工作，但環境變了。  
**OOD** = 連題目本身都可能已經超出模型原本學過的世界。


- **Autoregressive generation**：自回歸生成；模型每次產生下一個 token，都要根據前面已經產生的內容再決定下一個，因此答案是一步一步寫出來的。
- **Generative decoding machinery**：生成式解碼機制；模型把內部計算結果逐 token 轉成文字的整套流程。即使答案只有 `yes`，傳統 LLM 仍然走這套機制。
- **Output space**：模型允許輸出的答案範圍。例如只有 `A/B/C/D`，output space 就只有四種選擇。
- **Unstructured state**：沒有固定格式的輸入資訊，例如一整封 email、一段使用者描述或大量系統狀態。
- **Typed solution space**：預先規定好的答案類型。例如答案必須是四選一、1–5 分，或 0–1 之間的機率，而不是任意寫一段文字。
- **Structured decision**：結構化決策；模型直接輸出程式可以使用的選項、分數或機率，而不是先產生自然語言。
- **Parallel sampling**：不是像 LLM 那樣依序生成一長串 token，而是針對預先定義的決策空間直接計算或取樣結果，因此更適合固定格式的決策任務。
	- **一次把整個 decision space 的結果算出來**。這就是它說的 **parallel sampling**
	- 輸出維度彼此不需要按 token 順序生成，可以在同一次模型計算中一起得到
- **RLCD（Reinforcement Learning for Calibrated Decisions）**：TypeSafe 對 Jev 訓練方法的稱呼，目標不只是讓模型「選對答案」，還希望它給出的信心水準具有較好的統計意義。
- **Calibration**：信心校準；模型說自己有 90% 把握的案例，長期統計下真的應該大約有 90% 是正確的。
- **Statistical calibration**：不是看單一題的 90% 準不準，而是看大量「模型都報 90%」的案例，實際正確率是否也接近 90%。
- **LLM self-reported confidence**：LLM 在文字裡說「我有 95% 把握」。這通常只是生成出來的一句話，不代表經過統計校準的 95% probability。
- **Confidence threshold**：信心門檻；系統可以依模型信心決定下一步，例如高於 95% 自動處理，70–95% 交給另一模型，其餘人工審查。
- **Software automation interface**：讓軟體能直接根據 AI 輸出来執行規則的介面。相比一段自然語言，`fraud = 0.87` 這類結構化、可校準結果更容易安全地接進自動化流程。

==note end==

---

# 為什麼速度可以差這麼多？

TypeSafe公布的速度是約：

**70–500 ms**

並聲稱在某些 System-One shaped workloads 可以比 frontier LLM 快約 40–200 倍；輸入價格為每百萬 token $0.042，output 不另外計價。:chatgpt-content-reference{index="10"}

原因不難理解。

LLM：

$$
token_1
\rightarrow token_2
\rightarrow token_3
\rightarrow \cdots
\rightarrow token_n
$$

每一個 token 都依賴前面生成結果。

Jev：

$$
state
\rightarrow
\{p_1,p_2,\ldots,p_k\}
$$

一次把 decision variables 算出來。

所以當 output 本來就很小時，autoregressive generation 其實是一種昂貴 overhead。

==note start==

Jev 之所以可能比一般 frontier LLM 快很多，關鍵不是它「算得比較聰明」，而是它根本**不用走完整的逐 token 生成流程**。一般 LLM 即使最後只需要輸出 `yes`、`billing` 或一個風險分數，仍然要依序產生 token，而且每個新 token 都依賴前面已經產生的內容；Jev 則把答案限制在預先定義好的 decision space，直接從輸入狀態一次算出各選項的分數或機率。當任務本來只需要一個分類、分數或 yes/no 決策時，省掉 autoregressive generation，就能大幅降低不必要的計算與延遲。因此它的速度優勢主要來自：**不要用一套為「寫長篇文字」設計的機制，去完成其實只需要「做一個決定」的工作。**

==note end==

---

# 但「can't hallucinate」這句話要非常小心

TypeSafe自己的 marketing wording 說 Jev 「can't hallucinate」。

這句若不拆解，很容易誤會。

它真正能保證的是：

> **schema correctness。**

例如你只允許：

```text
A
B
C
```

它不可能回答：

```text
elephant
```

也不會產生 malformed JSON。

TypeSafe自己後面也承認，他們所謂 0% hallucination 並不是 empirical accuracy measurement；保證的是 schema matching。:chatgpt-content-reference{index="11"}

所以：

### 它可以保證

$$
\text{output} \in \text{allowed\_space}
$$

### 但不能保證

$$
\text{chosen\_output} = \text{correct\_answer}
$$

這個區別非常重要。

==note start==

TypeSafe 說 Jev「**can't hallucinate**」時，要非常小心地理解。它真正能保證的不是「模型一定判斷正確」，而是**模型不會輸出預先定義範圍以外的東西**。例如系統只允許 `A / B / C`，Jev 就不會突然回答 `elephant`，也不會產生格式錯誤的 JSON；這叫 **schema correctness**。但它仍然可能在 `A / B / C` 裡選錯，例如正確答案是 A，它卻高信心選了 B。因此更準確的說法是：**Jev 可以大幅降低「格式型 hallucination」，但不能消除「判斷錯誤」。** 這也是為什麼 calibration 仍然很重要：模型不只要輸出合法選項，還要讓自己的信心水準和實際正確率盡可能對得上。

==note end==

---

# 為什麼這個方向值得注意？

因為現在很多 AI engineering 其實長這樣：

```text
LLM
 ↓
JSON
 ↓
parser
 ↓
validator
 ↓
retry
 ↓
LLM
 ↓
if statement
```

Jev 試圖把它變成：

```text
decision model
 ↓
if statement
```

如果這一類模型成熟，AI infrastructure 很可能出現分工：

### Generative model

負責：

- 寫作
- reasoning
- code
- explanation
- planning

### Decision model

負責：

- classification
- routing
- scoring
- filtering
- verification
- guardrails
- escalation

這是非常合理的 system architecture。

==note start==

這個方向值得注意，因為現在很多 AI 系統其實用了很繞的方式來做一個本來很簡單的決策：先讓 LLM 生成文字或 JSON，再經過 parser、validator 檢查格式，格式錯了就 retry，最後才把結果交給程式的 `if statement`。Jev 這類 decision model 想做的，是把中間這些生成、解析、驗證與重試步驟盡量拿掉，直接輸出程式可以使用的決策結果。長期來看，AI infrastructure 很可能因此出現更清楚的分工：**generative model 負責需要創造、推理與規劃的工作；decision model 負責分類、路由、打分、過濾、驗證與升級處理。** 這種架構的重點不是誰取代誰，而是讓不同模型各自處理最適合自己的工作，讓整個 AI system 更便宜、穩定，也更容易控制。

==note end==

---

# Critical assessment

這個東西我反而會比 Opus 5.5 更謹慎。

TypeSafe公布的「193.6× faster、444.6× cheaper」等數字來自他們自行設計的 workflow evaluation。

他們自己也明確承認：

- 這些可能位於實際效益的高端；
- workflow 是自己團隊製作；
- reference answers 用 Astra + Fable；
- evaluation 本身可能存在 bias。:chatgpt-content-reference{index="12"}

這是很重要的 caveat。

因此目前比較合理的結論不是：

> 「Jev 已經取代 LLM classification。」

而是：

> **這提出了一種很合理的 architecture challenge：我們是否真的需要用 autoregressive generative model 來解每一種 AI 問題？**

這個問題本身非常值得追。

---

# AI × Law / Governance

這一則反而真的有一條比較清楚的治理連結。

假設未來大量 AI 決策不是產生文字，而是：

```text
approve / deny
risk = 0.83
route = investigation
fraud = true
```

那麼治理重點會從：

> 「AI 說了什麼？」

逐漸變成：

> 「AI 的 decision threshold 如何設定？」

例如：

$$
P(\text{fraud}) > 0.85
\Rightarrow
\text{freeze account}
$$

真正決定結果的可能不是模型本身，而是：

- threshold；
- confidence calibration；
- fallback rule；
- human-review boundary；
- choice taxonomy。

這非常接近你可以切入的 **AI × 公法監管 / 行政自動化** 問題。

### 值得追蹤的研究問題

一個相當實際的題目會是：

> **在行政自動化系統中，當 AI 不直接產出最終決定，而僅輸出 probabilistic classification 時，法律控制應著重模型本身、決策閾值，還是 surrounding workflow？**

這個比泛泛談「AI 可解釋性」具體很多。

==note start==

TypeSafe 公布的巨大速度與成本優勢，主要來自自己設計的 workflow evaluation，本身可能存在任務選擇、reference answer 與評測方法上的偏差。因此現在比較合理的結論，不是「Jev 已經證明可以取代 LLM classification」，而是它提出了一個很值得追的架構問題：**如果任務最後只需要分類、打分或做 yes/no 決策，我們是否真的需要每次都動用 autoregressive generative model？** 更重要的是，一旦這類 probabilistic decision model 大量進入真實系統，治理焦點也會跟著改變：真正影響人民或使用者結果的，可能不只是模型本身，而是 **threshold 怎麼設、confidence 是否校準、什麼情況交給人工、失敗時怎麼 fallback，以及系統一開始把世界分成哪些類別。** 對行政自動化而言，法律要控制的對象因此可能不只是「AI model」，而是整套 **decision workflow**。

- **Workflow evaluation**：拿一整套工作流程來測試模型，而不是只比較單一道題；結果很容易受到 workflow 怎麼設計影響。
- **Reference answer**：評測時拿來當標準答案或比較基準的答案。
- **Evaluation bias**：評測偏差；測試方法、資料或標準答案可能無意間比較有利於某一方。
- **Caveat**：重要的限制條件或保留事項；看到研究結果時不能忽略。
- **Architecture challenge**：對既有系統設計提出根本問題；這裡就是在問「為什麼所有 AI 任務都要用生成式 LLM？」
- **Probabilistic classification**：不是只回答「是／否」，而是輸出一個機率，例如 `fraud = 0.83`。
- **Decision threshold**：決策閾值；系統規定「機率高到多少才採取某個行動」。
- **Confidence calibration**：信心校準；模型說 90% 有把握時，長期統計下是否真的約有 90% 正確。
- **Fallback rule**：備援規則；模型不確定、失敗或結果異常時，下一步改由什麼系統或人處理。
- **Human-review boundary**：人工審查邊界；規定哪些情況可以全自動、哪些情況必須交給人。
- **Choice taxonomy**：系統事先設計好的分類架構；例如只有 `low / medium / high risk`，其實已經先決定系統「怎麼看世界」。
- **Surrounding workflow**：模型周圍的整套決策流程，包括 threshold、routing、fallback、人工審查與後續動作。
- **Administrative automation**：行政自動化；政府利用 AI 或其他系統協助分類、審查、排序或處理行政案件。
- **Legal control**：法律控制；法律究竟要規範模型、決策門檻，還是整套工作流程。
- **AI explainability**：AI 可解釋性；關心模型為什麼做出某個結果，但在這類 decision system 中，只談解釋可能還不夠，因為真正決定法律效果的還包括 threshold 和 workflow。

==note end==

---

# 3. 「一個 Agent 裡跑兩個模型」：Multi-model orchestration

這一期標題第三個重點，我認為應該放在更大的趨勢裡理解：

> **AI product 的競爭單位正在從 model 轉向 system。**

---

## 白話先說

以前大家常問：

> GPT、Claude、Gemini，到底哪個最好？

但實際做 agent 後，很快會發現這個問題不太對。

因為一個任務可能包含：

```text
讀 300 封 email
→ 判斷哪封重要
→ 找出其中 10 封
→ 查資料
→ 寫回覆
→ 檢查
→ 執行動作
```

你完全沒必要讓最昂貴模型做所有事情。

比較合理的是：

```text
cheap / fast model
    ↓
classification / extraction
    ↓
hard?
 ↙       ↘
no        yes
↓          ↓
處理       frontier model
```

或者甚至：

```text
Model A：planning
Model B：coding
Model C：verification
```

所以真正的 AI agent 很可能不是「一個 model」。

它是一個 **orchestrated system**。

---

# 技術深入：model routing

可以把 routing 抽象成：

$$
r(x)
=
\arg\min_m
C(m,x)
$$

subject to：

$$
Q(m,x) \ge q_{\min}
$$

其中：

- $m$：candidate model；
- $C$：cost / latency；
- $Q$：預期品質；
- $q_{\min}$：最低可接受品質。

最簡單的 architecture 是 cascade：

```text
small model
   ↓
confidence high?
 ┌───────┐
yes     no
↓        ↓
accept   large model
```

Jev 自己也正好很適合充當這個 gate。

因此你會發現本期第 2、3 條新聞其實連在一起：

> **Jev 這類 decision model + frontier generative model**

可能形成新的 agent architecture。

==note start==

未來 AI Agent 的競爭，重點不再只是「哪個單一模型最強」，而是**整個系統怎麼把工作分配給不同模型**。簡單、重複、低風險的任務交給便宜快速的小模型，困難或需要高品質推理的任務再交給強大的 frontier model；這樣可以同時兼顧**成本、速度與品質**。換句話說，AI 產品正在從「比模型能力」走向「比整套系統架構與協作能力」。

- **Multi-model orchestration（多模型協作／編排）**：讓多個不同模型一起工作，各自負責最適合的任務，像一個有分工的團隊。
- **Model routing（模型路由）**：像「派工員」，先判斷這個任務該交給哪一個模型。
- **Cascade（級聯）**：先讓便宜的小模型處理；如果不夠有把握，再升級給更強、更貴的模型。
- **Frontier model（前沿模型）**：目前能力最強的一級大型模型，通常推理與生成效果最好，但成本也較高。
- **Decision model（決策模型）**：不是主要拿來寫長篇文字，而是做分類、判斷、選擇，例如決定「通過／不通過」、「重要／不重要」。
- **Orchestrated system（編排式系統）**：不是只靠一個模型，而是把模型、工具、流程、驗證機制整合成一個完整 AI 系統。
- **`r(x) = argmin C(m,x)` subject to `Q(m,x) ≥ q_min`**：白話就是「在品質至少達標的前提下，選出最便宜或最快的模型」。

==note end==

---

# 這裡真正重要的是「heterogeneous intelligence」

過去 multi-agent 常常是：

```text
Claude
Claude
Claude
Claude
```

只是讓同一個模型扮演不同角色。

但更合理的未來可能是：

```text
Classifier
Retriever
Small LLM
Frontier reasoning model
Vision model
Verifier
```

每個模型只負責自己擅長的工作。

這比較像傳統軟體工程：

你不會說：

> 「整個作業系統到底哪一個 function 最強？」

而是不同 component 分工。

AI infrastructure 也開始往這個方向走。

---

# 為什麼這值得記住？

因為這會改變「模型競爭」的意義。

未來可能不是：

> Model A beats Model B.

而是：

> System X 使用 A+B+C 的組合，能否以更低成本、更高 reliability 完成 workload？

因此 evaluation unit 可能從：

$$
\text{model}
$$

移向：

$$
\text{model + harness + routing + tools + memory + verification}
$$

這也是為什麼最近單純 leaderboard 越來越難完整描述實際 AI capability。

==note start==

這裡真正重要的不是「多個 Agent」，而是 **heterogeneous intelligence（異質智慧）**：不要只是讓同一個大型模型扮演不同角色，而是把不同種類的 AI 元件組成一個系統，讓分類模型負責分類、搜尋模型負責找資料、小模型處理簡單任務、強推理模型處理難題、視覺模型看圖片、驗證模型檢查答案。這就像傳統軟體工程，不會要求一個元件包辦所有功能。因此未來真正要比較的，可能不是「Model A 比 Model B 強多少」，而是**哪一整套 AI 系統能以更低成本、更高可靠性完成真實工作**；AI 能力的評估單位，也會逐漸從單一模型轉向完整系統。

==note end==

---

# AI × Law / Governance

這裡有一個非常值得注意、而且不算硬牽的問題：

如果一個 AI 系統最後造成錯誤，到底是哪一層出了問題？

例如：

```text
Jev → classified high risk
        ↓
Router → selected model B
        ↓
Model B → recommended action
        ↓
Agent → executed action
```

法律與監管若只要求：

> 「說明使用哪個模型。」

資訊可能根本不夠。

真正需要了解的是：

- routing policy；
- model composition；
- confidence threshold；
- fallback；
- human intervention point；
- tool permissions。

換句話說：

> **governance object 可能必須從「AI model」逐漸轉向「AI system」。**

這個方向其實很適合你目前想找的「技術夠實、法律不要太法理」的研究路線。

==note start==

當 AI 從「單一模型」變成由分類器、路由器、不同模型與工具共同組成的 Agent 系統後，出錯時就不能只問「是哪個模型答錯」。真正的問題可能發生在**分類、模型選擇、門檻設定、失敗備援、人工介入或工具權限**任何一層。因此未來 AI 治理的對象很可能要從單純監管 **model**，進一步轉向監管整個 **AI system architecture**：不只是知道用了什麼模型，而是要知道「這個決策到底是怎麼一路形成並被執行的」。

==note end==

---

# Part II — 本期整體判斷

### Top 3 值得記住

**1. Frontier capability 的 cost-per-task 正快速下降**

Opus 5.5 的重要性不是只有更高 benchmark，而是完成複雜 agentic workload 所需 token、steps、時間都下降。:chatgpt-content-reference{index="13"}

**2. 「生成模型做所有事」可能不是終局**

Jev代表另一種 architecture：需要判斷時直接輸出 probabilistic structured decision，而不是先生成文字再解析。:chatgpt-content-reference{index="14"}

**3. AI engineering 正從 model-centric 走向 system-centric**

routing、specialized models、verification、tools、agent harness 開始和 base model 一樣重要。

---

## 本期技術上最值得注意

我會選 **Jev / System One model 這個概念**。

不是因為目前已證明它一定成功，而是它提出了一個非常基本、又常被忽略的問題：

> **為什麼我們要用會寫文章的 autoregressive language model，來完成只需要回答 yes/no 的工作？**

如果這個方向成立，對 AI infrastructure 的影響可能比一次 frontier model benchmark 更新更長久。

---

## AI × Law 最值得你追蹤的點

不是「Claude 變強會不會需要更多監管」。

而是：

> **AI systems 從單模型演變為 probabilistic decision layer + router + multiple generative models + tools 之後，法律上的 transparency、auditability 與 accountability 對象到底應該是哪一層？**

這條技術 → deployment → consequence → governance 的鏈是成立的。

而且這類題目不要求你一開始就進入非常深的法理學，卻需要理解真正的 AI architecture。

我覺得這正是很適合你慢慢累積 AI & Law 能力的題型。

---

## 哪一項目前最需要防 hype？

**Jev。**

不是因為概念不好，反而是概念很好。

問題是目前最亮眼的 speed / cost comparison 大量來自 TypeSafe 自己設計的 evaluation；官方自己也承認這些數字可能是實際 gain 的較高端。:chatgpt-content-reference{index="15"}

所以目前應理解成：

> **值得密切追蹤的新 model category hypothesis**

而不是：

> **已經證明 generative LLM 不適合 classification。**

---

# 這一期透露出的更大趨勢

我會用一句話概括：

> **AI 的下一輪進步，越來越不只是「把單一模型做得更聰明」，而是「把 intelligence 做得更便宜、更專門化、更能組合」。**

可以畫成：

```text
2023–2024
更大的模型
      ↓
2025
Reasoning models
      ↓
2025–2026
Agents + tools
      ↓
現在逐漸浮現
Specialized intelligence
+ model routing
+ cheap decision layers
+ frontier model escalation
```

如果這條線繼續走下去，「最強模型排行榜」的重要性可能逐漸下降。

真正重要的是：

$$
\frac{\text{successful useful work}}
{\text{cost × time × human supervision}}
$$

也就是 **每單位成本究竟能可靠完成多少工作**。

這是我認為本期最值得帶走的觀念。

==note start==

這一期最值得記住的不是某個單一模型又變強多少，而是 **AI 的進步方式正在改變**：過去主要靠把模型做大、做聰明，現在則越來越重視把不同能力拆開，讓便宜的小模型、專用模型、工具與最強的 frontier model 組成一套系統。簡單工作用便宜能力處理，困難工作才升級給昂貴模型。未來真正重要的指標因此可能不是「誰的 benchmark 分數最高」，而是**一套 AI 系統花同樣的錢、時間和人工監督，究竟能穩定完成多少真正有用的工作**。

==note end==


---

# Part III — English Terminology Supplement

這次只留幾個真的值得你吸收的。

| Expression | 意思與語感 | Example |
|---|---|---|
| **pace the frontier** | 控制／調節最前沿能力推進速度 | *The company pledged to pace the frontier while continuing to improve model efficiency.* |
| **cost per task** | 完成單一工作真正付出的總成本，不只是 token 單價 | *Cost per task is often more meaningful than the nominal API price.* |
| **structured decision** | 有固定結構、可由程式直接處理的決策結果 | *The model returns a structured decision rather than free-form text.* |
| **calibrated confidence** | 經校準、具有統計意義的信心水準 | *Reliable automation requires calibrated confidence rather than a model's verbal expression of certainty.* |
| **predefined solution space** | 事先限定的答案空間 | *Restricting the model to a predefined solution space eliminates malformed outputs.* |
| **model routing** | 根據工作性質分派給不同模型 | *Model routing allows simple requests to be handled by cheaper models.* |
| **fallback model** | 主模型不適用或信心不足時改用的備援模型 | *Low-confidence cases are escalated to a more capable fallback model.* |
| **execution trajectory** | agent 從接收工作到完成工作經過的一整串推理與操作步驟 | *A shorter execution trajectory can dramatically reduce agent costs.* |
| **system-level evaluation** | 不只評模型，而評整個 AI 系統 | *Agentic AI increasingly requires system-level evaluation rather than model-only benchmarks.* |

### Retrieval

1. 「校準過的信心水準」英文？
2. 「模型路由」？
3. 「事先限定的答案空間」？
4. 「完成單一任務的成本」？
5. Agent 執行任務的一整串行動路徑？
6. 「結構化決策」？
7. 主要模型失敗時使用的模型？
8. 不只測模型、而是測整套系統？

---

**答案**

1. calibrated confidence  
2. model routing  
3. predefined solution space  
4. cost per task  
5. execution trajectory  
6. structured decision  
7. fallback model  
8. system-level evaluation