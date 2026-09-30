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

> ==**不要假設 Agent 不會被攻擊，而是假設它遲早會被攻擊，再限制攻擊成功後能造成的傷害。**==

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

==note start==

Muse 的安全核心不是要求 Agent 自己「不要犯錯」，而是用多層系統邊界限制它：先把 Agent 關在受限執行環境中，再讓它拿不到真正憑證，最後所有對外連線與敏感操作都必須經過獨立守門機制批准。換句話說，就是同時限制 **Agent 能碰什麼、能拿什麼、能送出去什麼**，讓單一防線失效時仍有其他層保護。

- **Runtime isolation（執行環境隔離）**：把 Agent 放在 VM / container 中，限制 syscall、kernel capability 與 network access。
- **Credential separation（憑證分離）**：真正的 OAuth token、API key 不交給 Agent。
- **Surrogate token（替代憑證）**：Agent 持有的代理性 token，不等於真正帳號憑證。
- **Just-in-time credential insertion（即時憑證注入）**：操作通過審核後，系統才在最後一刻加入真正憑證。
- **Sentinel（獨立權限守門機制）**：負責批准或拒絕 network egress 與 connector action。
- **Network egress（對外連線）**：系統向外部網站、API 或服務送出資料的行為。
- **eBPF**：Linux 核心層的可程式化監控技術，可追蹤 process 與資料流。
- **Tainted（已接觸敏感資料）**：某個 process 讀過敏感資料後被標記，後續對外傳輸會受到更嚴格限制。
- **Least privilege（最小權限）**：只給完成任務所需的最低權限。
- **Mandatory mediation（強制中介）**：敏感操作必須經過獨立控制層審查。
- **Capability security（能力式安全）**：限制一個主體實際被允許執行哪些操作。
- **Defense in depth（縱深防禦）**：用多層防線避免單點失效造成全面失守。

==note end==

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

==note start==

Muse 對 **Human-in-the-loop** 的改進，在於把「人的同意」從單純的 UI 確認，變成系統真正會強制執行的權限。使用者核准一次特定操作，例如「這次支付 50 美元」，並不等於永久授權；權限可以被限制在單次、單一 session、特定任務或特定時間範圍，也能綁定 connector、destination 與 use case。對 AI governance 而言，真正重要的是：**consent 不再只是文字表示，而是可以被轉譯成 machine-enforceable authorization，也就是可由系統強制落實的授權。**

- **Human-in-the-loop**：人在 AI 執行關鍵操作前保留確認、批准或介入權。
- **Strict capability**：嚴格能力授權；只給 AI 明確、有限、不可任意擴張的操作權限。
- **One-time permission**：單次授權，只能使用一次。
- **Session-scoped**：僅在目前這次工作階段內有效。
- **Task-scoped**：僅限特定任務使用。
- **Time-bounded**：授權只在限定時間內有效。
- **Connector**：AI 用來連接外部服務或系統的介面。
- **Destination**：操作的目標，例如特定網站、帳戶或 API。
- **Use case**：授權被允許使用的特定用途。
- **Machine-enforceable authorization（可由系統強制落實的授權）**：把使用者授權轉換成系統可直接檢查並強制遵守的權限限制，例如限定可操作的資源、行為、金額、對象、次數或時間；Agent 即使想超出範圍，也會被技術機制阻擋，而不是只靠模型自行遵守。

==note end==

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

==note start==

**Agent 安全的核心，不是把模型訓練到「永遠不犯錯」，而是即使模型犯錯，也不能直接造成真實世界的高風險行動。** 因此真正重要的是把 LLM 當成**不可信元件**，由外部系統控制它「能做什麼、能碰哪些資源、在什麼條件下能做、權限多久有效、行為能否追蹤」。這正是傳統資訊安全的思路，也就是 **LLM 不應成為 trusted computing base（可信運算基礎）**。

==note end==

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

==note start==

這起 Muse 漏洞的精髓是：**雲端 Agent 本身即使隔離得很好，整體 Agent 系統仍可能因 client 端或其他周邊元件出現漏洞而失守。** 這次問題出在 macOS client 的本機設定可被修改，使原本應送往合法服務的 transcription request 可能被重新導向，而不是攻破 Meta 的 Secure VM。它提醒我們：Agent security 的安全範圍不能只看 LLM 或 Agent VM，而必須看整個 **attack surface（攻擊面）**，包括 **user、client、agent runtime、tools/connectors、credentials、network 與 third-party services**。這些元件構成一條 **trust chain（信任鏈）**；其中任何一個具有關鍵權限的環節被破壞，都可能削弱其他安全措施。

- **Local code execution（本機程式碼執行能力）**：攻擊者已能在受害者的電腦上執行程式；這次漏洞需要這項前提，因此不能理解成一般網路攻擊者可直接從遠端利用。
- **Secure VM**：Meta 在雲端用來隔離 Agent 執行環境的受控虛擬機；這次漏洞並不是突破這一層。
- **Agent sandbox**：限制 Agent 可存取的系統資源、權限與網路行為的隔離環境；Secure VM 可以是 sandbox 架構的一部分，而兩者並非完全同義。
- **Connector**：讓 Agent 代表使用者存取外部服務的介面，例如 Email、雲端硬碟或其他 API。
- **Credential（憑證）**：用來證明身分或取得服務權限的資訊，例如 OAuth token、API key；一旦遭竊取，攻擊者可能冒用使用者權限。
- **Trust chain（信任鏈）**：整個 Agent 系統中一連串必須被信任的元件；安全性取決於的不只是模型，而是這些元件共同形成的整體。
- **Attack surface（攻擊面）**：所有可能成為攻擊入口的元件與介面，包括 client、設定檔、browser、connector、credential、network、update mechanism 等。
- **Update mechanism（更新機制）**：軟體取得、驗證與安裝更新的流程；若本身被攻破，也可能成為供應鏈式攻擊入口。

最重要的觀念：Secure VM 安全 ≠ Agent 系統安全。Agent security 必須保護的是整個 attack surface 與 trust chain，而不是只把 LLM 關進 sandbox。

==note end==

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


==note start==

這段真正有研究價值的地方，是把法律上的「使用者有沒有同意」，進一步轉成「系統是否把使用者同意的範圍，落實為技術上可被強制遵守的權限邊界」。對高自主 Agent 而言，單純取得 **informed consent（知情同意）** 可能已不夠，還需要 **machine-enforceable consent（可由系統強制落實的同意）**：例如使用者同意 Agent 讀取 Gmail，不代表也同意它寄信、修改 forwarding rule，或存取所有信件。這會把 AI governance 從抽象的「有沒有告知、使用者有沒有按同意」，推進到更具體的 **technical architecture → legal duty（技術架構如何影響法律上的注意義務）**；同時，如果事故不是模型本身造成，而是 client、connector、credential、OS 或其他元件失效，就會進一步產生 **system-level AI liability（系統層級的 AI 責任分配）**問題。

==note end==

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

==note start==

OpenAI 這次真正重要的，不只是「AI 解了一道很難的數學題」，而是展示了一種新的研究模式：**不是靠單一模型一次想出答案，而是讓數千個 Agent 同時探索不同方向、交換中間成果、淘汰失敗路線，再把有希望的思路整合起來。** 這更像一個由 AI 組成的「計算型研究組織」，最後再把人類可讀的證明轉成 Lean 形式化證明。核心突破因此不只是 model intelligence，而是 **massively parallel research + coordination + formal verification**。

- **Multi-agent system（多代理系統）**：由多個 AI Agent 分工、互動與協作完成同一目標的系統。
- **Concurrent agents（並行 Agent）**：大量 Agent 同時工作，而不是一個做完再換下一個。
- **Cross-pollination（思路交叉傳遞）**：把某個 Agent 找到的有價值想法傳給其他 Agent，讓不同研究路線互相借用成果。
- **Consolidation（成果整合）**：把大量 Agent 產生的零散結果整理、比較並合併成較完整的研究方向。
- **Formalize（形式化）**：把一般數學證明轉換成可由電腦逐步驗證的嚴格形式。
- **Lean**：形式化定理證明系統，用來檢查每一步推理是否符合邏輯規則。
- **Formal verification（形式驗證）**：不是只相信模型「看起來推得對」，而是讓證明經過機器逐步驗證。

==note end==

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


==note start==

這段的重點是：**AI 的進步不一定只靠把單一模型訓練得更聰明，也可以靠更好的「研究編排」來提升整體能力。** 也就是在模型訓練完成後，投入更多推論算力，讓 AI 同時嘗試很多研究方向，再讓多個 agent 分工、互相檢查、整合成果。過去 scaling 主要是「把模型本身做強」，現在則逐漸出現「把 AI 的研究流程做強」這條路；Navier–Stokes 事件若證據成立，可能就是這種 **agentic research scaling** 的重要示範。

- **Orchestration**：安排多個 AI 要怎麼分工、合作、檢查彼此的結果，最後再整合答案。
- **Base model IQ**：模型本身原有的理解、推理與解題能力。
- **Massive inference compute**：回答同一個難題時投入非常大量的算力，讓 AI 可以多想、多試、多比較。
- **Parallel search**：同時探索很多條不同的解題路徑，而不是只沿著一條路往下想。
- **Agent coordination**：讓多個 agent 分工合作，例如有人提出假設、有人驗證、有人找反例。
- **Training-time scaling**：在訓練階段增加資料、算力或模型規模，把模型本身訓練得更強。
- **Test-time / inference-time scaling**：模型訓練完成後，在實際解題時花更多算力，讓它反覆思考或嘗試更多解法。
- **Agentic research scaling**：增加 AI agent 的數量與研究分工，讓大量 agent 同時探索不同方向，再整合成研究成果。
- **Scaling**：增加某種資源，例如算力、模型大小或 agent 數量，藉此提升 AI 的整體能力。

==note end==

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


==note start==

這段的核心是：**Lean 可以幫你確認「證明的邏輯有沒有真的走通」，但不能替你確認「你證明的是不是對的問題、是不是重要的新發現」。** 它會把自然語言中容易被忽略的條件、隱藏假設與推導漏洞逼出來，因此非常適合檢查形式邏輯上的正確性；但它只能驗證你事先寫進去的定義與前提。如果 formalization 本身就錯了，Lean 仍可能忠實地證明一個「形式上正確、實際上答非所問」的命題。所以 **formal verification 是強力的邏輯檢查工具，peer review 則負責判斷問題設定、意義、新穎性與科學價值，兩者是互補關係。**

==note end==

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

==note start==

**OpenAI 的證明不是泛泛宣稱「解掉 Navier–Stokes」，而是針對 Clay 官方 formulation 裡的 C/D alternatives：證明一個一開始仍然平滑的流體，在存在平滑外力（smooth external force）的情況下，確實可能在有限時間內形成 singularity，也就是解失去原本的平滑性。** OpenAI 認為這已符合 Clay 官方題目的形式要求；而 Clay Mathematics Institute 的態度則非常審慎，只表示這個問題「**apparently been settled**」，也就是「看起來已經獲得解決」，但還沒有直接宣布正式結案。現在數學界仍需要進一步確認證明內容、問題 formulation 是否完全對應、成果的新穎性與優先權，以及最後 credit 應如何分配。因此最準確的說法是：**AI 已提出一份看起來符合 Clay 正式問題要求、而且可能真的解決問題的 formal proof，但數學共同體的正式驗證與最終認定程序仍在進行。**

==note end==

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

==note start==

**當 AI 公司同時提供研究工具、又自己做前沿研究時，傳統的「研究優先權」問題會變得更複雜。** 以前大家主要擔心未公開論文、猜想、證明思路或程式碼會不會被拿去訓練模型；未來更棘手的問題是：如果研究者把尚未公開的想法交給 AI research assistant，而同一家公司的內部研究團隊後來做出相似成果，要怎麼證明那是**獨立發現（independently derived）**，而不是受到使用者輸入的間接影響。OpenAI 對這次事件表示 Buckmaster 的 Codex prompts 不可能影響內部模型，但那目前仍是公司的內部調查結論。這件事因此把問題從單純的「資料有沒有被拿去 training」，推進到更深一層的 **research provenance、priority、conflict of interest 與可稽核性**。

==note end==

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

==note start==

**當 AI 公司同時替大量研究者提供研究助理，又自己參與前沿科研時，未來只靠「我們沒有使用你的資料」這種聲明，可能不足以建立信任。** 更合理的做法，是建立完整的 **research provenance infrastructure**，把模型版本、訓練資料政策、prompt 紀錄、agent 行動軌跡、時間戳、資料集來源與中間產物都留下來，形成可追溯的 **scientific audit trail**。這樣未來若出現相似研究成果，才能更有依據地判斷是否屬於 **independent discovery**、研究成果應如何歸屬，以及 AI-assisted research 應揭露到什麼程度。長期來看，這不只是 authorship 問題，而是關於 **證據、歸屬、研究可信度與 AI 介入科研後的治理規則**。

**Formal verification 普及後，peer review 很可能會從「幫你檢查每一步證明有沒有算錯」，轉向「判斷你證明的是不是對的問題、是不是重要、是不是新的，以及這個結果應該如何被理解」。** 換句話說，**機器逐漸接手 correctness，人類 reviewer 更集中處理 meaning、significance、novelty、assumptions 與 scientific judgment。** 所以 formal verification 不會取代 peer review，而是會把 peer review 往更高層次推。

- **Research provenance infrastructure**：研究來源追蹤基礎設施；用來記錄「這個研究成果是怎麼一步一步產生的」。
- **Provenance**：來源與歷程。簡單說就是「這個東西從哪裡來、經過哪些步驟才變成現在這樣」。
- **Model checkpoint identity**：實際使用的是哪一個模型版本，避免只寫「用了某某模型」卻不知道具體版本。
- **Model training cutoff**：模型訓練資料收錄到哪個時間點，可用來判斷模型理論上是否可能接觸過某些資訊。
- **Fine-tuning lineage**：模型後續微調的歷史，例如用了哪些資料、經過哪些版本修改。
- **User-data inclusion policy**：使用者輸入的資料會不會被拿去訓練、微調或其他用途的政策。
- **Prompt logs**：研究過程中研究者曾經對 AI 下過哪些指令與問題的紀錄。
- **Agent trajectories**：AI agent 完成任務時實際走過的步驟，例如搜尋了什麼、呼叫了什麼工具、做過哪些中間判斷。
- **Intermediate artifacts**：研究過程中的中間產物，例如草稿、程式碼、證明片段、實驗結果。
- **Dataset provenance**：資料集的來源、蒐集方式、版本與修改歷史。
- **Scientific audit trail**：科研稽核軌跡；讓第三方能事後檢查研究成果是怎麼形成的。
- **Independent discovery**：獨立發現；也就是兩邊在沒有互相取得對方未公開資訊的情況下，各自得到相似結果。
- **Evidentiary standard**：證據標準；要拿出多強、多完整的證據，才足以證明某件事。
- **Disclosure standard**：揭露標準；規定 AI-assisted research 至少要公開哪些模型、資料與研究過程資訊。
- **Epistemic governance**：知識治理；關心的是「知識是怎麼產生、怎麼驗證、誰有資格相信、證據是否足夠」。
- **Attribution**：成果歸屬；也就是研究貢獻最後應該算在誰身上。
- **AI-mediated research**：由 AI 深度介入、協助或中介的研究活動。

==note end==

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
==note start==

這件事表面上看是「AI 公司指控競爭對手用自己的模型輸出訓練其他模型」，但真正複雜的地方其實不只 **model distillation**。Anthropic 指稱多家中國 AI labs 大規模、未經授權地取得 Claude 輸出，甚至有部分 Kimi、DeepSeek 使用者的請求被轉送到 Claude，再把 Claude 的答案回傳給原使用者。如果屬實，問題就同時涉及 **模型輸出的使用權、服務條款、帳號與 API 規避、使用者是否被誤導、資料是否跨平台轉送，以及競爭對手能否利用另一家模型作為自己的後端服務或訓練來源**。不過目前這些仍主要是 Anthropic 的調查與歸因，並非法院已認定的法律事實，因此最值得關注的不是先判斷誰「抄了誰」，而是這類行為正在逼迫法律重新回答：**AI 模型的輸出究竟能不能被競爭者大規模利用、平台應揭露實際使用哪個模型到什麼程度，以及模型供應商對下游轉送與再利用應有多少控制權。**

==note end==


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

==note start==

這段最重要的是先把「蒸餾」和「違規蒸餾」分開看。**Distillation 本身是很正常的機器學習方法**：先讓能力較強、但成本較高的 teacher model 產生大量高品質答案，再拿這些答案去訓練較小、較便宜的 student model，希望把老師的一部分能力轉移過去。因此，**distillation 本身不等於非法抄襲**。Anthropic 所說的 **illicit distillation**，重點不在「用了蒸餾技術」本身，而在它指稱對方是以**大規模、隱蔽、未經授權**的方式取得模型輸出，並搭配假帳號、被竊 API key、proxy network 等手段規避限制。也就是說，爭議核心其實是**取得與使用這些模型輸出的方式是否被授權、是否規避平台限制**，而不是蒸餾技術本身有問題。

- **Distillation / Knowledge distillation**：知識蒸餾；讓較小的模型學習較大模型的答案或行為，把一部分能力「濃縮」到小模型裡。
- **Teacher model**：老師模型；能力較強、通常較大也較昂貴的模型，負責提供高品質示範。
- **Student model**：學生模型；拿老師產生的資料來訓練，希望用更低成本做到接近老師的效果。
- **Parameters**：模型參數；可以先理解成模型內部學到的「可調整數值」。參數越多，通常模型容量越大，但不代表一定比較聰明。
- **Capability transfer**：能力轉移；把 teacher 已經展現出的部分能力，透過訓練方式讓 student 也學會。
- **Illicit distillation**：Anthropic 用來描述「未經授權、規模化且刻意規避限制的蒸餾行為」的說法；不等於所有 distillation 都非法。
- **Industrial-scale**：工業規模；不是少量測試，而是大批量、自動化、長時間進行。
- **Covert**：隱蔽進行；刻意不讓平台或對方察覺真正用途。
- **Unauthorized extraction**：未經授權大量取得模型輸出。
- **Fraudulent accounts**：假帳號或以不實方式建立的帳號。
- **Stolen API keys**：遭竊取或未經允許使用的 API 金鑰。不是去 Anthropic 的機房「偷 Claude 金鑰」，而是去攻擊那些已經合法持有 Claude API key 的公司、使用者或第三方服務，再把他們的憑證拿來用。
- **Proxy networks**：代理網路；透過大量中介伺服器或轉送節點隱藏真正來源、分散流量或繞過限制。

==note end==

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

==note start==

**Anthropic 想保護的已經不只是資料，而是模型本身的能力。** Claude 不會直接把完整 hidden reasoning 交給使用者，而是用 **thinking signature** 這類受保護的方式延續推理；但 Anthropic 指稱 Moonshot 找到 **cross-session replay**，把前一個 session 的 signature 帶到新的 session，設法恢復較完整的 reasoning trace，再拿這些資料去做 supervised fine-tuning。如果這種做法成立，代表 AI 安全的威脅正在從傳統的 **data exfiltration（把資料偷出去）**，進一步變成 **capability exfiltration（把模型學會的解題能力、推理模式與行為抽取出去）**。真正被「帶走」的，不一定是原始資料，而可能是模型多年訓練累積出的能力。

hidden reasoning 保護的是「不要直接展示內部思考」；攻擊者嘗試做的是，把既有的 hidden reasoning 偽裝成「我要你處理的一段內容」，再讓模型把它輸出。不是 Session B 不隱藏 reasoning，而是攻擊者試圖把 Session A 的舊 reasoning，從「隱藏思考」轉成「普通輸出內容」。



- **Hidden reasoning**：模型內部真正的推理過程；通常不會完整直接顯示給使用者。
- **Thinking signature**：可以把它想成「某段內部推理的受保護識別憑證」，讓系統之後能延續那段思考，但不直接把完整內容公開。
- **Cross-session replay**：把前一個 session 留下的資訊或憑證，拿到另一個新的 session 重複使用。
- **Reasoning trace**：模型解題時的推理軌跡，也就是從問題一路走到答案的中間步驟。
- **Extraction**：抽取；設法把原本不直接提供的資訊、行為或能力取得出來。
- **Supervised fine-tuning（SFT）**：監督式微調；拿大量「輸入＋理想答案／解題示範」去繼續訓練模型。模型已經先 pretrain 過了，現在再用較小、較有目的性的標註資料去調整它。
	- **準備資料**  
		- 把很多「輸入 → 理想輸出」整理成 dataset。
	- **把文字轉成 token**  
		- 模型實際吃的不是中文字，而是一串 token ID。
	- **讓模型先回答一次**
		- 模型會對下一個 token 給出機率分布。
	- **比較模型答案和理想答案**  
		- 用 loss 計算：模型現在離正確答案有多遠？
	- **反向傳播（backpropagation）**  
		- 根據 loss 計算每個參數應該往哪個方向調整。
	- **更新模型參數**  
		- 用 gradient descent / AdamW 之類 optimizer，把 weights 稍微改一點。
	- **重複很多 batch / epoch**  
		- 讓模型逐漸更常產生你希望的答案形式與解題方式。
	- **Full fine-tuning**：整個模型參數都更新，效果強但很吃 GPU。
		- Full fine-tuning = 把老師整套思考方式重新改造。  
	- **LoRA / QLoRA**：不改全部參數，只額外訓練一小組低秩矩陣，用來修正原模型的行為，成本低很多，也是現在自己 fine-tune 開源 LLM 很常見的方法。
		- 把原本的大矩陣先凍結不動，只另外加上一小組可訓練矩陣（可拆成兩個很小的矩陣）。
		- LoRA = 不動老師原本能力，只另外教他一套「遇到法律問題時，請採用這種回答習慣」的補充規則。
- **Data exfiltration**：資料外洩／資料竊取；把機密文件、資料庫內容、帳號資訊等偷出去。
- **Capability exfiltration**：能力外流；不是偷原始資料，而是設法把模型的**推理方式、解題策略、工具使用能力或其他已訓練出的能力**抽取到另一個模型。
- **Capability**：模型能做什麼，例如推理、寫程式、規劃、使用工具、修正錯誤等能力。
- **Model security**：模型安全；不只保護資料，也包括防止模型被濫用、能力被抽取、行為被逆向工程。

==note end==

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
==note start==

這段真正嚴重的地方，不一定是「A 偷學 B」，而是 **使用者以為自己把資料交給 Model A，實際上內容卻被悄悄轉送給 Model B**。一旦發生這種 **silent model routing**，問題就會從單純的模型競爭，擴大成資料治理與法律責任：使用者的文件到底被誰處理、是否曾被告知、敏感資料是否跨平台傳送、產品標示是否具有誤導性，以及平台是否透過假帳號、被盜憑證或規避限制來取得另一家模型服務。也因此，這類事件不能只用「偷模型、侵害著作權」來概括，因為它其實同時涉及 **契約、隱私、消費者透明、營業秘密、不公平競爭與存取控制規避**等不同法律問題。

- **Silent model routing**：靜默模型轉送；使用者以為自己在用 Model A，但後台其實把問題轉給 Model B，而且沒有明確告知。
- **Prompt**：使用者輸入給 AI 的內容，包括問題、文件、程式碼、公司資料等。
- **Processor / recipient**：實際處理或接收資料的一方；法律上重點是使用者是否知道資料最後落到誰手上。
- **Privacy / data protection**：隱私與資料保護；關心資料被誰蒐集、使用、轉送，以及是否符合告知與合法性要求。
- **Consumer transparency**：消費者透明；產品實際怎麼運作，不能和使用者看到的標示差太多。
- **Misleading representation**：誤導性表示；例如標示成某模型，實際卻主要由另一個模型回答。
- **Trade secret**：營業秘密；企業具有商業價值、未公開且採取保密措施的資訊。
- **Unfair competition**：不公平競爭；用不當方式取得或利用競爭對手的技術、資源或市場優勢。
- **Model capability extraction**：模型能力抽取；大量取得另一個模型的輸出、推理模式或行為，再用來提升自己的模型。
- **Computer misuse**：電腦系統濫用；泛指未經授權或超出授權範圍使用系統。
- **Access-control circumvention**：規避存取控制；設法繞過帳號、地區、API 或其他使用限制。
- **Fake accounts**：假帳號；以虛假身分或大量帳號規避平台限制。
- **Stolen credentials**：被盜憑證；例如 API key、帳號密碼、session token 等被未經授權取得。
- **Geographic restrictions**：地理限制；平台只允許特定國家或地區使用，卻被技術手段繞過。
- **Terms of Service（ToS）**：服務條款；使用平台時同意遵守的規則。

==note end==

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

==note start==

**Anthropic 的報告可以當成重要證據來源，但不能直接當成中立、完整的事實版本。** 因為 Anthropic 本身就是事件當事人，外界又看不到它全部的內部 telemetry，所以它對「誰做了什麼」的 attribution 仍需要第三方驗證；而且即使真的有 **151 million exchanges**，也只能說明被指稱的抽取行為規模很大，不能直接證明競爭模型有多少能力是從 Claude 蒸餾而來。The Batch 也提醒，中國 AI labs 本身在架構、訓練與工程上有大量獨立創新。因此比較嚴謹的做法，是把兩件事分開：**一方面判斷是否存在未經授權的大規模 extraction，另一方面獨立分析 Qwen、DeepSeek、Kimi 等模型的能力究竟來自哪些技術來源。** 不能從「可能有違規抽取」直接跳成「模型能力主要是抄來的」。

- **Provider framing**：服務商自己的敘事方式；同一件事由不同當事人描述，重點與用詞可能不同。
- **Interested party / 利益關係人**：事件結果會直接影響自身利益的一方，因此其說法需要特別注意立場。
- **Attribution**：歸因；判斷某個行為到底是誰做的。
- **Telemetry**：系統內部運作紀錄，例如 API 呼叫、帳號行為、流量模式、IP、session、時間戳等技術資料。
- **Independent reproduction / verification**：第三方能不能用自己的方法與資料重現、驗證同樣的結論。
- **Exchange**：一次或一組模型互動；數量大代表使用規模大，但不等於每次互動都有同樣訓練價值。
- **Distillation contribution**：模型能力中有多少部分可以歸因於蒸餾；這通常很難直接量化。
- **Architecture innovation**：模型架構上的創新，例如 attention、MoE、routing 等設計改進。
- **Training innovation**：訓練方法上的創新，例如資料策略、loss 設計、RL、post-training 方法。
- **Engineering innovation**：工程上的創新，例如推論效率、分散式訓練、記憶體最佳化、部署技巧。
- **Unauthorized extraction**：未經授權的大規模取得另一個模型的輸出或能力。
- **Causal attribution**：因果歸因；不只是看到兩件事同時存在，而是要證明「A 真的造成了 B」。這裡最難的就是不能因為有大量 Claude exchanges，就直接推論競爭模型能力主要因此而來。

==note end==

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

==note start==

這組問題的研究價值很高，因為它已經不只是「AI 生成內容有沒有著作權」這種單一議題，而是直接碰到 **AI 服務背後到底是誰在處理資料、模型之間如何互相利用、使用者是否有被充分告知，以及訓練資料來源能不能被追溯**。例如，一個產品標示「Powered by Model A」，到底只是品牌名稱，還是代表實際推論也是由 Model A 完成？如果服務商偷偷把 prompt 轉送給另一家公司，資料保護法上的 controller、processor、subprocessor 又該怎麼分？另外，大量收集其他模型輸出，到底什麼時候只是正常 benchmark 或 interoperability，什麼時候才變成 unauthorized extraction？最後，如果 synthetic data 本身又是其他模型產生的，未來可能還需要建立 **training-data provenance**，追蹤資料究竟來自哪個 upstream model。這些問題同時連結 privacy、contract、competition、cybersecurity 與 AI governance，因此很適合發展成 CLSR 或 IJLIT 類型的研究題目。

- **Model-routing disclosure duty**：模型轉送揭露義務；如果服務實際把使用者請求交給其他模型處理，是否應明確告知使用者。
- **Powered by Model A**：表面上表示「由 Model A 驅動」，但法律上要進一步問：這代表品牌、主要模型，還是實際負責推論的模型。
- **Model-output harvesting**：大量蒐集另一個模型的輸出，例如自動送入大量問題並保存答案。
- **Interoperability**：互通性；不同系統、模型或平台能彼此合作、交換資料或共同運作。
- **Benchmarking**：基準測試；用同一批題目比較不同模型的能力與表現。
- **Unauthorized model extraction**：未經授權的模型能力抽取；透過大量輸出、reasoning 或其他方式，試圖把另一個模型的能力轉移出來。
- **Data controller**：決定「為什麼處理資料、怎麼處理資料」的一方。
- **Data processor**：依照 controller 指示實際處理資料的一方。
- **Subprocessor**：processor 又委託的下一層資料處理者。
- **Secretly forwards prompts**：沒有充分告知使用者，就把 prompt 轉送給另一家服務商處理。
- **Training-data provenance**：訓練資料來源追蹤；記錄訓練資料從哪裡來、經過哪些處理、是否又是其他模型生成。
- **Synthetic data**：人工或模型產生的資料，而不是直接從真實世界蒐集的原始資料。
- **Upstream model source**：這筆 synthetic data 最初是由哪個上游模型產生。
- **Competition law**：競爭法；關心企業是否透過不公平方式取得競爭優勢或限制市場競爭。

==note end==

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