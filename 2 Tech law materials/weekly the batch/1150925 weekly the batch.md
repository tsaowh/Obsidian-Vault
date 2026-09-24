# The Batch 深度分析｜Issue 371｜2026-09-18

截至 **2026 年 9 月 24 日**，最新一期是 **Issue 371：〈Meta’s Agent Security, The Navier-Stokes Controversy, Fraud on Claude〉**。這一期很值得細讀，因為三篇主軸乍看完全不同——個人 AI Agent、安全架構、AI 解數學難題、模型蒸餾——但其實共同指向一個很明顯的轉折：

> **AI 的核心問題正在從「模型有多聰明」轉向「模型被放進什麼系統、取得什麼權限、留下什麼證據，以及出了問題誰負責」。**

因此這次我不逐篇流水帳，而集中分析最重要的三條線。([Charon Hub](https://charonhub.deeplearning.ai/issue-371/?utm_source=chatgpt.com "Meta’s Agent Security, The Navier-Stokes Controversy, Fraud on Claude"))  
[The Batch Issue 371](https://charonhub.deeplearning.ai/issue-371/?utm_source=chatgpt.com)

---

## 一、Meta Muse：Agent security 正從「模型對齊」變成「系統安全工程」

### 1. 發生了什麼？

Meta 在 9 月 8 日推出個人 AI agent **Muse**。它不是單純聊天機器人，而是能夠存取 email、calendar、browser 等外部服務，執行多步驟任務，甚至在背景持續運作的 agent。

真正值得注意的不是 Muse Spark 1.3 模型本身，而是 Meta 公開了一套相當完整的 **agent security architecture**。

Meta 的基本假設其實非常重要：

> **不要假設 Agent 不會被攻擊，而是假設它遲早會被攻擊，再限制攻擊成功後能造成的傷害。**

Meta 明確承認 prompt injection 仍是未解問題，所以 Muse 的核心模型即使被騙，也不能直接取得所有權限。([Meta AI Research](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse "How We Built Safety Into Muse | Meta AI Research"))

[Meta：How We Built Safety Into Muse](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse?utm_source=chatgpt.com)

---

### 2. 技術上真正重要的是什麼？

Muse 的安全架構可以簡化成：

**不可信 Agent → sandbox → permission broker → credential boundary → external world**

而不是：

**Agent → API key → Internet**

第一層是 **runtime isolation**。每位使用者有獨立 VM，而 agent runtime 又被放進 `systemd-nspawn` container。Runtime 裡面的 root 並不是 host root，而且 syscall、kernel capability、network access 都受到限制。更敏感的服務則放在 runtime cell 外。([Meta AI Research](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse "How We Built Safety Into Muse | Meta AI Research"))

第二層是 **credential separation**。真正的 OAuth token、API credentials 放在 `hatch-authd`，agent 本身拿到的只是 surrogate token。真正 credential 只有在網路要求獲准後，才在邊界做 **just-in-time credential insertion**。

也就是說：

**模型根本不需要知道真正的密碼或 token。**

這比「叫 LLM 不要洩漏 API key」高明很多，因為它不是靠模型服從，而是讓模型**物理上拿不到東西**。([Meta AI Research](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse "How We Built Safety Into Muse | Meta AI Research"))

第三層最有意思，是 **Sentinel**。

Muse 可以「提議」我要：

> 寄 email 給 A  
> 呼叫某 API  
> 把資料送到某網站

但真正有權批准 network egress 與 connector action 的不是 Muse，而是獨立於 agent 的 Sentinel。

Sentinel 可以檢查 hostname、IP、port、protocol、HTTP method、path，甚至 decoded request。Meta 還使用 eBPF 做資料流追蹤，當 process 讀過 user data 後，就可能被標記為 **tainted**；有風險的 egress 就失去自動批准資格。([Meta AI Research](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse "How We Built Safety Into Muse | Meta AI Research"))

這個設計非常接近資訊安全裡真正成熟的概念：

**least privilege + mandatory mediation + capability security + defense in depth**

而不是「希望 AI 自己做對的事」。

---

### 3. Human-in-the-loop 也出現一個很重要的變化

過去很多 AI 系統所謂：

> Are you sure?

其實只是 UI 上的一句確認。

Muse 把 approval 做成 **strict capability**。

例如核准：

> 這一次讓 Muse 在某網站支付 50 美元

就不代表：

> 從現在開始 Muse 可以自由支付。

Permission 可以限制成 one-time、session-scoped、task-scoped、time-bounded，且 connector、destination、use case 都可以綁定。

這是我認為這篇裡面 **對 AI governance 最有研究價值的地方之一**：

**consent 不只是文字，而可以被轉譯成 machine-enforceable authorization。** ([Meta AI Research](https://research.meta.ai/blog/security-and-safety-for-ai-agents-our-approach-with-muse "How We Built Safety Into Muse | Meta AI Research"))

---

### 4. 為什麼這比單純提升模型安全重要？

因為對 Agent 而言，錯誤的成本完全不同。

Chatbot hallucination：

> 「台北到高雄大概 100 公里。」

通常只是錯誤資訊。

Agent hallucination：

> 「我替你轉帳了。」  
> 「我寄出了那封信。」  
> 「我把文件上傳到那個網站。」

已經是 **real-world action**。

因此 Agent 安全逐漸會變成傳統資訊安全非常熟悉的問題：

**誰可以做什麼？對哪些資源？在什麼條件下？可以做多久？是否可追蹤？**

這也是為什麼我認為 Muse 的意義未必在 Muse 本身，而是在它展示了一個可能逐漸成為業界基礎模式的架構：

**LLM 不應被視為 trusted computing base。**

---

### 5. 但發布後馬上發生了一件很有意思的事

The Batch 9 月 18 日出刊後，9 月 21 日資安研究者 Patrick Wardle 公開 Muse macOS client 的漏洞。

問題並不是攻破 Meta 的 Secure VM，而是本機程式可以修改一個未公開的 transcription endpoint 設定，把 Muse 的語音／要求導向攻擊者控制的 endpoint。Meta 隨後發布 hotfix。這個攻擊需要本機已有程式碼執行能力，所以不能簡化成「任何網路攻擊者都能遠端接管 Muse」。([Ars Technica](https://arstechnica.com/security/2026/09/muse-metas-extraordinarily-privileged-ai-assistant-has-a-serious-0-day/?utm_source=chatgpt.com "Muse, Meta’s extraordinarily privileged AI assistant, has a serious 0-day"))

這件事情反而讓 The Batch 那篇文章更值得研究。

因為它顯示：

> **你可以把 cloud-side agent sandbox 做得非常漂亮，但整個 trust chain 還包含 client、OS、tokens、connector、browser、local settings、update mechanism。**

換句話說，Agent security 的安全邊界不是「LLM」；甚至也不是「Agent VM」。

而是整條：

**user → client → agent → tools → credentials → network → third-party services**

---

### 6. AI × Law / governance 意義

這裡有一個非常值得發展成科技法律研究的概念：

**從 informed consent 走向 enforceable consent。**

法律常問：

> 使用者有沒有同意？

Agent 世界可能需要進一步問：

> 使用者同意的內容，有沒有被轉換成技術上不可逾越的 permission boundary？

例如：

「我同意它讀 Gmail」

與

「我同意它讀 Gmail，而且只能讀 inbox、不能改 forwarding rule、不能寄信」

法律上可能都是 authorization，但技術風險差異巨大。

另外還有責任分配問題：

如果模型完全照規則行事，但 **desktop client 被攻破**，最後 Agent 使用合法 token 做了錯誤交易，責任究竟比較接近：

- model provider
    
- agent application provider
    
- OS/platform provider
    
- connector provider
    
- user
    

這將會是非常典型的 **system-level AI liability** 問題。

### 可發展的研究題目

**RQ1：** Machine-enforceable scoped permissions 是否應成為 high-autonomy AI agent 的 reasonable security baseline？

**RQ2：** Agent provider 若未採 least privilege、credential isolation、mandatory mediation，在侵權／資安法上的注意義務應如何判斷？

**RQ3：** 法律上的 informed consent 能否進一步與 capability-based authorization 建立對應模型？

我覺得這一條線非常適合 **AI and Law / CLSR**，因為它不是空泛談 AI ethics，而是有非常具體的 **technical architecture → legal duty** 對接。

---

# 二、OpenAI × Navier–Stokes：真正的突破可能不是「AI 解出一道數學題」

第二件事表面上最轟動，但也最容易被 headline 誤導。

## 1. 發生了什麼？

OpenAI 9 月 8 日公布，由尚未公開的內部模型組成的大規模 multi-agent system，提出 Navier–Stokes existence and smoothness problem 的解法。

OpenAI 說，在 Navier–Stokes 階段，大約有 **10,000 個 concurrent agents**，總共花約 **88 小時**找到解法；之後再花約 **17 小時**透過 GPT-6 Astra 將證明 formalize 成 Lean。

Navier–Stokes 部分約產生：

**2.7 million agent messages + 130 billion output tokens。** ([OpenAI](https://openai.com/index/navier-stokes-solution/ "On the Navier–Stokes Millennium Prize Problem | OpenAI"))

[OpenAI：On the Navier–Stokes Millennium Prize Problem](https://openai.com/index/navier-stokes-solution/)

這已經不是我們習慣的：

> 「問一個厲害模型一道數學題。」

它比較像：

> **建立一個由數千個 AI researcher 組成的計算型研究組織。**

不同 agent 嘗試不同 approach，彼此交換結果，再由 Codex consolidation，把有潛力的研究方向交叉傳遞（cross-pollination）。

---

## 2. 技術突破其實是「研究 orchestration」

這件事情最值得注意的不一定是 base model IQ。

而是三個東西結合：

**frontier model capability  
× massive inference compute  
× multi-agent research orchestration**

以前 scaling 主要談：

> 更多 training compute → 更好的模型。

現在開始出現另一條路：

> 已經訓練好的模型
> 
> - 巨量 inference compute
>     
> - 大量 parallel search
>     
> - agent coordination  
>     → 新研究結果。
>     

可以把它理解成：

### Training-time scaling

增加模型能力。

### Test-time / inference-time scaling

花更多計算尋找答案。

### Agentic research scaling

讓大量 agent 各自探索不同研究路徑，再整合成果。

Navier–Stokes 事件可能是第三種 scaling 非常極端的示範。

---

## 3. Lean formalization 為什麼很重要？

OpenAI 不只給自然語言證明，還提供 **Lean formalization**。

Lean 是 theorem prover。

簡單來說：

數學家寫：

> 因此 A 推得 B。

Lean 會要求：

> 你確定嗎？把每一步邏輯形式化給我。

因此它大幅降低：

- 漏掉條件
    
- 偷渡假設
    
- algebraic mistake
    
- informal proof gap
    

的風險。

但這裡一定要區分一件非常重要的事：

> **formal verification ≠ 整個 scientific claim 已被證明沒有問題。**

Lean 能驗證的是：

**在你 formalize 的 definitions 與 premises 下，結論是否邏輯成立。**

Lean 無法自己回答：

- 你 formalize 的問題是不是大家原本以為的那個問題？
    
- 這個結果重要到什麼程度？
    
- 是否真的具有 novelty？
    
- priority 應歸誰？
    
- formal specification 本身有沒有錯？
    

因此 formal proof 與 peer review 是互補的，不是替代關係。

---

## 4. 「AI 解開 Millennium Problem」需要一個重要限定

OpenAI 的證明處理的是 Clay 官方 formulation 裡的 **C/D alternatives**：一個 initially smooth fluid 在存在 smooth external force 的條件下，可以於 finite time 出現 singularity。OpenAI 說這足以滿足 Clay 官方問題的形式要求。([OpenAI](https://openai.com/index/navier-stokes-solution/ "On the Navier–Stokes Millennium Prize Problem | OpenAI"))

而 Clay Mathematics Institute 在 9 月 11 日使用的措辭非常審慎：

> 問題「apparently been settled」。

同時明確表示認定成果與分配 credit 的程序會刻意保持審慎。([Clay Mathematics Institute](https://www.claymath.org/news/navier-stokes-announcement/ "Navier-Stokes Announcement - Clay Mathematics Institute"))

[Clay Mathematics Institute：Navier-Stokes Announcement](https://www.claymath.org/news/navier-stokes-announcement/)

因此現在比較精確的說法不是：

**「AI 已正式拿下 Navier–Stokes 千禧難題。」**

而是：

**OpenAI 公布了一項符合 Clay 正式 formulation 的 AI-generated proof；Clay 認為問題看來已獲解決，但正式的數學共同體驗證與認定仍在進行。**

這個 distinction 很重要。

---

## 5. 更有意思的是 priority controversy

另一組數學家 Tristan Buckmaster 與 Levent Alpöge 同時間也在做密切相關研究，而且使用過 Codex / Claude。

因此立刻產生一個以前很少存在的問題：

> **AI provider 同時是研究工具提供者，又可能成為你的研究競爭者。**

OpenAI 後來表示經內部調查，Buckmaster 過去兩個月的 Codex prompts 不可能影響這次使用的 internal model，包括透過 training；OpenAI 也承認 Alpöge / Buckmaster 在 forced Euler work 上的 priority。這是 OpenAI 自己的調查結論，目前應將它視為公司的說法，而不是第三方獨立鑑識結果。([OpenAI](https://openai.com/index/navier-stokes-solution/ "On the Navier–Stokes Millennium Prize Problem | OpenAI"))

這對 AI × Law / research governance 很有意思。

以前研究資料治理的問題通常是：

> 我的 unpublished manuscript 會不會被拿去 training？

未來問題可能變成：

> **如果我把未公開 conjecture、proof strategy、source code 放進 AI research assistant，而這家公司自己也用 AI 做研究，我要如何證明未來的 discovery 是 independently derived？**

---

## 6. 這會需要新的「研究 provenance infrastructure」

我認為這件事情長期比單純 authorship 更重要。

未來 frontier AI research 可能需要保存：

- model checkpoint identity
    
- model training cutoff
    
- fine-tuning lineage
    
- user-data inclusion policy
    
- prompt logs
    
- agent trajectories
    
- timestamp
    
- intermediate artifacts
    
- dataset provenance
    

這些資料的功能會很像：

**scientific audit trail。**

不是因為大家一定作弊，而是當 AI 同時接觸成千上萬研究者的資訊，又自己產生科研成果時，單靠：

> 「我們沒有看你的資料。」

會逐漸不夠。

需要的是：

> **可驗證的 provenance。**

### 很有潛力的研究問題

**RQ1：** 當 AI provider 同時提供 research assistant 與進行自主研究時，應採何種 evidentiary standard 證明 independent discovery？

**RQ2：** 是否應建立 AI-assisted research 的 model/data/provenance disclosure standard？

**RQ3：** Formal verification 普及後，scientific peer review 的功能會如何重新分工？

這組題目我反而覺得非常適合 **AI and Law**，因為核心是：

**epistemic governance + evidence + attribution + AI-mediated research。**

---

# 三、Anthropic 指控的「illicit distillation」：最重要的其實可能不是模型抄模型

第三條線看起來像商業競爭新聞，但法律問題最多。

## 1. 發生什麼？

Anthropic 在 9 月的 threat intelligence report 表示，它偵測到七家中國 AI labs 進行未經授權的 distillation campaigns。

Anthropic 將這類行為稱為 **illicit distillation**。

其中它指稱：

- Alibaba 的相關活動在 2026 年 5–7 月超過 **151 million exchanges**
    
- Moonshot 超過 **23 million**
    
- DeepSeek 在 7 月某 14 天期間超過 **12.1 million**
    

Anthropic 還指稱 Moonshot 與 DeepSeek 曾把部分自己的使用者請求 **轉送給 Claude**，再把 Claude 的 output 顯示給原本以為自己正在使用 Kimi / DeepSeek 的使用者。([Anthropic](https://www.anthropic.com/threat-intelligence-report-september-2026 "Countering misuse of AI: September 2026 / Anthropic \ Anthropic"))

這些目前是 **Anthropic 的 attribution 與調查結果**，不是法院已確認的法律事實，這一點需要一直保留。The Batch 本身也使用「Anthropic accused/alleged」的方式報導。([DeepLearning.ai](https://www.deeplearning.ai/the-batch/some-kimi-and-deepseek-users-were-served-claude-instead-anthropic-says?utm_source=chatgpt.com "Anthropic's Accounts of Distillation, Gray-Market Transfer Stations, Straw Accounts, and User Fraud"))

[Anthropic：Detecting and countering misuse of AI — September 2026](https://www.anthropic.com/threat-intelligence-report-september-2026?utm_source=chatgpt.com)

---

## 2. 先釐清：distillation 本身不是壞事

Distillation 是非常正常的 machine-learning technique。

基本形式：

**teacher model → 產生答案 → student model 用答案訓練**

例如：

大型模型 500B parameters  
↓  
大量生成 training examples  
↓  
訓練 20B student model

希望保留一部分 teacher capability，但 inference 更便宜。

所以：

**distillation ≠ unlawful copying。**

Anthropic 所稱的 illicit distillation，是它自己定義的一個行為集合：

> industrial-scale、covert、unauthorized extraction，再搭配 fraudulent accounts、stolen API keys、proxy networks 等方式。

這個 distinction 非常重要。([Anthropic](https://www.anthropic.com/threat-intelligence-report-september-2026 "Countering misuse of AI: September 2026 / Anthropic \ Anthropic"))

---

## 3. 技術上最有意思的是 reasoning-trace extraction

Anthropic 說 Claude 為了防止 extraction，不會直接把完整 hidden reasoning 暴露出去，而是回傳一個 **thinking signature**。

但 Anthropic 指稱 Moonshot 找到 cross-session replay 方式：

**Session A**  
Claude → thinking signature

↓

**Session B**  
把 signature 再餵回 Claude

↓

設法恢復較完整的 reasoning trace

↓

拿去做 supervised fine-tuning。

這代表模型安全出現一個很有趣的新類型：

過去防的是：

**data exfiltration**

現在還要防：

**capability exfiltration。** ([Anthropic](https://www.anthropic.com/threat-intelligence-report-september-2026 "Countering misuse of AI: September 2026 / Anthropic \ Anthropic"))

---

# 4. 但真正嚴重的法律議題可能是「silent model routing」

假設使用者打開：

**Model A**

輸入：

> 幫我分析這份公司內部文件。

但 Model A provider 背後偷偷：

**把 prompt 送到 Model B provider。**

使用者不知道。

這就不是單純：

> A 偷學 B 的模型。

它同時涉及：

**使用者資料被送給誰？**

Anthropic 指稱，被轉送的內容包括姓名、email、企業資料、credentials 等敏感資訊，且部分使用者並不知道其內容被轉送到 Anthropic。([Anthropic](https://www.anthropic.com/threat-intelligence-report-september-2026 "Countering misuse of AI: September 2026 / Anthropic \ Anthropic"))

從法律角度，至少會拆成不同問題：

**一、Contract / Terms of Service**  
是否違反 API 使用條款？

**二、Privacy / data protection**  
使用者被告知的 processor / recipient 是否與實際一致？

**三、Consumer transparency**  
標示「Model A」卻實際由 Model B 生成，是否構成不透明甚至誤導？

**四、Trade-secret / unfair-competition issues**  
大量抽取 model capability 是否觸及其他法益？

**五、Computer misuse / access-control circumvention**  
若使用 fake accounts、stolen credentials、規避 geographic restrictions，又是另一組法律問題。

不能簡單把所有東西統稱：

> 「偷模型，所以侵害著作權。」

那反而會把法律問題講窄。

---

## 5. Anthropic 的說法也必須批判性閱讀

這一篇尤其不能只接受 provider 的 framing。

第一，**Anthropic 是利益關係人。**

它既是受攻擊方，也是發表 attribution 的公司。

目前外部研究者無法取得 Anthropic 全部 telemetry，因此很難完全獨立重現它的 attribution。

第二，**151 million exchanges ≠ 成功複製 Claude。**

Exchange 數量可以證明 Anthropic 所稱行為的規模，但不能直接推導：

> Qwen 的能力有多少百分比是從 Claude 得來。

第三，The Batch 自己也特別提醒：

中國模型的競爭力不能全部歸因於 distillation；這些 labs 自身也有大量公開且重要的 architecture、training 與 engineering innovation。([DeepLearning.ai](https://www.deeplearning.ai/the-batch/some-kimi-and-deepseek-users-were-served-claude-instead-anthropic-says?utm_source=chatgpt.com "Anthropic's Accounts of Distillation, Gray-Market Transfer Stations, Straw Accounts, and User Fraud"))

所以這裡合理的研究姿勢是：

> **把 unauthorized extraction 的證據與競爭模型能力來源分開分析。**

---

## 6. 這條線的研究價值非常高

我認為這甚至比「AI output 有沒有 copyright」更有新意。

### RQ1

**AI service 是否應負有 model-routing disclosure duty？**

例如：

> Powered by Model A

究竟表示 UI brand？  
還是 inference provider？  
還是 final output provider？

### RQ2

**什麼程度的 model-output harvesting 應被視為普通 interoperability / benchmarking，而什麼程度才是 unauthorized model extraction？**

### RQ3

**當 AI service secretly forwards prompts 給另一 provider 時，data controller / processor / subprocessor 的法律角色應如何認定？**

### RQ4

**是否需要建立 AI training-data provenance，標示 synthetic data 的 upstream model source？**

這一組尤其適合 **CLSR / IJLIT**：技術機制夠具體，privacy、contract、competition、cybersecurity 都可以接進來。

---

# 四、把三篇放在一起看：這一期真正重要的訊號

這一期最值得記住的三件事不是三則新聞本身，而是三個結構性變化：

1. **Agent safety → system security**  
    AI safety 不再只是 alignment；開始進入 sandbox、least privilege、authorization、credential isolation、egress control。
    
2. **AI research → compute-intensive organization**  
    frontier science 不一定靠「一個超聰明模型」，而可能靠數千 agent + massive inference compute + formal verification。
    
3. **Model output → strategic asset**  
    output、reasoning trace、agent trajectory 都開始具有訓練價值，因此 provenance、access control 與 data governance 會越來越重要。
    

這三條其實最後會匯流到同一組字：

> **authorization、provenance、auditability、accountability。**

這也是我覺得 Issue 371 對 AI × Law 特別有價值的原因。

---

# 五、本期總評

### 最值得記住的 3 個發展

**① Muse：Agent security 正式進入 OS / capability-security 思維。**

這可能比某個模型 benchmark 再高 5% 更具有長期影響。

**② OpenAI Navier–Stokes：AI research scaling 開始超越 single-agent reasoning。**

10,000-agent orchestration + 130B output tokens + Lean verification，可能是未來 automated science 的重要雛形。([OpenAI](https://openai.com/index/navier-stokes-solution/ "On the Navier–Stokes Millennium Prize Problem | OpenAI"))

**③ Claude distillation 爭議：AI supply chain 開始出現「model provenance」問題。**

你以為自己使用哪個模型、資料實際送到哪裡、output 又被拿去訓練誰，可能會成為新的 governance layer。

---

### 本期技術上最重要

我會選 **Navier–Stokes multi-agent research system**。

不是單純因為題目有名，而是它展示：

> **inference compute 可以被組織成 research infrastructure。**

這跟單純提高 context window 或 benchmark 是不同層次的變化。

---

### AI × Law 最值得追的一條

如果是做研究，我會特別注意 **Agent authorization / consent architecture** 與 **silent model routing / provenance**。

前者可以研究：

**legal consent → technical permission**

後者可以研究：

**technical routing → legal disclosure / data governance**

這兩條都具有非常漂亮的「技術問題 ↔ 法律問題」接口，而不是泛泛而談 AI ethics。

---

### 最需要保留的懷疑

是「**AI 已經解決 Navier–Stokes Millennium Problem**」這句新聞式表述。

目前已有非常強的證據支持這是一項重大數學成果，而且 Clay 也公開表示問題看來已獲解決；但正式 review、credit 與 mathematical significance 仍需要時間確認。([Clay Mathematics Institute](https://www.claymath.org/news/navier-stokes-announcement/ "Navier-Stokes Announcement - Clay Mathematics Institute"))

---

## 與之前 The Batch 的連續脈絡

這不是突然出現的三件孤立事件。

今年 5 月 The Batch 已經開始密集談 **agent cybersecurity**；6 月 5 日又專門討論中國 AI labs 使用的 **gray-market LLM access / proxy ecosystem**。這一期其實是那些問題進一步成熟後的版本：從「Agent 有安全風險」進入具體 security architecture，從「gray market 存在」進入大規模 distillation 與 user-data routing 的證據爭議。([DeepLearning.ai](https://www.deeplearning.ai/the-batch?utm_source=chatgpt.com "The Batch | DeepLearning.AI | AI News & Insights"))

所以目前 The Batch 裡很清楚的一條 2026 主線可以寫成：

**Capability → Agency → Access → Governance**

這條線非常值得繼續追。

---

# English terminology supplement

這次只挑和本期最值得累積的 10 個。

|Expression|Meaning / nuance|Example|中文與用法|
|---|---|---|---|
|**defense in depth**|多個獨立安全層，單一層失效不致全面失守|_Agent security requires defense in depth rather than reliance on model alignment alone._|**縱深防禦／多層防禦**；資安正式用語|
|**least-privilege access**|僅取得完成任務所必需的最低權限|_Agents should operate under least-privilege access by default._|**最小權限存取**|
|**human-in-the-loop approval**|關鍵動作仍需人工批准|_High-impact transactions should require human-in-the-loop approval._|**人在迴路核准**；AI governance 常見|
|**bound the impact**|即使事故發生，也限制傷害程度|_The architecture is designed to bound the impact of a successful attack._|**限制影響範圍**；比 prevent 更細膩|
|**mandatory mediation**|所有敏感操作必須經過某個不可繞過的控制點|_Network requests are subject to mandatory mediation by the permission layer._|**強制仲介／強制中介控制**；security architecture|
|**illicit distillation**|Anthropic 用來描述未授權、大規模能力抽取的詞|_Anthropic characterized the activity as illicit distillation._|**未授權／違規模型蒸餾**；注意 _illicit_ 帶有評價性，報導時最好 attribution|
|**gray-market access**|透過非正式或規避官方限制的途徑取得服務|_Some developers obtained gray-market access to frontier models._|**灰色市場存取管道**|
|**exfiltrate reasoning traces**|未授權把推理紀錄帶離系統|_The attackers allegedly attempted to exfiltrate reasoning traces._|**外洩／竊取推理軌跡**；資安 register|
|**independent verification**|由非原始提出者進行獨立確認|_The mathematical claim still requires independent verification._|**獨立驗證**；研究與監管都很好用|
|**evidentiary trail**|可供事後證明事件經過的一連串紀錄|_Model provenance could provide an evidentiary trail in priority disputes._|**證據軌跡／證據鏈**；law + audit 很實用|

## Retrieval practice

先不要往下看答案。

1. 「縱深防禦」英文怎麼說？
    
2. 「只讓 AI 取得完成工作所需要的最低權限」可以用哪個片語？
    
3. 關鍵交易必須由人確認：`_____ approval`。
    
4. 「不是完全防止攻擊，而是限制成功攻擊造成的損害」可以用哪個片語？
    
5. 所有敏感操作都必須通過一個不可繞過的控制點，稱為什麼？
    
6. Anthropic 用什麼詞描述未經授權、大規模抽取另一模型能力？
    
7. 「獨立驗證研究結果」英文怎麼說？
    
8. 能夠在日後證明資料、模型與事件來源的「證據軌跡」怎麼說？
    

---

**Answer key**

1. **defense in depth**
    
2. **least-privilege access**
    
3. **human-in-the-loop approval**
    
4. **bound the impact**
    
5. **mandatory mediation**
    
6. **illicit distillation**
    
7. **independent verification**
    
8. **evidentiary trail**