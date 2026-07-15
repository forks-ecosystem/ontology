# Core Concepts — Deep Dive

Detailed definitions and properties of the seven core concepts.

---

## Block

### Full Definition

A Block is an immutable, cryptographically sealed container that:
- Holds a set of Operations
- References the previous Block (forming a chain)
- Carries metadata (timestamp, actor, domain, version)
- Provides cryptographic proof of integrity
- Cannot be altered without breaking the chain

### Structure

```
Block {
  id: UUID (unique identifier)
  previous_hash: Hash (link to parent)
  timestamp: DateTime (creation time)
  actor: Actor (who created this block)
  domain: String (ixbase, legacycoin, etc.)
  semantics_version: Version (interpretation rules used)
  operations: Operation[] (state changes)
  metadata: Map (additional context)
  hash: Hash (cryptographic seal)
}
```

### Lifecycle

1. **Creation:** Actor initiates Operation(s)
2. **Validation:** Nodes verify Operations against Semantics
3. **Consensus:** Distributed agreement is reached
4. **Finalization:** Block is sealed and referenced by successor
5. **Archival:** Block enters permanent history

### Key Properties

| Property | Value |
|----------|-------|
| Mutability | Immutable once created |
| Reversibility | Operations can be audited, not reversed |
| Discoverability | Queryable by ID, timestamp, actor, domain |
| Replicability | Can be verified independently on any node |

---

## Table

### Full Definition

A Table is a structured collection of rows that:
- Conforms to a defined schema
- Maintains semantic consistency
- Supports efficient queries and aggregations
- Preserves version history
- Enables temporal queries (state at time T)

### Structure

```
Table {
  id: UUID
  name: String
  schema: Schema {
    columns: Column[]
    primary_key: String
    foreign_keys: ForeignKey[]
  }
  rows: Row[] (or reference to storage)
  version: Version
  semantics: Semantics (interpretation rules)
  metadata: Map
}

Row {
  id: UUID
  values: Map<ColumnName, Value>
  created_at: DateTime
  created_by: Actor
  block_id: UUID (which Block created this row)
  deprecated_at: DateTime (optional)
}
```

### Examples

**ixbase: Patient Registry**
```
Table: patients
Columns:
  - patient_id (primary key)
  - medical_id (regional code)
  - name
  - date_of_birth
  - registered_by (actor)
  - created_at
  - semantics_version
```

**legacycoin: Order Book**
```
Table: orders
Columns:
  - order_id (primary key)
  - trader (actor)
  - asset
  - quantity
  - price
  - status (pending, filled, cancelled)
  - created_at
  - block_id
```

### Key Operations

- **INSERT:** Add row (creates Block)
- **UPDATE:** Modify row (creates new Block, marks old as deprecated)
- **DELETE:** Mark as deprecated (non-destructive)
- **QUERY:** Read rows matching criteria
- **AGGREGATE:** Sum, count, average, etc.
- **TEMPORAL:** Query state at historical time T

---

## Operation

### Full Definition

An Operation is an atomic instruction that:
- Transforms system state from valid state A to valid state B
- Is initiated by an Actor
- Is semantically meaningful (not arbitrary)
- Carries explicit intent and context
- Is either fully applied or fully rolled back

### Structure

```
Operation {
  id: UUID
  actor: Actor
  intent: String (what the actor is trying to achieve)
  action_type: String (create, modify, transfer, etc.)
  target: Entity (what is being affected)
  parameters: Map (action-specific parameters)
  timestamp: DateTime
  block_id: UUID (which Block contains this Op)
  semantics: Semantics (interpretation context)
  preconditions: Condition[] (must all be true)
  postconditions: Condition[] (must all result true)
}
```

### Atomicity Rules

1. **All or Nothing:** Either all postconditions are satisfied or nothing changes
2. **Consistency:** System must be in valid state before and after
3. **Isolation:** Concurrent operations don't interfere
4. **Durability:** Once confirmed by Consensus, changes persist

### Examples

**ixbase: Diagnosis Entry**
```
Operation {
  actor: Doctor_ID
  intent: "Record patient diagnosis"
  action: "create_diagnosis"
  target: Patient_ID
  parameters: {
    diagnosis_code: "ICD-10-DE: F32.1",
    severity: "moderate",
    notes: "Patient reports depressive symptoms..."
  }
  preconditions: [patient_exists, actor_is_doctor, patient_active]
  postconditions: [diagnosis_in_table, diagnosis_queryable, audit_trail_updated]
}
```

**legacycoin: Trade Execution**
```
Operation {
  actor: Trader_ID
  intent: "Execute market order"
  action: "execute_trade"
  target: Order_ID
  parameters: {
    asset: "BTC",
    quantity: 1.5,
    side: "buy",
    price: 45000
  }
  preconditions: [order_exists, trader_has_funds, market_open]
  postconditions: [trade_in_ledger, balance_updated, settlement_pending]
}
```

---

## Actor

### Full Definition

An Actor is an entity that:
- Initiates Operations
- Maintains one or more Accounts
- Has permissions and roles
- Is subject to audit
- Can be individual or collective

### Types

#### Human
- Individual person
- Has biometric or password authentication
- Examples: Doctor, Trader, User, Manager

#### Organization
- Corporate entity
- Has member Actors
- Examples: Hospital, Exchange, Company, Department

#### Bot
- Automated process
- Rules-based decision making
- Examples: Trading bot, Validator bot, Monitor

#### Simulator
- Scenario modeling
- "What-if" analysis without real consequences
- Examples: Market simulator, Scenario bot

#### Service
- System component
- Internal operations
- Examples: Market maker, Price oracle, Validator

#### Node
- Consensus participant
- Validates and replicates state
- Examples: Blockchain node, Medical validator node

### Actor Structure

```
Actor {
  id: UUID
  type: ActorType (human, organization, bot, simulator, service, node)
  name: String
  authentication: AuthMethod
  roles: Role[]
  permissions: Permission[]
  accounts: Account[]
  metadata: Map
  created_at: DateTime
  status: Status (active, suspended, archived)
}

Account {
  id: UUID
  actor_id: UUID
  account_type: String (medical_record, wallet, trading, business)
  balance: Decimal (domain-specific)
  operations: Operation[]
  created_at: DateTime
}
```

### Permissions Model

```
Permission {
  resource: String (what can be accessed)
  action: Action (create, read, update, delete, execute)
  scope: Scope (own, team, organization, global)
  conditions: Condition[] (additional constraints)
  granted_at: DateTime
  granted_by: Actor
}
```

---

## Node

### Full Definition

A Node is a system participant that:
- Validates Operations against Semantics
- Maintains replicated state (Tables and Blocks)
- Participates in Consensus
- Can be specialized for different roles
- Is independently verifiable

### Node Types

#### Full Node
- Stores complete history
- Validates all Operations
- Participates in Consensus
- Can serve as reference

#### Light Node
- Stores headers and recent state
- Trusts full nodes for validation
- Lower resource requirements

#### Observer Node
- Reads-only access
- No validation responsibility
- Examples: Explorer, Analytics

#### Validator Node
- Specialized validation logic
- Domain-specific expertise
- Example: Medical validator for ixbase

### Node Structure

```
Node {
  id: UUID
  node_type: String (full, light, observer, validator)
  domain: String
  endpoint: URL
  status: Status (online, offline, syncing)
  version: Version (software version)
  state: {
    last_block_id: UUID
    last_block_timestamp: DateTime
    synced_blocks: Integer
    pending_operations: Integer
  }
  consensus_role: ConsensusRole (proposer, validator, observer)
  peers: Node[] (connected nodes)
}
```

### Responsibilities

1. **Validation:** Check Operation against Semantics
2. **Replication:** Store Blocks and Tables
3. **Consensus:** Participate in agreement protocol
4. **Serving:** Answer queries from Actors
5. **Monitoring:** Track system health

---

## Semantics

### Full Definition

Semantics are rules that:
- Assign meaning to raw data
- Enable transformation between representations
- Support cross-domain and cross-cultural interpretation
- Are deterministic and versioned
- Can evolve over time

### Semantic Rules

```
Semantic {
  id: UUID
  domain: String (ixbase, legacycoin, enterprise, etc.)
  version: Version
  rules: Rule[] {
    source_type: String
    target_type: String
    transformation: Function (data -> data)
    reverse_transformation: Function (or null if lossy)
    metadata: Map
  }
  created_at: DateTime
  deprecated_at: DateTime (optional)
}
```

### Examples

**ixbase: Medical Code Mapping**
```
Semantic Rule:
  source_type: "ICD-10-DE (German)"
  target_type: "ixBase::internal"
  transformation: {
    "F32.1" -> "ixBase::MentalHealth::MajorDepression::Moderate"
    "F32.2" -> "ixBase::MentalHealth::MajorDepression::Severe"
  }
  reverse_transformation: (exists, bidirectional)
  applied_since: 2026-01-01
```

**legacycoin: Currency Normalization**
```
Semantic Rule:
  source_type: "Raw price_feed"
  target_type: "Normalized::USD"
  transformation: {
    price_in_satoshis -> price_in_usd
    apply_exchange_rate(timestamp)
    round_to_2_decimals
  }
  reverse_transformation: (null, information loss in rounding)
```

---

## Consensus

### Full Definition

Consensus is a mechanism that:
- Ensures all Nodes reach agreement on state
- Is fault-tolerant (tolerates some malicious or failed nodes)
- Is verifiable (agreement can be cryptographically proven)
- Is efficient (doesn't require all nodes to communicate directly)
- Is auditable (decision process is reproducible)

### Consensus Protocol Structure

```
ConsensusProtocol {
  name: String
  version: Version
  domain: String
  fault_tolerance: Float (e.g., 0.33 for BFT)
  
  phases: Phase[] {
    name: String
    participants: ActorType[]
    timeout: Duration
    success_condition: Condition
  }
  
  finality: {
    type: String (probabilistic, absolute, economic)
    criteria: Condition
  }
}
```

### Examples

**ixbase: Quorum Validation**
```
Phase 1: Submit
  - Doctor submits diagnosis operation
  
Phase 2: Validation
  - 3 independent medical validators check
  - Each validator verifies against Semantics
  
Phase 3: Agreement
  - If 2/3 validators approve, operation enters Block
  - If disagreement, operation is rejected with reason
  
Finality: Absolute (once in Block, never reversed)
```

**legacycoin: Blockchain Consensus**
```
Phase 1: Mempool
  - Transactions broadcast to network
  - Nodes validate transactions
  
Phase 2: Block Proposal
  - Miner/validator proposes next block
  - Other nodes verify block
  
Phase 3: Confirmation
  - Majority of nodes accept block
  - Block is added to chain
  
Finality: Economic (requires 51% attack to reverse)
```

---

## Version History

- **v1.0** (2026-07-15): Seven core concepts detailed
