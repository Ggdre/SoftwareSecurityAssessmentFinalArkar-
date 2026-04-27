# Semgrep Triage Notes (Webapp)

## Scan Scope
- Target: `webapp/app.py`
- Rulesets: `p/security-audit`, `p/owasp-top-ten`


## Finding A - True Positive
- **Rule ID:** `python.flask.security.injection.tainted-sql-string.tainted-sql-string`
- **Location:** `webapp/app.py` (e.g., login/register/product query lines)
- **Why flagged:** User-controlled input is directly embedded into SQL strings using f-strings.
- **Reachability:** High (internet-facing routes `/login`, `/register`, `/product/<id>`).
- **Exploitability:** High (validated in dynamic testing Task D with SQLi bypass).
- **Decision:** **True Positive**
- **Action:** Keep as blocking issue; refactor to parameterized queries.

## Finding B - False-Positive Candidate (Rule/Framework Mismatch)
- **Rule ID:** `python.django.security.injection.sql.sql-injection-using-db-cursor-execute.sql-injection-db-cursor-execute`
- **Location:** `webapp/app.py`
- **Why flagged:** Pattern detects tainted SQL execution correctly, but remediation guidance assumes Django QuerySets.
- **Context check:** Application is Flask + sqlite3 (not Django ORM).
- **Decision:** **Partial / Contextual finding**  
  (Risk is real SQLi, but framework-specific rule message is noisy/mismatched.)
- **Action:** Do not ignore risk; map to Flask SQLi remediation (`?` placeholders, parameterized execute).