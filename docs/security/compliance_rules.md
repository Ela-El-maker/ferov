# Compliance Rules

Compliance evaluates agent version, OS build, attestation status, update state, and policy hash alignment. ComplianceController returns compliant|non_compliant|unknown with reasons; FastAPI and Agent must respect policy hashes on connect.
