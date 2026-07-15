# Semantic Transformation Rules

How data is deterministically transformed between representations while maintaining consistency and auditability.

---

## Principles

1. **Determinism:** Given identical input and context, transformation always produces identical output
2. **Reversibility:** Transformations should be reversible where possible; information loss must be explicit
3. **Auditability:** Every transformation is logged with source, target, timestamp, and actor
4. **Composability:** Transformations can be chained (A → B → C)
5. **Versioning:** Semantic rules are versioned; historical transformations are reproducible

---

## Semantic Transformation Framework

### Structure

```
SemanticTransformation {
  id: UUID
  source_semantics: SemanticVersion
  target_semantics: SemanticVersion
  
  forward_mapping: Map<SourceValue, TargetValue>
  reverse_mapping: Map<TargetValue, SourceValue> (or null)
  
  transformation_function: Function
  validation_rules: Rule[]
  
  created_at: DateTime
  created_by: Actor
  
  documentation: String
  examples: Example[]
}
```

---

## Domain-Specific Semantics

### ixbase: Medical Semantics

#### Diagnosis Code Normalization

**Source:** Regional medical codes (ICD-10-DE, ICD-10-CM, ICD-11, etc.)
**Target:** ixBase Universal Diagnostic Codes

```
Transformation Rule {
  "ICD-10-DE:F32.1" -> "ixBase::MentalHealth::MajorDepressive::Moderate"
  
  reverse_mapping: bidirectional
  
  validation: [
    source_code_exists_in_registry,
    target_code_is_valid,
    semantics_version_current
  ]
  
  confidence: 1.0 (perfect mapping)
}
```

#### Severity Normalization

```
Transformation Rule {
  severity_mappings: {
    "mild" -> 1,
    "moderate" -> 2,
    "severe" -> 3,
    "critical" -> 4
  }
  
  transformation_function: 
    source_severity -> normalize_to_scale_1_4(source_severity)
  
  reverse_mapping: null (information loss in generalization)
}
```

#### Temporal Semantics

```
Transformation Rule {
  historical_semantics: {
    date_range: [2000-01-01, 2020-12-31],
    icd10_version: "2010",
    valid_codes: [...]
  },
  
  current_semantics: {
    date_range: [2021-01-01, now],
    icd10_version: "2023",
    valid_codes: [...]
  },
  
  bridge_mapping: historical_code -> current_code
}
```

---

### forks-life-node: Wallet Semantics

#### Currency Code Normalization

```
Transformation Rule {
  source: "Raw wallet format"
  target: "ISO 4217 equivalent"
  
  mappings: {
    "BTC" -> "XBT",
    "ETH" -> "ETH" (no standard code),
    "USDT" -> "USD (pegged)"
  }
  
  transformation_function:
    raw_currency_code -> standardize_or_register_custom(code)
}
```

#### Balance Transformation

```
Transformation Rule {
  source_format: "satoshis (raw units)"
  target_format: "BTC (user-facing)"
  
  transformation_function:
    satoshis -> satoshis / 100_000_000
  
  reverse_transformation:
    btc -> btc * 100_000_000
  
  precision: 8 decimal places
  
  rounding_rule: "banker's rounding (round-to-even)"
}
```

#### Multi-Region Balance Semantics

```
Transformation Rule {
  context: "Convert balance to regional currency"
  
  transformation_function:
    balance_in_btc + exchange_rate(region, timestamp) 
    -> balance_in_regional_currency
  
  reverse_mapping: null (exchange rates are temporal)
  
  audit_trail: [
    source_amount,
    exchange_rate_used,
    timestamp,
    rate_source,
    calculated_amount
  ]
}
```

---

### legacycoin: Smart Contract Semantics

#### Operation Type Mapping

```
Transformation Rule {
  source: "Raw transaction data"
  target: "Semantic operation type"
  
  mappings: {
    payload_type=0x01 -> "Transfer",
    payload_type=0x02 -> "Approval",
    payload_type=0x03 -> "Burn",
    payload_type=0x04 -> "Mint"
  }
  
  validation: [
    payload_type_valid,
    parameters_match_operation_type,
    actor_has_permission
  ]
}
```

#### Contract State Transformation

```
Transformation Rule {
  from_state: State_A
  to_state: State_B
  
  transformation_rules: [
    {field: "balance", change: "-1000"},
    {field: "allowance", change: "+500"},
    {field: "last_transfer", change: "timestamp"}
  ]
  
  invariants: [
    "total_supply unchanged",
    "no_negative_balances",
    "transfers_conserve_value"
  ]
}
```

---

### enterprise-management: Business Semantics

#### Metric Aggregation

```
Transformation Rule {
  source: "Individual transactions"
  target: "Aggregate metrics"
  
  aggregation_rules: {
    total_revenue: sum(transaction.amount),
    average_transaction: mean(transaction.amount),
    transaction_count: count(transactions)
  }
  
  time_windows: [
    daily,
    weekly,
    monthly,
    quarterly,
    yearly
  ]
  
  transformation_function:
    transactions -> aggregate_by_time_window
}
```

#### Status Workflow Semantics

```
Transformation Rule {
  workflow: {
    "draft" -> "submitted" (actor: creator)
    "submitted" -> "approved" (actor: manager, condition: review_passed)
    "approved" -> "executing" (actor: system)
    "executing" -> "completed" (actor: system, condition: success)
    "*" -> "cancelled" (actor: creator)
  }
  
  validation_at_each_step: [
    permissions_check,
    preconditions_satisfied,
    audit_trail_recorded
  ]
}
```

---

## Cross-Domain Semantic Bridging

### Example: Medical-to-Insurance Mapping

```
SemanticBridge {
  source_domain: "ixbase"
  target_domain: "enterprise-management"
  
  transformation: {
    medical_diagnosis -> claim_code -> insurance_category -> cost_model
  }
  
  loss_of_information: [
    "patient_identity (stripped for privacy)",
    "specific_treatment_details (generalized)"
  ]
  
  created_audit_trail: [
    source_diagnosis_id,
    transformation_rules_version,
    timestamp,
    actor_id
  ]
}
```

---

## Semantic Versioning

Each semantic version is immutable and traceable:

```
SemanticVersion {
  domain: "ixbase",
  version: "1.0.0",
  effective_date: "2026-01-01",
  deprecation_date: null (or future date),
  
  rules: Rule[],
  
  created_by: Actor,
  reviewed_by: Actor[],
  
  changelog: [
    {date: "2026-01-15", change: "Added F32.3 mapping", author: "..."},
    {date: "2026-02-01", change: "Deprecated F32.0 mapping", author: "..."}
  ]
}
```

---

## Validation & Testing

### Semantic Transformation Validation

```
Validation {
  test_cases: [
    {
      input: example_data,
      expected_output: expected_result,
      semantics_version: version_id
    }
  ],
  
  determinism_check: {
    run_transformation_n_times: 1000,
    verify_all_outputs_identical: true,
    fail_if_any_differ: true
  },
  
  reversibility_check: {
    for_each: test_case,
    apply: forward_transformation,
    apply: reverse_transformation,
    verify: result_equals_original
  }
}
```

---

## Version History

- **v1.0** (2026-07-15): Semantic transformation framework
