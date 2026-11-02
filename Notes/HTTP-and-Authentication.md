# KPBBI Bug Bounty — Kushal Mahajan

**Maintainer:** Kushal Santosh Mahajan · cypher3107  
**Focus:** HTTP, Burp Suite and evidence-based authentication testing

A day-wise learning journal for the KPBBI program. These notes organize HTTP, Burp Suite and authentication concepts. Practical results will be recorded as exercises are completed.

## Class material

- [Companion Notion journal](https://app.notion.com/p/40bfbd869bff81c49a94e790f2f87c08?pvs=204)

## Day 01: HTTP and Burp Suite

HTTP connects a client request to a server response. Inspect the method, path, headers, body, status code and session context together.

| Method | Typical purpose |
| --- | --- |
| GET | Read a resource |
| POST | Submit data, often login or verification input |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Remove a resource |

Status families: 2xx success, 3xx redirection, 4xx request/authentication errors and 5xx server errors. A status alone does not establish security impact.

Burp Proxy → Intercept allows inspection of requests and responses. HTTP history helps reconstruct the sequence. Preserve a baseline, change one thing and inspect the next real server response.

## Authentication bypass: the important distinction

Authentication establishes identity. Authorization decides what that identity may access. A changed screen is not proof of either.

A multi-step login has separate states: anonymous session, accepted primary credential, pending MFA challenge, completed verification, and access to a protected operation. The server must enforce every transition.

### Response manipulation

Changing an intercepted failure into a success response can make the browser proceed. It does not, by itself, update the server's verification state.

| Observation | What it establishes |
| --- | --- |
| Error disappears after response editing | Browser behavior changed |
| Dashboard screen loads | Navigation changed; server access still needs validation |
| Protected API returns 401/403 | Backend rejected access |
| Protected operation succeeds without required verification | Possible server enforcement failure, requiring reproducible evidence |
| Signup redirects to sign-in | Not proof of a newly created account |

### What a convincing lab record needs

1. Expected denial with an invalid or missing verification step.
2. The precise modification and browser result.
3. A subsequent protected request with its **unaltered server response**.
4. Evidence of persistent server-side state or a protected action.
5. A clean-session repeat to rule out an already authenticated session.
6. Actual impact, limitations and remediation.

## Authentication study guide

### Session puzzling

Session puzzling occurs when shared session state is interpreted differently by separate workflows. A recovery-stage session must not be treated as a fully authenticated identity.

**Lab question:** Can an anonymous, recovery or partially verified session reach a resource requiring full authentication?

**Expected design:** Separate workflow state, explicit endpoint checks, and session rotation when authentication level changes.

### Cookie and session controls

Useful checks in a controlled lab:

- Compare protected access with a valid session, no session and a logged-out session.
- Check expiry and server-side revocation after logout.
- Review session policy following password changes and resets.
- Verify rotation between anonymous and authenticated states.
- Ensure editable role, user or organization cookies cannot establish permissions.
- Review Secure, HttpOnly and SameSite in the application's context.

Cookie sessions and token-based designs can both be secure. A JWT is not automatically safer than an opaque session identifier.

### Password recovery

Review these recovery-token properties using controlled lab accounts:

| Property | Expected security behavior |
| --- | --- |
| Account binding | Token works only for its intended account |
| Purpose binding | Reset token is not accepted as a login or verification token |
| Expiration | Server enforces a documented, appropriate lifetime |
| Single use | Successful reset invalidates the token |
| Reissue | Behavior of older tokens is deliberate and tested |
| Invalid token | Missing, empty or altered values cannot authorize a reset |
| Confidentiality | Credentials and tokens do not leak unnecessarily |

Token format or length alone does not prove predictability. Multiple active reset links are not automatically a vulnerability: demonstrate practical risk.

### OTP and MFA

- Bind verification to the account, session, contact and intended action.
- Enforce pending versus completed verification at protected endpoints.
- Apply expiry, single use, retry limits and resend controls.
- Check that recovery and alternate login paths maintain equivalent requirements.
- Re-verify sensitive contact changes as required by the application policy.
- Do not let verification for one action authorize an unrelated action.

### JWT and OAuth — further reading

For JWT study, focus on signature verification, allowed algorithms, issuer, audience, expiry and token purpose. Symmetric signing is not inherently insecure; correct key management and validation matter.

For OAuth/OIDC study, review redirect URI validation, state/nonce where applicable, PKCE and binding the result to the initiating session. OAuth concerns delegated authorization; OpenID Connect adds identity.

## Practical documentation template

| Field | Record |
| --- | --- |
| Environment | Authorized lab, version and scope |
| Baseline | Expected permitted and denied behavior |
| Change | One controlled modification |
| Result | Actual server response and protected action |
| Evidence | Redacted requests, responses and screenshots |
| Impact | Demonstrated consequence |
| Limitations | Untested states and alternate explanations |
| Fix | Missing server-side check or state validation |

## Progress

- HTTP and Burp Suite notes organized.
- Authentication study guide added.
- Practical reproduction and screenshots: pending.

Practice only in authorized environments. Redact credentials, session tokens and personal data before publication.
