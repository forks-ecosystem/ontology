# LBTC Ecosystem Ontology v1.0

## Core Concepts

The LBTC Ecosystem is built on seven fundamental concepts:

### 1. **Block**

**Definition:** An immutable container that holds a set of operations, a timestamp, a reference to the previous block, and a cryptographic proof of integrity.

**Properties:**
- Immutable once created
- Cryptographically linked to previous block
- Contains one or more operations
- Carries semantic context (domain, version, culture)

**Examples:**
- Medical record block (ixbase)
- Transaction block (forks-life-node)
- Smart contract execution block (legacycoin)
- Business process block (enterprise-management)

---

### 2. **Table**

**Definition:** A structured collection of related data organized by schema, supporting queries and aggregations while maintaining semantic consistency.

**Properties:**
- Structured schema
- Queryable and indexable
- Semantically coherent rows
- Supports version tracking

**Examples:**
- Patient registry (ixbase)
- Account ledger (forks-life-node)
- Token supply table (legacycoin)
- Organization registry (enterprise-management)

---

### 3. **Operation**

**Definition:** An atomic state change that transforms a system from one consistent state to another, carrying actor identity, intent, and semantic meaning.

**Properties:**
- Atomic (all-or-nothing)
- Reversible or auditable
- Carries actor identity
- Semantically deterministic

**Examples:**
- Diagnosis entry (ixbase)
- Fund transfer (forks-life-node)
- Token transfer (legacycoin)
- Metrics update (enterprise-management)

---

### 4. **Actor**

**Definition:** An entity that initiates operations, maintains accounts, and acts within the ecosystem according to roles and permissions.

**Types:**
- **Human** — Individual person
- **Organization** — Corporate entity
- **Bot** — Automated process
- **Simulator** — Market or scenario modeling
- **Service** — System component
- **Node** — Consensus or validation participant

**Properties:**
- Unique identity
- Accounts (multiple)
- Permissions
- Audit trail

---

### 5. **Node**

**Definition:** A validation and consensus point in the distributed system, responsible for verifying operations, maintaining state, and participating in consensus.

**Properties:**
- Validates operations
- Maintains replicated state
- Participates in consensus
- Can be specialized (validator, explorer, relay)

**Examples:**
- Medical data validator (ixbase)
- Wallet node (forks-life-node)
- Blockchain validator (legacycoin)
- Enterprise audit node (enterprise-management)

---

### 6. **Semantics**

**Definition:** Rules that assign meaning to data and operations, enabling cross-domain interpretation and cultural/temporal normalization.

**Properties:**
- Deterministic transformation
- Domain-specific (medical, financial, organizational)
- Culture-aware (regional codes, terminology)
- Versioned and auditable

**Examples:**
- Medical terminology mapping (ixbase)
- Currency code normalization (forks-life-node)
- Smart contract semantics (legacycoin)
- Business process taxonomy (enterprise-management)

---

### 7. **Consensus**

**Definition:** A distributed agreement mechanism that ensures all nodes reach the same state despite failures, delays, or adversarial conditions.

**Properties:**
- Safety (agreement)
- Liveness (progress)
- Fault tolerance
- Auditable decision trail

**Examples:**
- Validation quorum (ixbase)
- Wallet synchronization (forks-life-node)
- Blockchain proof-of-work/stake (legacycoin)
- Business approval workflow (enterprise-management)

---

## Relationships

```
         Actor
          |
          | initiates
          |
          v
      Operation
          |
          | contains
          |
          v
        Block
          |
          | stored in
          |
          v
        Table
          |
          | validated by
          |
          v
         Node
          |
          | reaches
          |
          v
       Consensus
       |
       +-- via Semantics
```

---

## Universal Properties

### Immutability
Once data enters a Block, it cannot be retroactively changed. Modifications create new Blocks with explicit links to predecessors.

### Traceability
Every Operation carries:
- Actor identity
- Timestamp
- Semantic context
- Previous state reference

### Determinism
Given identical inputs and Semantics, the same Operation always produces identical outputs.

### Semantic Independence
Data structure (Table) remains independent of interpretation rules (Semantics), allowing reuse across domains.

---

## Cross-Domain Application

### ixbase (Medical)
```
Actor: Doctor
  → Operation: Diagnosis entry
    → Block: Medical record
      → Table: Patient registry
        → Node: Medical validator
          → Consensus: Quorum validation
            → Semantics: Medical terminology mapping
```

### forks-life-node (Wallet)
```
Actor: User
  → Operation: Send funds
    → Block: Transaction
      → Table: Account ledger
        → Node: Wallet node
          → Consensus: Double-spend prevention
            → Semantics: Currency normalization
```

### legacycoin (Blockchain)
```
Actor: Trader
  → Operation: Execute trade
    → Block: Trade block
      → Table: Order book
        → Node: Blockchain validator
          → Consensus: Blockchain consensus
            → Semantics: Smart contract logic
```

### enterprise-management (Business)
```
Actor: Manager
  → Operation: Update KPI
    → Block: Metrics block
      → Table: Performance registry
        → Node: Audit node
          → Consensus: Approval workflow
            → Semantics: Business process rules
```

---

## Version History

- **v1.0** (2026-07-15): Initial ontology
  - Seven core concepts
  - Universal properties
  - Cross-domain relationships
  - Four implementation examples
