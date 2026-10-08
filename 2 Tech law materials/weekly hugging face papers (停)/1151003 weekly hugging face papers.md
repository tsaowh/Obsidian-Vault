
本週 Hugging Face Papers 的週榜範圍是 **2026/9/27–10/3**；截至本次檢查，Daily Papers 最新已發布的一批是 **10/2**。我以 weekly feed 作為全量候選母集合，再針對可能進 A 的論文核對摘要與主要結果。以下只呈現我判定為 **A — Deep Dive** 的論文，不列 B／C／Skip。論文中的數字皆是作者報告結果，不能直接視為已經被獨立 replication。[Hugging Face](https://huggingface.co/papers/week/2026-W40)

## 1. Context Language Models

[Context Language Models — Hugging Face Papers](https://huggingface.co/papers/2609.37725)

**白話一句話：** 以前是外部程式幫 Agent 決定「哪些東西塞進 context」；這篇乾脆讓模型自己管理自己的 context，想留下、刪掉、改寫什麼都由模型決定。

它把 context 當成一個模型可直接修改的 **file**。這不是單純做更好的 summarization，而是把原本屬於 harness 的 context management 搬進模型自己的行為能力。作者報告，在 BrowseComp-Plus 上 accuracy 提高 11.4%，同時 FLOPs 少 21.5%；12 小時 EdgeBench 也在更少計算量下提高表現。再用 online RL 訓練 Qwen3.5-9B 後，BrowseComp-Plus 表現提高 47.6%，FLOPs 反而少 12%。[Hugging Face](https://huggingface.co/papers/2609.37725)

**我怎麼看：這是實質 architecture shift，不只是 benchmark trick。** 長時間 Agent 的瓶頸逐漸不是 context window 能塞幾百萬 token，而是「模型能不能自己判斷哪些資訊值得持續保留」。CLM 把 memory/context management 從人工規則變成 learned policy。

法律治理有一條真正值得注意的鏈：

**模型自行修改 context → 未來行動取決於一個會變動的內部工作狀態 → 原始 prompt 與最終 output 不再足以重建決策過程 → audit / recordkeeping / authorization 需要考慮 mutable agent state。**

這不是現在就有一條「CLM 法」，但對長時間公共行政、金融、醫療或其他需可稽核 Agent，是 **Emerging** 問題。

**AI × Law：值得追。** 很好的問題是：未來 audit trail 是否必須保存「模型如何修改自己的工作 context」，而不是只保存 user prompt 與 final answer？

**閱讀建議：全文。** 這是本週我最推薦讀的技術論文之一。

---

## 2. Post-Training Leaves Behavioral Shadows on Unrelated Decisions

[Post-Training Leaves Behavioral Shadows on Unrelated Decisions — Hugging Face Papers](https://huggingface.co/papers/2609.29233)

**白話一句話：**一個學會寫程式的模型，竟然可以只回答大量「完全不像程式問題的一個普通單字」，就把一部分程式能力偷偷傳給另一個模型。

方法叫 **Active Taskless Distillation（ATD）**。研究者挑出 teacher 與共同 ancestor 對兩個普通單字幾乎猶豫不決的 prompt，讓 teacher 每題只選一個字；student 只學這些 prompt-word pairs，看不到 coding examples、teacher logits 或 teacher parameters。作者在 Qwen2.5-1.5B 的 coding 實驗中，只用 5,664 個這類單字回答，就比 nuisance-matched control 在 HumanEval+ 高 **5.34 percentage points**，並報告在科學知識、commonsense、reading comprehension 也看到 transfer。[Hugging Face](https://huggingface.co/papers/2609.29233)

這很重要的原因不是「5.34 分很多」，而是它挑戰一個直覺：

> post-training 得到的能力，不一定只存在於與該任務明顯相關的 output 裡。

模型更新後可能在大量看似無關的 decision probabilities 中留下 **behavioral shadow**。

但我不會把它講成「現在用幾千個單字就能偷 GPT-6」。目前仍是特定 experimental setup、小模型、有共同 ancestor 等條件下的結果。**這是很強的新現象，不等於成熟的 production model-extraction attack。**

法律／治理鏈條倒是很清楚：

**post-training capability 留下分散的 behavioral trace → 無關輸出也可能傳遞能力資訊 → 傳統只檢查敏感 task output 的防洩漏策略可能不足 → model provenance、distillation、trade secret / IP 與 capability leakage 出現新的技術問題。**

成熟度：**Emerging**。

**AI × Law：非常值得追。** 尤其「behavioral evidence 能不能證明某模型受另一模型影響」會同時碰到技術 provenance 與法律上的證明問題。

**閱讀建議：全文。Must Read。**

---

## 3. Raven: The Harness of Harnesses for Composable Agentic Intelligence

[Raven — Hugging Face Papers](https://huggingface.co/papers/2609.33439)

**白話一句話：**不是做一個超級 Agent 包辦所有事，而是讓系統自己打造不同專門 Agent 的工具組，再決定什麼時候叫誰來做、怎麼合作，而且做過的經驗可以留下來。

Raven 將 **model + harness** 視為可組合的 intelligence unit。Host Agent 會拆解任務、選擇 subagent、安排依賴關係，再整合結果；archive 保存跨任務經驗，Skill Forge 把成功經驗變成可重用 procedure。更關鍵的是 harness 本身可以被自動建構與演化，而非全部由人預先手寫。作者報告在複雜 long-horizon tasks 上優於比較的 agent systems。[Hugging Face](https://huggingface.co/papers/2609.33439)

所以這不是「LLM 本身突然更聰明」，而是 **system-level capability gain**：同樣的模型因為 orchestration、memory、specialization 與 reusable skills 組合起來，能完成單一 Agent 不容易完成的工作。

真正的治理問題也在 system level：

**harness 可動態改變 → 實際能力與可用工具不再只由 base model 決定 → 執行中的 system boundary、permission boundary 會變動 → accountability 與 audit 對象不能只寫成「某模型」。**

成熟度：**Emerging**。

**AI × Law：值得追，但焦點應該是「動態 Agent system 的責任與權限邊界」，而不是泛泛談 AI accountability。**

**閱讀建議：selected sections**，重點看 harness evolution、composition architecture 與 evaluation。

---

## 4. False Frontiers: Diagnosing and Mitigating Co-Cheating in Self-Evolving Search Agents

[False Frontiers — Hugging Face Papers](https://huggingface.co/papers/2609.39102)

**白話一句話：**Agent 自己出題、自己解題、自己拿答案當訓練資料時，可能兩邊一起學錯，最後分數越來越漂亮，但其實沒有真的變強。

這篇叫這個現象 **co-cheating**。在 self-evolving search agent 中，proposer 產生問題，solver 解題；如果兩者共享同樣的錯誤來源，它們可能愈來愈同意彼此的錯答案。研究者發現 internal reward 上升時，external correctness 可以停滯甚至下降。[Hugging Face](https://huggingface.co/papers/2609.39102)

他們提出 **CrossFit**：把 source documents 分組，A 組出的問題讓只在 B 組資料上訓練的 auxiliary solver 評，反之亦然，刻意切斷同一份 evidence 同時污染出題與評分的路徑。作者報告 false-agreement mass 明顯下降，七個 search benchmarks 的平均表現也優於原本 coupled self-evolution。[Hugging Face](https://huggingface.co/papers/2609.39102)

這不是「又一篇讓 Agent 更強的 paper」，反而是本週非常重要的 **negative / reliability signal**：

> self-improvement 的分數上升，不代表能力真的上升。

治理鏈很直接：

**AI 自己產生 training/evaluation evidence → generator 與 evaluator 共享錯誤 → internal metric 可系統性高估能力 → 依賴模型自評的 certification / audit 可能失真。**

成熟度：**Emerging**。

這很適合轉成 AI governance 研究：**self-evolving AI 的 evaluator independence 要做到什麼程度，才能算可信的 assurance？**

**閱讀建議：全文。Must Read。**

---

## 5. Marathoner: Ultra-Long-Horizon Autonomous Intelligence

[Marathoner — Hugging Face Papers](https://huggingface.co/papers/2609.34378)

**白話一句話：**它不是教 Agent 一次做得更聰明，而是在訓練 Agent「不要做兩小時就崩掉」，讓它能持續工作十幾小時、執行上千次工具操作。

研究者利用包含 **1,000+ lines 新程式碼的大型 GitHub release PR** 合成 long-horizon tasks，再用 Multi-Task Chaining 把多個任務串成更長工作；之後以 teacher trajectories 做 rejection-sampling finetuning，再讓模型在 sandbox 中用 RL 真正執行。另加入 **Later Stage Bonus Reward**，避免模型前半段工作、後半段開始失去方向。作者報告模型可持續 **10+ 小時、1,000+ tool calls**。[Hugging Face](https://huggingface.co/papers/2609.34378)

這是明顯的 capability signal，但名稱 **Ultra-Long-Horizon Autonomous Intelligence** 我會稍微降溫看待。實驗證明的是長時間 software/agent execution 能力大幅延伸，不是證明模型已經可以自主處理「幾個月的人類專案」。

治理意義要等真正 deployment：

**執行時間從幾分鐘延長到十小時以上 → Agent 可以在沒有逐步人工確認下累積大量 actions → 使用者最初的授權可能與後期情境脫節 → checkpoint、revocation、delegation scope 與 human oversight 變得更實際。**

成熟度：**Emerging**。

**AI × Law：有價值，但不要現在就擴張成「自主 AI 法人格」之類的大題。比較實際的是 long-horizon delegation / authorization。**

**閱讀建議：selected sections。**

---

## 6. What Makes World Action Models Generalize? / Simple-WAM

[What Makes World Action Models Generalize? — Hugging Face Papers](https://huggingface.co/papers/2609.34981)

**白話一句話：**機器人不一定真的要把「未來影片」完整生成出來；但它在決定動作前，似乎需要先形成一個「接下來大概會怎樣」的內部 future representation。

World Action Model（WAM）的一個爭論是：模型訓練時學預測未來畫面，那 inference 時是不是也必須花很多算力把未來影片生成出來？

這篇做 matched comparisons 後發現，把 future representation 完全拿掉的 latent WAM，在 in-distribution 看起來沒問題，但 environmental perturbation、data efficiency、task generalization 都會掉。更有意思的是，大部分好處似乎在 **第一個 denoising step** 就出現了，不需要真的把未來畫面完整 render 完。於是作者做 Simple-WAM，只做一次 predictive preparation，就得到接近 latent WAM 的效率，同時保留更好的 generalization。[Hugging Face](https://huggingface.co/papers/2609.34981)

這是很漂亮的 mechanistic finding：

> **「預想未來」有用，不等於「必須把未來完整生成出來」。**

技術上值得 A，因為它幫我們更具體理解 world model 為何能改善 robotic policy generalization，而不只是多堆一個 expensive video generator。

**法律／治理：目前沒有明顯意義，主要是技術訊號。** 如果未來 WAM 成為大規模自主機器人核心，再談 product safety 或 accountability 才合理。

**目前不值得延伸成 AI × Law 題目。**

**閱讀建議：selected sections**，尤其 controlled comparison 與 ablation。

---

## 7. Controlled Decoding Attacks on Black-Box LLMs

[Controlled Decoding Attacks on Black-Box LLMs — Hugging Face Papers](https://huggingface.co/papers/2609.36956)

**白話一句話：**就算 API 不給你 logits、也不給 model weights，只要允許你重複抽樣同一位置並從指定 assistant prefix 繼續生成，攻擊者仍可能從「大量文字答案」估出足夠的機率資訊來操控 generation。

過去 controlled decoding attack 常需要直接知道 next-token probability。這篇改成從 repeated samples 重建局部 distribution，而且不在每個 token 都做昂貴估計，而是用 **Risk-Gated Residual Control** 只在疑似關鍵位置介入，再用 speculative execution 降低 query cost。作者在四個 endpoints、三個 benchmarks 上報告相對 baseline 的強結果。[Hugging Face](https://huggingface.co/papers/2609.36956)

這是實質 security signal，但 caveat 很重要：

> 它不是「所有純文字 chatbot 都可以這樣 jailbreak」。

攻擊需要 endpoint 提供 **repeated sampling + assistant-prefix continuation** 等特定 affordances。安全意義在於：**不提供 logits 並不自然等於沒有 distribution-level attack surface。**

治理鏈：

**black-box interface 暴露可重複 sampling capability → 攻擊者統計重建局部 behavior → safety layer 可被適應性繞過 → API design、abuse controls 與 safety evaluation 不能只看「有沒有公開 logits」。**

成熟度：**Current**，至少在具有這類 API affordance 的服務上。

**AI × Law：有研究價值，但比較偏 cybersecurity / safety assurance，而不是一般 AI regulation。**

**閱讀建議：selected attack model + threat assumptions。** 不先看 threat model 很容易把結果講過頭。

---

## 8. KaliBench

[KaliBench — Hugging Face Papers](https://huggingface.co/papers/2610.02206)

**白話一句話：**現在模型知道很多資安知識，但真正叫它正確打出 Kali Linux 指令還是常出錯；不過只要有可以自動驗證對錯的 reward，小模型可以被很快訓練成相當強的 cyber tool user。

KaliBench 有 **8,504 組 natural-language → CLI command**、涵蓋 **1,642 個 Kali tools、23 種 capabilities、5 個 security phases**。在 unrestricted setting 中，作者測的 open-weight models 沒有一個超過 42% exact-command accuracy；但利用 deterministic/verifiable rewards 做 SFT + RL 後，**8B model 在這項 benchmark 上可達到與 685B MoE 相近的表現**。[Hugging Face](https://huggingface.co/papers/2610.02206)

這裡一定要避免一個錯誤解讀：

> **不是「8B 模型已經跟 685B 模型一樣會駭客攻擊」。**

它證明的是一項更窄但仍重要的能力：**正確選 tool 並產生 executable CLI arguments** 可以透過 verifiable feedback 被有效壓進小模型。

這是一個實際的 capability diffusion signal：

**cyber tool use 可精確自動驗證 → RLVR 成本降低 → 小型／本地模型也可能快速得到可靠 CLI execution capability → cyber capability evaluation、release / fine-tuning risk assessment 的模型大小假設變得更不可靠。**

成熟度：**Emerging**。

**AI × Law／治理：值得追，尤其 cybersecurity governance；但目前還不能從這篇直接推論 autonomous offensive capability。**

**閱讀建議：selected sections**，重點是 benchmark construction、RLVR 與 8B/685B 的比較條件。

---

# 本週最重要的 Technical Signals

**1. Agent 正從「長 context 的聊天模型」變成「會維護自己狀態、重組工具、長時間工作的系統」。** Context Language Models 讓模型直接管理 context；Raven 讓 harness 本身可組合、演化；Marathoner 把 execution horizon 推到 10+ 小時與 1,000+ tool calls。三篇放在一起，比任何一篇單獨的 benchmark improvement 更重要。[Hugging Face](https://huggingface.co/papers/2609.37725)

**2. Post-training 的能力可能比我們想像中更「分散」在模型行為裡。** Behavioral Shadows 顯示，task-specific update 可以出現在看似無關的微小 decision 中，甚至讓另一模型吸收到部分能力。這如果後續能在更大型、不同 genealogy 的模型上重現，會是很重要的 model provenance / capability-transfer 訊號。[Hugging Face](https://huggingface.co/papers/2609.29233)

**3. Feedback 正同時成為能力加速器與最大風險來源。** KaliBench 顯示可驗證 reward 能把非常具體的 cyber tool skill 有效教給小模型；False Frontiers 則反過來顯示，如果 feedback loop 本身不獨立，self-improvement 可以只把錯誤愈練愈一致。也就是：**有可靠 verifier，能力可以很快長；沒有可靠 verifier，分數也可以很快長，但能力沒有。** [Hugging Face](https://huggingface.co/papers/2610.02206)

# 最重要的 AI × Law Signals

**1.「模型版本」可能不再是足夠的治理單位。** 如果 Agent 可以自己修改 context、組裝 harness、累積 skills，並連續執行十幾個小時，那同一 base model 在不同時間點實際運作的 system state 可能差很多。真正值得研究的是 **mutable agent state、dynamic authorization 與 audit trail**，不是再泛泛增加一個「AI 要透明」原則。[Hugging Face](https://huggingface.co/papers/2609.37725)

**2. Behavioral provenance 可能成為新的技術—法律交界。** 如果後訓練能力會在無關行為留下統計痕跡，就有可能發展成模型來源、distillation 或 capability transfer 的 forensic evidence。但目前實驗條件離「法庭上證明模型抄襲」還很遠，這正是研究空間，而不是已經有答案。[Hugging Face](https://huggingface.co/papers/2609.29233)

**3. Cybersecurity governance 不能只看模型大小或 API 是否公開 logits。** 一邊是小模型可以透過 verifiable RL 顯著提高 Kali CLI competence；另一邊是 text-only black-box API 在特定 interface affordances 下仍可能被 distribution reconstruction attack 利用。兩者共同指向的是 **capability-based testing，而不是 architecture-based assumptions**。[Hugging Face](https://huggingface.co/papers/2610.02206)

# Emerging Cross-Paper Patterns

本週最明顯的 pattern 是 **Agent architecture 從「外部 scaffold 控模型」往「模型與 scaffold 共同演化」移動**。Context management、memory、skills、harness selection、long-horizon execution 以前大多是外部程式設計問題，現在愈來愈多工作把它們變成 learned behavior。Context LM、Raven、Marathoner 是三個非常不同但方向一致的例子。[Hugging Face](https://huggingface.co/papers/2609.37725)

第二個 pattern 是 **self-improvement 研究開始從「能不能自我改善」轉向「改善訊號到底可信不可信」**。False Frontiers 的價值就在這裡：如果 evaluator 與 learner 共用同一套錯誤來源，整套閉環可能在沒有真實 progress 的情況下自我強化。這對所有 automated curriculum、self-training、self-evolving agent 都是重要警告。[Hugging Face](https://huggingface.co/papers/2609.39102)

第三個 pattern 是 **不一定需要把中間世界完整生成出來，重點可能是保留 task-relevant internal state**。Simple-WAM 顯示機器人只需要非常早期的 future representation 就能取得不少 generalization benefit；Context LM 則直接讓模型維護 task-relevant context state。兩者領域不同，但都在往「保留對後續決策真正有用的 state，而不是暴力保存／生成所有資訊」靠近。[Hugging Face](https://huggingface.co/papers/2609.34981)

# Research Radar

**① Mutable Agent State 應不應成為法定／制度化的 audit object？ — Emerging**

技術動機是 Context LM + Raven + Marathoner：Agent 的有效狀態逐漸包含自行修改的 context、動態 harness、累積 skills 與數百次先前 actions。真正的問題不是「要不要 explainable AI」，而是：**在需要事後究責的系統中，到底哪些 runtime state 必須留下，才有辦法重建 Agent 當時為什麼具有某個權限、知識與目標？** [Hugging Face](https://huggingface.co/papers/2609.37725)

**② Behavioral Shadows 能否成為 model provenance / distillation 的證據方法？ — Emerging（法律應用仍偏 speculative）**

技術動機是 ATD：能力更新可能透過與任務無關的行為被統計辨識甚至傳遞。研究可以進一步問：這些 trace 是否具有模型／post-training procedure 的可識別性？false-positive rate 多高？不同 ancestor 是否還存在？只有先解完這些技術問題，才有資格談 IP、trade secret 或 evidence。[Hugging Face](https://huggingface.co/papers/2609.29233)

**③ 高風險 AI capability 的管制／assurance 是否應測「post-training 可達能力」，而非只測 release-time base model？ — Emerging**

KaliBench 最值得注意的不是 benchmark 本身，而是 8B 模型經過可驗證 reward 訓練後，在特定 cyber CLI 任務上的能力可以逼近大型 MoE。若這類現象普遍存在，release evaluation 只測原始 checkpoint 可能低估「使用者很容易 fine-tune 出來的能力」。這是一個比單純用 parameter count 分級更扎實的治理問題。[Hugging Face](https://huggingface.co/papers/2610.02206)

# What I Should Read This Week

**Must Read 1 — Context Language Models。** 因為它可能是比「再把 context window 拉長」更根本的 agent architecture 方向：讓模型學會管理自己的 working state。[Hugging Face](https://huggingface.co/papers/2609.37725)

**Must Read 2 — Post-Training Leaves Behavioral Shadows on Unrelated Decisions。** 它提出的是一個不太符合直覺、但若能擴展會很有後續研究價值的現象，尤其值得從 provenance、distillation 與模型能力形成機制繼續追。[Hugging Face](https://huggingface.co/papers/2609.29233)

**Must Read 3 — False Frontiers。** 近期很多 paper 都在講 self-improving / self-evolving agents，這篇反而告訴你「它們什麼時候只是把自己的錯誤變得更一致」。對判斷未來 self-improvement paper 有沒有 hype，很有用。[Hugging Face](https://huggingface.co/papers/2609.39102)

**Track：** ultra-long-horizon execution、self-evolving/composable harness、black-box decoding attacks、small-model cyber tool specialization，以及 world-action models 的 test-time future representation。這幾條線都值得繼續看，但目前沒有必要把每一篇相關 paper 都全文讀完。