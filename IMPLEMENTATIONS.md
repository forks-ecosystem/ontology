# Implementations of the Ontology

How each project applies the seven core concepts and universal axioms.

---

## ixbase — Medical Intelligence

### Concept Application

**Actor:** Doctors, Patients, Medical Organizations, Validators

**Operation:** Diagnosis entry, Treatment plan, Prescription, Medical note

**Block:** Medical record (immutable patient history)

**Table:** Patient registry, Diagnosis history, Treatment log

**Node:** Medical data validator, Regional health authority node

**Semantics:** Medical code mapping (ICD-10 regional variants, terminology normalization)

**Consensus:** Quorum validation (multiple doctors agree before finalizing)

### Example Flow

```
1. Doctor (Actor) examines patient
   └> Enters diagnosis (Operation)

2. Diagnosis interpreted by Semantics
   └> ICD-10-DE: F32.1 → ixBase::MentalHealth::MajorDepression::Moderate

3. Diagnosis grouped into Medical Block
   └> Block ID: uuid-12345
   └> Previous Block: uuid-12344 (last entry for this patient)

4. Validator Nodes receive Block
   └> Verify doctor credentials
   └> Validate diagnosis code
   └> Check patient status

5. Consensus (2 out of 3 validators agree)
   └> Block sealed
   └> Patient registry updated

6. Patient History Table
   └> New row: diagnosis_id, date, code, doctor_id, block_id
   └> Queryable by patient_id, date range, diagnosis code
```

### Specific Implementations

- **Repository:** https://github.com/forks-ecosystem/ixbase
- **Core:** `docs/SPEC.md`, `design/formalization/`, `design/cultural-barriers/`

---

## forks-life-node — Wallet & Node Deployment

### Concept Application

**Actor:** Users, Organizations, Automated services, Validator nodes

**Operation:** Send funds, Create account, Update balance, Set permissions

**Block:** Transaction block (atomic fund transfer)

**Table:** Account ledger, Balance history, Permission matrix

**Node:** Wallet node (validates transactions), Blockchain node (consensus)

**Semantics:** Currency normalization, Unit conversion (satoshis ↔ BTC)

**Consensus:** Double-spend prevention (distributed confirmation)

### Example Flow

```
1. User (Actor) initiates Send operation
   └> "Send 1.5 BTC to address X"

2. Operation interpreted by Semantics
   └> 1.5 BTC → 150,000,000 satoshis
   └> Verify sender has balance

3. Send grouped into Transaction Block
   └> Block ID: uuid-txn-789
   └> Operations: [debit_account, credit_account]

4. Wallet Nodes validate
   └> Verify sender's signature
   └> Check sender has sufficient balance
   └> Verify recipient address format

5. Consensus (51% of network agrees)
   └> Block finalized
   └> Balance tables updated

6. Account Ledger Table
   └> New rows: sender_balance--, recipient_balance++
   └> Queryable by account, date, transaction type
```

### Specific Implementations

- **Repository:** https://github.com/forks-ecosystem/forks-life-node
- **Core:** `install.sh`, `README.md`

---

## legacycoin — Blockchain & Exchange

### Concept Application

**Actor:** Traders, Market makers, Smart contracts, Validator nodes

**Operation:** Execute trade, Mint token, Burn token, Update price

**Block:** Blockchain block (transactions + smart contracts)

**Table:** Order book, Trade history, Token supply, Price history

**Node:** Blockchain validator, Market maker node, Oracle node

**Semantics:** Smart contract logic, Price normalization, Fee calculations

**Consensus:** Proof-of-work/stake (majority node agreement)

### Example Flow

```
1. Trader (Actor) places order
   └> "Buy 10 ETH at $2000/ETH"

2. Order interpreted by Semantics
   └> Price check: is offer within market range?
   └> Fee calculation: 0.1% = 0.02 ETH fee

3. Order added to Order Book Table
   └> Row: order_id, trader, asset, qty, price, status=pending

4. Market Maker Bot (Actor) matches order
   └> Creates Trade Block
   └> Contains: [buyer_debit, seller_credit, fee_debit]

5. Blockchain Nodes validate
   └> Verify buyer has funds
   └> Verify seller has asset
   └> Check no double-spend

6. Consensus (proof-of-work solved)
   └> Block finalized
   └> Order Book updated: status=filled
   └> Balance tables updated

7. Trade History Table
   └> New row: trade_id, buyer, seller, asset, price, timestamp, block_id
```

### Specific Implementations

- **Repository:** https://github.com/forks-ecosystem/legacycoin (future)
- **Core:** Blockchain consensus, smart contracts, exchange interface

---

## enterprise-management — Business Operations

### Concept Application

**Actor:** Employees, Managers, Departments, Automated workflows

**Operation:** Update KPI, Approve budget, Assign task, Record metric

**Block:** Business decision block (audit trail)

**Table:** Employee registry, Performance metrics, Budget allocations, Task log

**Node:** Audit node, Process validator, Analytics node

**Semantics:** Business taxonomy, KPI definitions, Performance scoring

**Consensus:** Approval workflow (manager review, director approval)

### Example Flow

```
1. Salesperson (Actor) records deal
   └> "Closed $50,000 deal with Client X"

2. Operation interpreted by Semantics
   └> Validate deal against opportunity
   └> Calculate commission: 2% = $1,000
   └> Record revenue: $50,000

3. Deal Block created
   └> Block ID: uuid-deal-456
   └> Contains: [revenue_credit, commission_debit, opportunity_close]

4. Audit Nodes validate
   └> Verify deal details
   └> Check client credit status
   └> Verify salesperson authority

5. Consensus (Manager + Finance approves)
   └> Block sealed
   └> Financial tables updated

6. Performance Metrics Table updated
   └> Salesperson.quarterly_revenue += $50,000
   └> Salesperson.deals_closed += 1
   └> Team.revenue += $50,000

7. KPI Aggregation
   └> Team metrics updated
   └> Department metrics updated
   └> Company dashboard updated
```

### Specific Implementations

- **Repository:** https://github.com/forks-ecosystem/enterprise-management (future)
- **Core:** Organization structure, process workflows, metrics aggregation

---

## Cross-Domain Example: Medical-to-Payment Flow

### Scenario: Patient Receives Treatment → Payment via Wallet

```
1. Doctor (Actor in ixbase) diagnoses patient
   └> Creates Diagnosis Operation
   └> Diagnosis Block added to patient record
   └> Diagnosis Table updated

2. Treatment administered, cost $500
   └> Patient (Actor in forks-life-node) receives invoice
   └> Patient initiates Payment Operation

3. Payment Block created
   └> Links to Diagnosis Block ID (reference)
   └> Amount: $500 (from USD semantics)
   └> Recipient: hospital_wallet_address

4. Transaction validated
   └> Wallet Nodes verify funds
   └> Check balance: Patient has $600 ✓
   └> Block sealed

5. Hospital receives payment
   └> Hospital Balance Table: +$500
   └> Patient Account Ledger: -$500

6. Enterprise-management dashboard
   └> Hospital Finance Team (Actor) records revenue
   └> Revenue Operation references both:
     - Original Diagnosis Block (ixbase)
     - Payment Block (forks-life-node)
   └> KPI updated: Hospital_revenue +$500

7. Full audit trail
   └> Can trace: patient → diagnosis → treatment → payment → revenue
   └> All blocks immutable and cryptographically linked
   └> All operations auditable by actor
   └> All semantics versioned
```

---

## Summary Table

| Aspect | ixbase | forks-life-node | legacycoin | enterprise-management |
|--------|--------|-----------------|------------|---------------------|
| **Primary Actor** | Doctor | User | Trader | Manager |
| **Main Operation** | Diagnosis | Transfer | Trade | KPI Update |
| **Key Block Type** | Medical record | Transaction | Smart contract | Decision block |
| **Core Table** | Patient registry | Account ledger | Order book | Metrics table |
| **Validator** | Medical expert | Blockchain node | Blockchain node | Audit team |
| **Consensus** | Quorum | Majority | Proof-of-work | Approval workflow |
| **Semantics Focus** | Medical codes | Currency units | Contract logic | Business taxonomy |

---

## Version History

- **v1.0** (2026-07-15): Four implementations detailed
