# Dagrs 計畫模板

本文件供 `docs/plan/` 下的實作、API 調整、相容性、文件與發布計畫使用。新計畫請複製本文件，填妥 `<...>` 欄位；不適用的強制章節請填 `N/A` 並說明原因。此模板沿用 Libra 計畫模板的基線、決策、任務卡與驗收結構，並依 Dagrs 的 Rust crate 與非同步執行特性調整。

**模板版本：** `v1.0`（2026-09-26）

## 使用規則

- 日期計畫命名為 `plan-YYYYMMDD.md`，長期方向可記於 `plan-long.md`；若尚無長期計畫，不必為單一工作建立空檔。
- 計畫以目前 checkout 的原始碼、測試與文件為事實基線。歷史計畫或外部專案只作線索；引用外部程式碼時記錄固定 revision、檔案路徑與查核日期。
- 任務開始前刷新本卡涉及的程式碼、API、測試 target 與文件位置。若需求已完成，改做補測、補文件、記錄證據或關閉任務，不重做。
- 每張任務卡都要能獨立交付給執行者：範圍、依賴、檔案落點、驗收條件與驗證命令都要明確。
- 必要章節不得刪除；不適用時填 `N/A` 並附理由。完成卡片時，在同一計畫更新狀態與驗收證據。
- 多卡並行時，只有實作寫集不重疊且依賴已滿足的卡才能並行；測試、文件或整合落點重疊時先排序或合併。
- 公開 API、錯誤語義、序列化格式、checkpoint、channel 行為或相容性變更，必須在同一計畫安排測試、文件與遷移說明。

### 規範性 ID 與術語

正文引用要求時使用 ID（例如「依 G-01 拆卡」），不要使用可能因章節調整而失效的「上一條」。新增 ID 前同步此表。

| 前綴 | 含義 | 定義位置 |
|---|---|---|
| `ER-*` | 執行、驗收與完成要求 | 執行檢查必備需求 |
| `GC-*` | 全計畫適用的工程約束 | 全局工程約束 |
| `G-*` | 任務卡粒度與並行規則 | 任務卡粒度規則 |
| `ADR-*` | 已決議的設計決策 | 已決議設計決策 |
| `GAP-*` | 經證據確認的現況缺口 | 事實基線 |
| `DEP-*` | 計畫內外依賴 | 依賴登記表 |
| `REL-*` | 發布或整合分組（需要時） | 發布分組 |
| `FIX-*` | 執行時發現、超出原卡範圍的修復項 | 任務卡或修訂歷史 |
| `DEFER-*` | 明確延後的項目 | 非目標與延後項 |
| `M-*` | 里程碑 | 里程碑驗收與回復 |

**行為軸：** 一個可獨立實作、驗收與回復的行為變更方向。  
**落點：** 明確的程式碼、測試或文件目錄／檔案；`src/`、`tests/`、`docs/` 等頂層目錄太廣，不算具體落點。  
**寫集：** 任務預計修改的程式碼、測試、文件及設定檔集合；用來判斷任務是否可並行。  
**focused 測試：** 直接覆蓋該卡行為的最小測試集合。  
**回復方式：** 失敗後恢復到自洽狀態的方法，例如 revert、前滾修復或恢復舊 checkpoint 格式支援。

## 標題

`# <主題>計畫（<YYYY-MM-DD>）`

## 文件職責

本計畫處理 `<問題或能力>`，交付 `<可查驗的結果>`。計畫描述待完成的工作，不代表功能已實作。

### 適用範圍

- `<涉及的 Dagrs API、graph/node/channel/checkpoint 或 derive macro>`
- `<涉及的 Rust package、feature、範例或使用者流程>`
- `<需要更新的測試、README、CHANGELOG 或其他文件>`

### 非目標

- `<本計畫不處理的功能或模組>`
- `<由其他計畫承接的項目；沒有承接計畫時註明未排期與重啟條件>`
- `<不承諾的相容性或效能行為>`

### 成功定義

- `<使用者可觀察到的行為>`
- `<公開 API、錯誤、執行狀態或資料格式的結果>`
- `<測試與文件交付>`
- `<所有任務卡及計畫收口何時可標記完成>`

## 事實基線

> 開工時重新確認檔案與行號。以下是定位起點，不取代對當前程式碼和測試的檢查。

| 類別 | 本倉庫檢查方向 | 本計畫現況與證據 |
|---|---|---|
| Crate / workspace | 根 `Cargo.toml`、`dagrs-derive/Cargo.toml` | `<package、版本、workspace member>` |
| 公開 API | `src/lib.rs` 與相關 `src/` 模組 | `<API / file:line>` |
| 執行與狀態 | `src/graph/`、`src/node/`、`src/utils/` | `<行為 / file:line>` |
| 非同步通道 | `src/connection/` | `<容量、關閉、錯誤或排序語義 / file:line>` |
| Macro | `dagrs-derive/src/` | `<macro 輸入、展開及錯誤 / file:line>` |
| 測試 | `tests/`、`dagrs-derive/tests/` | `<target::test_fn>` |
| 範例 | `examples/`；`examples/dagrs-sklearn/` 是獨立 Cargo workspace | `<受影響範例 / manifest>` |
| 使用者文件 | 根 `README.md`、`dagrs-derive/README.md`、`docs/CHANGELOG.md` | `<需同步的章節>` |
| 外部參照 | `<repo@revision>` | `<路徑與查核日期；無則 N/A>` |

### 已確認的缺口

| ID | 缺口 | 影響 | 證據 | 對應任務 |
|---|---|---|---|---|
| GAP-01 | `<可重現描述>` | `<使用者或維護影響>` | `<file:line / test>` | `<TASK-ID>` |

## 與其他計畫或契約的關係

| 計畫／文件 | 關係 | 本計畫如何處理 |
|---|---|---|
| `docs/plan/plan-long.md` | `<關聯項目 / N/A>` | `<承接、更新或不影響>` |
| `docs/plan/plan-YYYYMMDD.md` | `<前置、並行、衝突或替代 / N/A>` | `<依賴或去重方式>` |
| `README.md` / `docs/CHANGELOG.md` | `<API / 使用說明 / 發版紀錄>` | `<同步項目>` |

## 評審結論與修訂記錄

開工前自審一次。若有阻斷項，先修計畫再開始實作。

| 維度 | 結論 | 修訂動作 |
|---|---|---|
| 目標與使用者影響 | `<清楚 / 待補>` | `<調整>` |
| 可行性與任務粒度 | `<可獨立交付 / 待拆卡>` | `<調整>` |
| API 與相容性 | `<影響面>` | `<測試或遷移安排>` |
| 非同步與並行安全 | `<狀態、取消、鎖與資源生命週期>` | `<調整>` |
| 錯誤與失敗恢復 | `<錯誤路徑與恢復方式>` | `<調整>` |
| 測試與文件完整性 | `<缺少的覆蓋>` | `<補充項>` |
| 效能與維護性 | `<預期成本及抽象邊界>` | `<調整>` |

### 修訂歷史

記錄成稿後影響任務範圍、依賴、設計決策、驗收或發布方式的變更。

| 日期 | 觸發 | 變更 | 原任務 → 新任務 | 受影響章節 |
|---|---|---|---|---|
| `<YYYY-MM-DD>` | `<評審 / 新證據 / 使用者決定>` | `<變更內容>` | `<TASK-ID → TASK-ID(s) / N/A>` | `<依賴、測試矩陣、里程碑等>` |

## 已決議設計決策

實作若需偏離已接受的決策，先更新本節並記錄理由。

### ADR-<PREFIX>-01：<決策標題>

- **狀態：** `<Proposed / Accepted / Superseded>`
- **背景：** `<需要解決的問題>`
- **決策：** `<選定方案>`
- **考慮過的方案：** `<替代方案與取捨>`
- **影響：** `<API、相容性、效能或維護成本>`
- **重新評估條件：** `<何時重審；無則 N/A>`

## 全局工程約束

以下要求適用於本計畫所有任務；不相關時在任務卡說明 `N/A`。

- **GC-01 現況先行：** 實作前核對當前 API、程式碼、測試與文件；計畫中的行號可能過期，不可直接當作現況證明。
- **GC-02 單一事實源：** graph 狀態、錯誤映射、checkpoint 序列化、macro 展開規則及共用 channel 行為不得在多處各自維護一份。
- **GC-03 公開 API 與相容性：** 評估 `dagrs` 與 `dagrs-derive` 的版本相容、Rust 型別/API 變化、錯誤語義及既有呼叫端。破壞性改動須明確提出遷移方式並記錄決策。
- **GC-04 非同步執行：** 執行期由呼叫端管理 Tokio runtime。不得在 async 執行路徑引入阻塞等待；channel 操作、取消、關閉與背壓語義要清楚。
- **GC-05 checkpoint：** 若修改 checkpoint 格式或恢復流程，定義舊資料的讀取、升級或拒絕方式，並覆蓋中斷與重試狀態。
- **GC-06 並行與資源生命週期：** 說明鎖、task、channel、訂閱者與外部資源的建立、關閉、取消及錯誤後清理方式。
- **GC-07 錯誤處理：** 公開失敗使用既有 `DagrsError` / `ErrorCode` 契約或明確說明其變更；不可將失敗轉成靜默成功。
- **GC-08 生產路徑 panic：** 新增 `unwrap()`、`expect()` 或 `panic!()` 前需證明邏輯不可失敗並附 `// INVARIANT:`；否則以 `Result` 傳回錯誤。
- **GC-09 測試與文件：** 公開行為改變時同步更新相關測試及使用文件；新增或修改 derive macro 時一併檢視 UI pass/fail fixtures。
- **GC-10 效能與容量：** 對無界佇列、無界 task 建立、重複序列化或熱路徑額外配置說明成本、上限與驗證方式。
- **GC-11 Cargo 管理：** 依賴與 lockfile 由 Cargo 管理；不得手工編輯 `Cargo.lock` 的版本或 checksum。修改 workspace、feature 或依賴時評估兩個 crate 與獨立範例 workspace 的影響。

## 執行檢查必備需求（強制）

所有適用的要求都必須完成，才可將任務或計畫標記完成。

- **ER-01 開工檢查：** 查看目前 VCS 狀態與目標檔案，辨認既有使用者修改；不得覆蓋未確認的變更。使用目前 checkout 可用的原生狀態工具。
- **ER-02 基線刷新：** 開工前確認本卡程式碼、測試、公開 API、feature、文件及外部參照的現況，將證據記入卡片。
- **ER-03 粒度檢查：** 開工前核對本卡唯一行為軸、驗收條件、寫集及依賴。發現範圍擴張或與並行卡衝突時，先修訂或拆卡。
- **ER-04 每卡驗收：** 每張程式碼卡執行本卡 focused 測試及驗收條件；文件卡檢查內容、連結與涉及的 API 描述。需要 feature 才能覆蓋時，命令必須明列該 feature。
- **ER-05 Review：** 完成前檢視實際差異、錯誤路徑、回復方式、公開 API 與文件同步；發現超出本卡範圍的工作，登記 `FIX-*` 或新卡，不靜默擴張。
- **ER-06 API 與文件：** 公開 API、macro 行為或使用方式變更時，列出並更新受影響文件；至少檢查根 `README.md`、`dagrs-derive/README.md` 與 `docs/CHANGELOG.md` 是否適用，逐項寫明更新或 `N/A` 理由。
- **ER-07 計畫收口測試：** 全部任務完成後執行計畫涉及的完整驗收。一般 Rust workspace 的預設命令為：

  ```bash
  cargo fmt --all -- --check
  cargo clippy --workspace --all-targets --all-features -- -D warnings
  cargo test --workspace --all-targets --all-features
  ```

  若計畫修改 `examples/dagrs-sklearn/`，它是獨立 workspace，另執行：

  ```bash
  cargo fmt --manifest-path examples/dagrs-sklearn/Cargo.toml -- --check
  cargo test --manifest-path examples/dagrs-sklearn/Cargo.toml --all-targets --all-features
  ```

  可依改動範圍省略不適用的命令，但須記錄理由與實際執行結果。若收口測試暴露與計畫有關的問題，修復後重跑相應驗收；整體仍未全綠時不可宣告計畫完成。
- **ER-08 版本與發布（適用時）：** 發版計畫須說明 `dagrs` 與 `dagrs-derive` 版本關係、SemVer 影響、CHANGELOG、建置／發布步驟與回復策略。不得以 build 成功代替已發布證據；未發版的計畫填 `N/A`。
- **ER-09 證據：** 記錄可重現的命令與結果；外部服務或工具失敗時說明錯誤、重試上限與後續處理。輸出中不得包含 token、secret 或使用者敏感資料。

## 實施順序

以有向無環依賴安排任務；可並行的任務需有互不重疊的寫集。

### 依賴登記表

| ID | 前置條件／任務 | 本卡所需產物 | 未滿足時的處理 |
|---|---|---|---|
| DEP-01 | `<任務、決策或外部前置>` | `<API、文件、工具或 N/A>` | `<阻塞處理>` |

### 任務順序與狀態

| 順序 | 任務 | 前置 | 寫集／落點 | 可並行 | 狀態 |
|---|---|---|---|---|---|
| 1 | `TASK-01` | `<無 / DEP-ID>` | `<具體路徑>` | `<是 / 否>` | `pending` |

### Phase 0：基線與設計

- `<核實現況、凍結 API 語義或完成 spike>`

### Phase 1：實作切片

- `<依賴就緒後的實作與測試任務>`

## 任務卡

### 任務卡粒度規則（強制）

- **G-01 單一行為軸：** 一張卡處理一個可獨立驗收的行為軸；不同 API、序列化格式或 runtime 語義不要塞進同一卡，除非它們必須共同改變才能保持正確。
- **G-02 完整交付：** 與該行為直接相關的實作、測試及必要文件在同一卡完成，不把驗收必要部分留給未指定的後續任務。
- **G-03 可檢查條件：** Acceptance criteria 與 Verification 都寫成可觀察結果或可執行命令；避免「改善效能」「確保安全」等未量化描述。
- **G-04 明確落點：** Implementation write set 列出具體模組／檔案，不只寫 `src/`、`tests/` 或 `docs/`。規模以 S/M/L 表示；L 卡需說明無法再拆分的理由。
- **G-05 自包含：** 不讀取其他卡的正文，也能理解本卡目的、要修改的內容、依賴與完成條件。
- **G-06 依賴無環：** 前置任務列入 DEP 表及任務順序；不得依賴尚未定義的介面或未確認的外部工作。
- **G-07 寫集無衝突：** 並行卡的實作寫集不得重疊。發現衝突時補上順序邊或指定整合卡。
- **G-08 發布邊界：** 若同一發布需要多張卡，指定唯一整合／發布點；版本 bump、CHANGELOG 與 crate 相容性檢查由計畫明確安排。

### Task <ID>：<任務標題>

**Task type：** `<implementation | migration | removal | docs | audit | spike | release>`

**Lifecycle：** `<pending | in-progress | blocked | done>`

**Description：** `<做什麼、為什麼，以及唯一的行為軸>`

**Out of scope：** `<明確不做的內容；若有後續承接，寫任務 ID；否則寫延後或永久非目標>`

**Current evidence：**

| 現況 | 證據 |
|---|---|
| `<程式碼、測試或文件中的事實>` | `<file:line / test target>` |

**Acceptance criteria：**

- [ ] `<使用者或呼叫端可觀察的行為>`
- [ ] `<錯誤、邊界、併發或恢復行為>`
- [ ] `<API／相容性／文件交付；不適用時說明 N/A>`

**Verification：**

- [ ] `<精確的 focused test / cargo 命令>`
- [ ] `<需要的 feature、環境或手動檢查>`

**Dependencies：** `<無 / TASK-ID / DEP-ID，並說明消費的產物>`

**Implementation write set：** `<具體程式碼、測試、文件與設定路徑>`

**Docs and compatibility impact：** `<列出 README、derive README、CHANGELOG、API 或格式相容性影響；或 N/A + 理由>`

**Rollback / recovery：** `<revert | forward-fix | 舊格式兼容 | N/A，並說明恢復後的自洽狀態>`

**Security and privacy：** `<路徑、輸入、secret、序列化等風險；無則 N/A>`

**Performance budget：** `<資料規模、複雜度、記憶體／延遲預期與驗證；無變化則 N/A>`

**Version / release：** `<patch | minor | major | N/A；說明 dagrs 與 dagrs-derive 是否需協同發布>`

**Estimated scope：** `<S | M | L + 不可再拆分的理由>`

**Granularity：** `axis=<單一行為軸>; complete=<實作、測試、文件是否同卡>; acceptance=<可判定>; verification=<可重現>; landing=<具體路徑數>; deps=<TASK-ID / DEP-ID / none>; writeset=<無重疊 / 順序化於 TASK-ID>; scope=<S/M/L>`

## 測試矩陣

此表是計畫最終需覆蓋的範圍；各卡執行與其行為直接相關的 focused 測試，計畫收口時按 ER-07 執行適用的完整命令。

| 類別 | 覆蓋內容 | 測試位置／命令 | 任務 |
|---|---|---|---|
| 單元 | `<純邏輯、狀態轉移、錯誤映射>` | `<src/** 或 cargo test -p dagrs ...>` | `<TASK-ID>` |
| 整合 | `<graph 執行、節點與 channel 協作>` | `<tests/<file>.rs / target>` | `<TASK-ID>` |
| Macro | `<輸入、展開、編譯失敗訊息>` | `<dagrs-derive/tests 或 UI fixture>` | `<TASK-ID>` |
| Checkpoint | `<保存、恢復、舊格式與失敗恢復>` | `<測試位置；不適用寫 N/A>` | `<TASK-ID>` |
| 範例 | `<公開用法可編譯>` | `<cargo check / manifest path>` | `<TASK-ID>` |
| 效能 | `<吞吐量、延遲、記憶體或容量>` | `<criterion / benchmark；不適用寫 N/A>` | `<TASK-ID>` |

## 追溯表

| 任務 | 需求或證據 | 程式碼落點 | 測試 | 文件／相容性動作 |
|---|---|---|---|---|
| `<TASK-ID>` | `<GAP-ID / issue / file:line>` | `<具體路徑>` | `<target::test_fn>` | `<README / CHANGELOG / N/A>` |

## 里程碑驗收與回復

| 里程碑 | 完成條件 | 證據 | 失敗後回復 |
|---|---|---|---|
| M0：基線確認 | `<現況、決策及依賴確認>` | `<檔案或命令輸出>` | `<N/A>` |
| M1：可驗收切片 | `<任務卡與 focused 測試通過>` | `<測試、review、文件>` | `<revert / forward-fix>` |
| M2：計畫收口 | `<ER-07 全部適用門通過>` | `<完整命令及結果>` | `<修復後重跑>` |

### 故障恢復矩陣

| 故障情境 | 可接受狀態 | 恢復動作 | 不可接受結果 |
|---|---|---|---|
| `<執行中取消或節點失敗>` | `<checkpoint / graph 可恢復狀態>` | `<明確重試或恢復步驟>` | `<靜默成功、資料不一致>` |

## 風險登記

| 風險 | 可能影響 | 緩解／偵測方式 | 任務 |
|---|---|---|---|
| `<風險>` | `<高／中／低與影響>` | `<設計、測試或監測>` | `<TASK-ID>` |

## 效能與容量摘要

| 操作 | 成本或增長來源 | 預算／上限 | 驗證方式 |
|---|---|---|---|
| `<操作>` | `<複雜度、配置或佇列增長>` | `<數值或 N/A 理由>` | `<benchmark / test>` |

## API、相容性與文件收口

- [ ] `dagrs` 公開 API 或 macro expansion 有變更時，已評估 source compatibility 與呼叫端遷移。
- [ ] `dagrs` 與 `dagrs-derive` 需協同變更時，已核對兩個 crate 的版本與功能相容性。
- [ ] 根 `README.md`、`dagrs-derive/README.md` 及 `docs/CHANGELOG.md` 已更新，或各自記錄 `N/A` 理由。
- [ ] checkpoint 或序列化格式已說明舊資料如何讀取、升級或拒絕，或記錄 `N/A`。
- [ ] 範例和 feature 說明與實際 API 一致，或記錄 `N/A`。

## Review 紀錄

| 輪次 | 範圍 | 結果 | 未解問題 | 證據 |
|---|---|---|---|---|
| R1 | `<任務或檔案>` | `<PASS / FAIL>` | `<問題與處置>` | `<review / 測試>` |

## 非目標與延後項

| ID | 內容 | 延後原因 | 重啟條件 | 承接位置 |
|---|---|---|---|---|
| DEFER-01 | `<項目>` | `<原因>` | `<觸發條件>` | `<計畫 / N/A>` |

## 完成判據

只有在以下條件全數滿足後，計畫才能標記完成：

- [ ] 所有非延後任務卡均為 `Lifecycle=done`，沒有未解除的 `blocked` 任務。
- [ ] 所有 Acceptance criteria 與 Verification 都已完成，命令和結果已記錄。
- [ ] ER-07 中所有適用的格式、lint、測試與範例驗收均通過；失敗已修復並重跑。
- [ ] 公開 API、derive macro、checkpoint、feature 與相容性影響已評估，必要文件已同步。
- [ ] 所有 `FIX-*` 已完成或正式列入 `DEFER-*`；延後項具備原因與重啟條件。
- [ ] 里程碑、風險、修訂歷史與 Review 紀錄已更新。
- [ ] 若計畫包含發布，ER-08 所需的版本、CHANGELOG、建置與發布證據已取得；否則記錄 `N/A`。
