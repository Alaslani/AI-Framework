# Security Scan Flow

How `/security-scan` produces an *honest* result — verify the path, separate tool detection from result evaluation, run custom checks additively — and how the CI gate rolls out safely.

```mermaid
flowchart TD
    Start(["/security-scan [scope]"]) --> Disc["Discover and verify<br/>real source trees"]
    Disc --> PathOK{"Path exists?"}
    PathOK -->|No| Stop["STOP and report<br/>never pass an empty scan"]
    PathOK -->|Yes| Detect{"semgrep present?<br/>(command -v)"}
    Detect -->|No| Custom["Custom checks only<br/>reduced coverage"]
    Detect -->|Yes| Scan["semgrep --json<br/>parse results[]"]
    Scan --> Add["plus additive custom checks<br/>dedupe overlaps"]
    Custom --> Report["Report findings<br/>(only this scope's row)"]
    Add --> Report
    Report --> CI{"CI gate:<br/>ERROR baseline = 0?"}
    CI -->|Yes| Block["--severity ERROR --error<br/>required check"]
    CI -->|No| Ramp["Triage / suppress<br/>or non-blocking ramp"]

    style Scan fill:#27ae60,color:#fff
    style Block fill:#27ae60,color:#fff
    style Report fill:#2ecc71,color:#fff
    style Custom fill:#f1c40f,color:#000
    style Ramp fill:#f1c40f,color:#000
    style Stop fill:#e74c3c,color:#fff
```

| Signal | Meaning |
|--------|---------|
| Green | Trustworthy path — real scan, `results[]` parsed, gate enforced |
| Yellow | Reduced coverage or not-yet-blocking — proceed, but say so |
| Red | Stop — refuse to report a clean result you did not earn |

**When to use:** Explaining why a scan can pass while finding nothing real (a missing path, a misread exit code), or planning a safe CI gate rollout.

*See: [Semgrep SAST + /security-scan](../methodology/semgrep-sast.md)*
