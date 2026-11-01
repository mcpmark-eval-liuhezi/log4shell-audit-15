# Log4Shell Security Notes (CVE-2021-44228)

Quarterly security briefing research write-up for Log4Shell.

## Core Facts

- **CVE identifier:** CVE-2021-44228
- **What it is:** A JNDI lookup vulnerability in Apache Log4j 2's `log4j-core` component. An attacker who can control log messages or log message parameters can execute arbitrary code loaded from LDAP/RMI/other JNDI endpoints via message lookup substitution. Only `log4j-core` is impacted; `log4j-api` alone is not.
- **Affected versions:** Apache Log4j 2 versions 2.0-beta9 through 2.14.1 (i.e. all versions < 2.15.0). Per NVD: 2.0-beta9 through 2.15.0 excluding security releases 2.12.2, 2.12.3, and 2.3.1.
- **Fixed in:**
  - 2.15.0 (Java 8 and later) — disables message lookup substitution by default
  - 2.12.2 (Java 7)
  - 2.3.1 (Java 6)
  - Later follow-ups: 2.16.0 (completely removes JNDI lookup functionality), 2.17.0, and 2.17.1 (Java 8 and later; recommended final fix, also addressing CVE-2021-45105 and CVE-2021-44832)
- **Severity:** CVSS 3.x **10.0 CRITICAL** — CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H

## Remediation Summary

1. **Upgrade path:** Move to Log4j 2.17.1 (Java 8+), 2.12.2 (Java 7), or 2.3.1 (Java 6). 2.15.0's fix was incomplete in some non-default configurations (CVE-2021-45046), so 2.17.1 is the recommended target.
2. **Detect vulnerable deployments:** Scan for `log4j-core` presence and version (< 2.15.0), including transitive dependencies in build manifests and container images.
3. **Hardening:** Disable JNDI lookups via `log4j2.formatMsgNoLookups=true` (or `LOG4J_FORMAT_MSG_NO_LOOKUPS` env var) or remove the `JndiLookup` class from the `log4j-core` jar; upgrade to 2.16.0+ where the functionality is fully removed.
4. **Automated dependency scanning:** Enable GitHub Dependabot alerts + automated security updates, OWASP Dependency-Check, Snyk/Trivy/Grype in CI, and monitor related CVEs (CVE-2021-45046, CVE-2021-45105, CVE-2021-44832).

## Sources

- Apache Logging Services Security page: https://logging.apache.org/security.html
- NVD CVE-2021-44228: https://nvd.nist.gov/vuln/detail/cve-2021-44228
- Apache Log4j CVEs (ASF Blog): https://news.apache.org/foundation/entry/apache-log4j-cves
- JPCERT/CC alert: https://www.jpcert.or.jp/english/at/2021/at210050.html
