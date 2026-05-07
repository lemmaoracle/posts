# アーキテクチャ: Lemma + DLP/SIEM統合

## 設計原則

**Lemmaは証明レイヤーであり、検知レイヤーではありません。**

既存のエンタープライズセキュリティツール（DLP, SIEM, CASB）は検知とアラートを担当します。Lemmaは何が起きたかの暗号学的証明を提供し、監査結果を否認不可能で改ざん検知可能にします。

```
┌─────────────────────────────────────────────────────────┐
│                     Enterprise Network                   │
│                                                          │
│  ┌──────────┐     ┌──────────────┐     ┌──────────────┐ │
│  │  CRM /   │────▶│   Lemma      │────▶│   DLP/SIEM   │ │
│  │  DB      │     │  Attestation │     │  (existing)  │ │
│  │          │     │  Gateway     │     │              │ │
│  └──────────┘     └──────┬───────┘     └──────────────┘ │
│                          │                               │
│                          ▼                               │
│                  ┌──────────────┐                        │
│                  │  Commitment  │                        │
│                  │  Root /      │                        │
│                  │  On-chain    │                        │
│                  │  Anchor      │                        │
│                  └──────────────┘                        │
└─────────────────────────────────────────────────────────┘

                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
   ┌────────────┐ ┌────────────┐ ┌────────────┐
   │ Insurer A  │ │ Agency B   │ │ Regulator  │
   │ (verify)   │ │ (verify)   │ │ (audit)    │
   └────────────┘ └────────────┘ └────────────┘
```

## コンポーネント詳細

### 1. Lemma認証ゲートウェイ

データストアとユーザーの間に配置されます。すべての読み取り/クエリを傍受し:

- ZK認証を生成: `proof(user_id, record_id, timestamp, access_type)`
- 認証をローカルMerkleツリーにコミット
- 定期的にMerkleルートをオンチェーンに固定
- データ内容を検査または保存**しません**（プライバシー保護）

### 2. DLP/SIEM（既存）

通常の機能を継続:

- 異常なアクセスパターン（一括エクスポート、異常な時間帯、時間外アクセス）を監視
- アラートとインシデントを生成
- **新統合:** DLPが異常をフラグすると、Lemmaから**検証済みアクセスレポート**を要求可能 — 誰がいつどのレコードにアクセスしたかの暗号学的証明

### 3. コミットメントルート / オンチェーン固定

- すべての認証のMerkleルート、定期的に固定
- 任意の当事者（保険会社A、代理店B、規制当局）が、特定の時間に認証が生成され、変更されていないことを検証可能
- 機密データはオンチェーン上にありません — コミットメントのみ

### 4. 検証レイヤー

各ステークホルダーは独立して以下を検証可能:

- **保険会社A**: 「従業員Xが日付[D1..D2]の間にレコード[1..N]にアクセスした」
- **代理店B**: 「同じ従業員が出向中に当方のレコード[M..P]にアクセスした」
- **規制当局**: 「両組織の認証は一貫性があり、改ざん検知可能である」

## データフロー: アクセスイベント

```
User → CRM Query
         │
         ▼
   ┌─────────────────────┐
   │ Attestation Gateway │
   │ 1. Intercept query  │
   │ 2. Generate ZK att. │
   │ 3. Commit to tree   │
   │ 4. Forward query    │──▶ CRM returns data
   └─────────────────────┘
         │
         ▼
   Attestation stored:
   {
     user_hash: "0xabc...",
     record_hash: "0xdef...",
     timestamp: 1714982400,
     access_type: "read",
     merkle_index: 47,
     proof: "0x..."
   }
```

## データフロー: 異常検知 → 証明

```
DLP detects anomaly ──▶ Requests verified access report from Lemma
                              │
                              ▼
                    Lemma assembles attestations
                    for the flagged user/timeframe
                              │
                              ▼
                    Returns cryptographic proof:
                    "User X accessed Y records
                     between T1 and T2,
                     here is the Merkle proof
                     against root R (anchored at block B)"
                              │
                              ▼
                    DLP incident now has
                    UNDENIABLE EVIDENCE
```

## データフロー: 規制監査

```
FSA requests audit ──▶ Insurer exports attestation set
                                  │
                                  ▼
                        FSA verifies against
                        on-chain commitment roots
                                  │
                                  ▼
                        No manual log reconciliation.
                        Proof is self-evident.
```

## 導入モデル

| モデル | 説明 | 最適な用途 |
|---|---|---|
| **ゲートウェイプロキシ** | LemmaがCRM/DBの前段にリバースプロキシとして配置 | グリーンフィールド導入、クラウドネイティブシステム |
| **SDK統合** | アプリケーションがデータアクセスの前後にLemma SDKを直接呼び出し | APIレイヤー制御を持つ既存システム |
| **ログ後処理** | Lemmaが既存のアクセスログを処理し、事後的に認証を生成 | プロキシ挿入が困難なレガシーシステム（最も弱い保証 — ログを信頼する必要がある） |

## セキュリティ考慮事項

- **ゲートウェイバイパス**: ユーザーがCRMに直接アクセスできる場合（ゲートウェイを迂回）、認証は不完全になります。対策: ネットワークレベルでの強制（ゲートウェイがデータストアへの唯一の経路）
- **オンチェーン固定頻度**: コストと証明の細かさのトレードオフ。推奨: 高感度環境では時間単位の固定
- **プライバシー**: ZK認証はアクセスが発生したことを証明し、何がアクセスされたかは明かしません（レコード内容は非公開のまま）