

| NETCONF error-tag | NETCONF error-type | HTTP Status Code |
| ---- | ---- | ---- |
| in-use | N/A | 409 |
| invalid-value | rpc |400 |
| invalid-value | protocol | 404 |
| invalid-value | application | 406 |
| (request) too-big | N/A | 413 |
| (response) too-big | N/A | 400 |
| missing-attribute | N/A | 400 |
| bad-attribute | N/A | 400 |
| unknown-attribute | N/A | 400 |
| bad-element | N/A | 400 |
| unknown-element | N/A | 400 |
| unknown-namespace | N/A | 400 |
| access-denied | protocol | 401 |
| access-denied | application | 403 |
| lock-denied | N/A | 409 |
| resource-denied | N/A | 409 |
| rollback-failed | N/A | 500 |
| data-exists | N/A | 409 |
| data-missing | N/A | 409 |
| operation-not-supported | protocol | 405 |
| operation-not-supported | application | 501 |
| operation-failed  | rpc | 412 |
| operation-failed | protocol | 412 |
| operation-failed | application | 500 |
| partial-operation | N/A | 500 |
| malformed-message | N/A | 400 |
