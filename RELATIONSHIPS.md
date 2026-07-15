# Relationships Between Core Concepts

How the seven core concepts interact, depend on, and relate to each other.

---

## Hierarchical Relationships

```
                    Actor
                      ↓
                 initiates
                      ↓
                  Operation
                      ↓
                  becomes
                      ↓
                    Block
                      ↓
                 stored in
                      ↓
                    Table
                      ↓
             validated by
                      ↓
                     Node
                      ↓
              participates
                  in
                      ↓
                 Consensus
                      ↓
                 governed by
                      ↓
                  Semantics
```

---

## Conceptual Graph

### Core Dependencies

```
Actor
  ├── has → Account (in Table)
  ├── initiates → Operation
  ├── belongs to → Organization (Actor)
  └── has → Permissions (enforce via Node)

Operation
  ├── created by → Actor
  ├── interpreted by → Semantics
  ├── grouped into → Block
  ├── modifies → Table
  └── validated by → Node

Block
  ├── contains → Operation[]
  ├── references → Block (previous)
  ├── stored in → Table
  ├── sealed by → Consensus
  └── interpreted by → Semantics

Table
  ├── composed of → Row[]
  ├── has schema → Schema
  ├── stores → Block[]
  ├── stores → Account (from Actor)
  └── queried by → Node

Node
  ├── validates → Operation
  ├── maintains → Table (replicated)
  ├── maintains → Block (replicated)
  ├── participates in → Consensus
  └── applies → Semantics

Semantics
  ├── interpret → Operation
  ├── interpret → Block
  ├── interpret → Table (schema meaning)
  └── enable → Consensus (validation rules)

Consensus
  ├── involves → Node[]
  ├── finalizes → Block
  ├── uses → Semantics (validation)
  └── ordered by → temporal causality
```

---

## Process Flows

### Standard Operation Lifecycle

```
1. Actor initiates Operation
   └> Operation carries actor identity + intent

2. Operation is interpreted by Semantics
   └> Validates preconditions
   └> Transforms parameters

3. Operation enters Mempool
   └> Awaits validation

4. Node receives Operation
   └> Validates against current state (Table)
   └> Checks Semantics rules
   └> Checks Actor permissions

5. Operation grouped into Block
   └> Block references previous Block
   └> Block carries operation list

6. Consensus protocol runs
   └> Nodes validate Block
   └> Nodes apply Semantics
   └> Nodes reach agreement

7. Block finalized and sealed
   └> Cryptographic proof generated
   └> Block added to chain

8. Table updated
   └> Rows modified based on Operations
   └> New state persisted
   └> Old state archived (not deleted)

9. Event published
   └> Actors notified of outcome
   └> Audit trail updated
```

### Semantic Transformation Flow

```
Raw Data (Domain-specific format)
       ↓ [Semantic Rule v1.0]
Internal Representation
       ↓ [Query/Analysis]
Result (Internal)
       ↓ [Reverse Semantic Rule v1.0]
Output Format (User-facing)

With Versioning:
  If current_semantics_version != v1.0:
    └> Bridge mapping through intermediate versions
    └> Ensure deterministic transformation
```

### Consensus Flow

```
Proposer selects Block
       ↓
Broadcast to Validators
       ↓
Each Validator:
  1. Verify Block structure
  2. Validate each Operation
  3. Apply Semantics
  4. Check against current Table state
  5. Vote (accept/reject)
       ↓
Collect votes
       ↓
If consensus_threshold reached:
  1. Block finalized
  2. Table updated
  3. New Block becomes "previous" reference
Else:
  1. Block rejected
  2. Proposer tries again
```

---

## Cardinality & Multiplicity

### One-to-Many

```
Actor : Account = 1 : N
  One actor can have multiple accounts (medical, trading, personal)

Actor : Operation = 1 : N
  One actor performs multiple operations

Block : Operation = 1 : N
  One block contains multiple operations

Table : Row = 1 : N
  One table has many rows

Node : Table = N : M (Many-to-Many)
  Multiple nodes replicate multiple tables
```

### Many-to-Many

```
Node : Consensus = N : M
  Multiple nodes participate in multiple consensus protocols

Semantics : Table = N : M
  Multiple semantic versions can apply to same table
  Multiple tables can use same semantic rules

Actor : Organization = N : M
  Actor can belong to multiple organizations
  Organization has multiple actors
```

---

## Temporal Relationships

### Causality & Ordering

```
Operation A must occur before Operation B if:
  1. B depends on output of A
  2. Both reference same actor/account
  3. Consensus protocol requires ordering

Block A → Block B (temporal arrow)
  Block B's previous_hash == Block A's hash
  Guarantees causal ordering

Semantics v1.0 → Semantics v2.0 (deprecation)
  Cutover date defines when v2.0 takes effect
  Historical blocks continue using v1.0
  New blocks use v2.0
```

### Temporal Queries

```
Query: "What was table state at time T?"
Process:
  1. Find all Blocks created before time T
  2. For each Block, extract Operations
  3. Apply Semantics (version valid at time T)
  4. Reconstruct table state
  5. Return historical snapshot
```

---

## Semantic Relationships

### Equivalence

```
Two values are semantically equivalent if:
  semantic_transform(value_a, semantics_v1) 
    == 
  semantic_transform(value_b, semantics_v1)

Example:
  "ICD-10-DE: F32.1" ≡ "DSM-5: Major Depressive Disorder, Moderate"
  (both map to same internal semantic code)
```

### Subsumption

```
Semantic A subsumes Semantic B if:
  Every instance of B is also an instance of A
  (A is more general than B)

Example:
  "MentalHealth" subsumes "Depression"
  "Depression" subsumes "MajorDepression::Moderate"
```

### Conflict

```
Semantics conflict if:
  semantic_transform(value, semantics_a) ≠ semantic_transform(value, semantics_b)
  AND both semantics claim to be "correct"

Resolution:
  1. Explicit versioning (not both current)
  2. Regional context (different regions, different semantics)
  3. Temporal context (different eras, different semantics)
```

---

## Cross-Domain Relationships

### ixbase ↔ forks-life-node

```
Medical Actor → Wallet Account
  Doctor with medical records
    ↓
  Same person has crypto wallet
    ↓
  Linked via Actor.id

Medical Block → Transaction Block
  Diagnostic data → Payment for treatment
    ↓
  Both stored as immutable blocks
    ↓
  Both subject to semantic interpretation
```

### forks-life-node ↔ legacycoin

```
Wallet Balance → Exchange Order
  User has X BTC in wallet
    ↓
  User places order to trade on exchange
    ↓
  Order references wallet account
    ↓
  Trade execution updates both wallet and order book
```

### legacycoin ↔ enterprise-management

```
Trader Performance → Organizational KPI
  Individual trader executes trades
    ↓
  Trades aggregated into team performance
    ↓
  Team metrics contribute to org metrics
    ↓
  Actor (trader) �� Organization (company)
```

### All Four Domains

```
Actor (Human)
  ├── Medical Account (ixbase)
  │   └── Diagnosis blocks, patient history
  ├── Wallet Account (forks-life-node)
  │   └── Transaction blocks, balance tables
  ├── Trading Account (legacycoin)
  │   └── Order blocks, portfolio tables
  └── Employee Record (enterprise-management)
      └── Performance blocks, metrics tables

All accounts linked via Actor.id
All blocks use same foundational semantics
All tables follow same schema patterns
All nodes apply same consensus principles
```

---

## Version History

- **v1.0** (2026-07-15): Core relationships mapped
