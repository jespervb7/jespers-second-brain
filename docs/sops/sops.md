# Standard Operating Procedure (SOP) Template for Analytics Engineers

| Field | Value |
|-------|-------|
| **Document Title** | |
| **SOP ID** | |
| **Version** | |
| **Effective Date** | |
| **Review Date** | |
| **Owner** |  |
| **Approved By** | |
| **Frequency** | Daily / Weekly / Monthly / Ad Hoc |
| **Automation Level** | Manual / Semi-Automated / Automated |

---

# 1. Purpose

Describe the objective of this procedure.

> **Example:**
> This SOP provides standardized instructions for performing a specific analytics engineering process to ensure consistency, reliability, data quality, and operational efficiency.

---

# 2. Scope

## Applies To

- Teams
- Systems
- Data platforms
- Environments (Development, QA, Production)
- Business domains

## Out of Scope

- Related activities handled by other teams
- Processes covered by other SOPs

---

# 3. Roles and Responsibilities

| Role | Responsibilities |
|------|------------------|
| Analytics Engineer | Execute the procedure and validate results |
| Data Engineer | Support infrastructure and pipelines |
| Data Analyst | Validate business outputs |
| Product Owner | Define business requirements |
| Engineering Manager | Approve changes when required |
| Data Steward | Validate data governance compliance |

---

# 4. Preconditions / Requirements

## Access

- Data warehouse
- BI platform
- Git repository (branch permissions, PR review rights)
- CI/CD platform
- Cloud environment
- Ticketing system

## Software

- dbt
- SQL IDE
- Python
- Git
- Data catalog
- Orchestration platform

## Permissions

- Read access
- Write access
- Production deployment approval
- Secrets management

## Documentation

- Business requirements
- Data model documentation
- Existing SOPs
- Architecture diagrams

---

# 5. Inputs

| Input | Source | Required |
|------|--------|----------|
| Business Requirement | Jira / Product Team | Yes |
| Source Data | Data Warehouse | Yes |
| Existing Models | Git Repository | Optional |
| Data Dictionary | Documentation | Yes |

---

# 6. Outputs

- New dbt model
- Updated SQL model
- Dashboard
- Documentation
- Test results
- Deployment
- Monitoring updates

---

# 7. Procedure

## Step 1 — Preparation

### Objective

Describe what this step accomplishes.

### Actions

- Confirm business requirement is documented and unambiguous
- Identify upstream sources and confirm freshness/availability
- Create feature branch following naming convention (e.g. `feature/<ticket-id>-<short-desc>`)
- Review existing models for reuse before building new ones

### Validation

Branch created, requirement signed off, source data confirmed available.

---

## Step 2 — Implementation

### Objective

Build or modify the dbt model(s) to satisfy the requirement.

### Actions

1. Determine model layer (staging → intermediate → mart) and place file accordingly
2. Choose materialization (view / table / incremental / ephemeral) and justify the choice
3. Write SQL following the team's style guide (CTEs, naming, formatting)
4. Add or update YAML: column descriptions, source freshness config, tests-as-code
5. Add relevant macros or reference existing ones rather than duplicating logic
6. Run model locally (`dbt run --select <model>`) and confirm output shape

### Validation

Model compiles and runs without error; output matches expected grain and row count for a sample data pull.

---

## Step 3 — Testing

### Automated Tests (run in CI)

- Schema validation
- Null checks (`not_null`)
- Duplicate/uniqueness checks (`unique`)
- Referential integrity (`relationships`)
- Custom generic tests (business rules expressed as dbt tests)

### Manual / Periodic Validation

- Business logic validation (spot-check against source-of-truth or stakeholder sign-off)
- Performance validation (query runtime, warehouse cost)
- Regression testing (downstream models/dashboards unaffected)

### Acceptance Criteria

Document measurable success criteria (e.g. "100% of automated tests pass," "query runtime < X seconds," "stakeholder sign-off received").

---

## Step 4 — Documentation

Update:

- Data catalog
- dbt docs (descriptions, exposures)
- README
- Wiki
- Lineage graph
- Business definitions / metric glossary

---

## Step 5 — Deployment

### Deployment Checklist

- [ ] Code reviewed and approved via PR
- [ ] Branch merged following Git convention (dev → staging → prod promotion)
- [ ] Automated tests passed in CI
- [ ] Manual validation completed and signed off
- [ ] Approvals received
- [ ] CI/CD run successful
- [ ] Monitoring/alerting enabled for new/changed models

---

## Step 6 — Post-Deployment Validation

Verify:

- Data freshness
- Row counts
- Business metrics
- Dashboards
- Alerts
- Scheduled jobs

---

# 8. Decision Points

*(Branching logic during execution — see Section 9 for root-cause troubleshooting once something has already failed.)*

| Condition | Action |
|-----------|--------|
| Automated tests pass | Continue to deployment |
| Automated tests fail | Return to Step 2, fix, retest |
| Manual validation fails | Escalate to Product Owner / Data Steward before deploying |
| Deployment fails | Trigger Rollback Procedure (Section 10) |
| Data quality issue found post-deploy | Escalate per Section 14 |

---

# 9. Exception Handling

*(Diagnosing and resolving issues once they've occurred.)*

| Issue | Possible Cause | Resolution |
|-------|----------------|------------|
| Pipeline failure | Upstream issue | Investigate logs, check source freshness |
| Test failure | Invalid transformation | Correct SQL, add regression test to prevent recurrence |
| Missing data | Source delay | Notify stakeholders, check orchestration schedule |
| Permission error | Access revoked | Contact administrator |

---

# 10. Rollback Procedure

If deployment fails:

1. Stop deployment.
2. Restore previous version (via Git revert or previous artifact).
3. Validate restored objects.
4. Notify stakeholders.
5. Create incident ticket.
6. Perform root cause analysis.

---

# 11. Validation Checklist

- [ ] Requirements reviewed
- [ ] Code completed
- [ ] Automated tests passed
- [ ] Manual validation completed
- [ ] Documentation updated
- [ ] Peer review completed
- [ ] Deployment approved
- [ ] Production validated
- [ ] Stakeholders informed

---

# 12. KPIs / Success Metrics

### Team / Process Level

- Deployment success rate
- Incident count
- Mean Time to Recovery (MTTR)
- Test pass rate

### Model / Pipeline Level

- Model execution time
- Pipeline runtime
- Data freshness
- Data quality score

---

# 13. Risks

| Risk | Mitigation |
|------|------------|
| Source schema changes | Automated testing, source freshness alerts |
| Bad data | Data quality monitoring |
| Performance degradation | Query optimization, warehouse cost monitoring |
| Deployment errors | Rollback procedure |

---

# 14. Escalation Matrix

| Situation | Contact |
|-----------|---------|
| Pipeline failure | Data Engineering |
| Business logic issue | Analytics Engineering |
| Infrastructure issue | Platform Team |
| Critical production incident | On-call Engineer |

---

# 15. References

- Internal documentation
- Data catalog
- https://docs.getdbt.com
- SQL style guide
- Git repository (branching/PR conventions)
- Architecture diagrams
- Incident management procedures

---

# 16. Revision History

*(Tracks changes to this SOP document. If the underlying dbt model/schema this SOP governs changes, log that separately and cross-reference via "Related SOPs" in Appendix D.)*

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | | | Initial version |

---

# Appendix A – SQL / Code Examples

Replace with an example that reflects this SOP's actual procedure — e.g. an incremental model pattern or a macro call used in this pipeline:

```sql
-- Example: incremental model pattern
select
    order_id,
    customer_id,
    order_date,
    order_amount
from {{ source('raw', 'orders') }}
{% if is_incremental() %}
where order_date > (select max(order_date) from {{ this }})
{% endif %}
```

---

# Appendix B – Configuration

Document:

- Environment variables
- Configuration files
- Parameters
- Secrets (reference secure locations only)

---

# Appendix C – Troubleshooting Guide

| Symptom | Cause | Resolution |
|---------|-------|------------|
| Job timeout | Large dataset | Optimize query, consider incremental materialization |
| Missing rows | Source issue | Validate upstream tables, check source freshness |
| Failed tests | Logic error | Review transformations, check test definitions |

---

# Appendix D – SOP Metadata

| Field | Value |
|------|-------|
| SOP Name | |
| Business Area | |
| Priority | |
| Systems Used | |
| Dependencies | |
| Related SOPs | |
| Estimated Duration | |

---

# Workflow Overview

```text
Start
  │
  ▼
Preparation (branch created, sources confirmed)
  │
  ▼
Validate Inputs
  │
  ▼
Inputs Valid?
 ├── No ──► Resolve Issues ──► Revalidate
 └── Yes
        │
        ▼
Implementation (model built, layer/materialization chosen)
        │
        ▼
Testing (automated + manual)
        │
        ▼
Tests Passed?
 ├── No ──► Fix Issues ──► Retest
 └── Yes
        │
        ▼
Documentation
        │
        ▼
Deployment (PR review → CI/CD → environment promotion)
        │
        ▼
Deployment Successful?
 ├── No ──► Rollback ──► Incident Review
 └── Yes
        │
        ▼
Post-Deployment Validation
        │
        ▼
Validation Successful?
 ├── No ──► Investigate & Remediate
 └── Yes
        │
        ▼
End
```