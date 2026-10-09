# privatebase

**A backend for every user. Theirs alone.**

privatebase gives each user of your app a private backend in its own confidential
microVM, unlocked only by their end-to-end encrypted account. You ship the schema and
the code; you never see their data.

- **Attest:** each backend boots in its own confidential microVM on AMD SEV-SNP. The
  browser checks its attestation: the right code, on genuine hardware.
- **Unlock:** only an attested backend can unlock its user's data. The host in between
  relays ciphertext.
- **Run:** the CPU encrypts the VM's memory. The host, the cloud operator and the app
  developer see ciphertext only.

What a developer gets: a SQLite database per user (with migrations), Python functions
(`@app.function`), storage next to the data, realtime, an MCP server for AI assistants,
user management with recovery codes, and Dev/Prod stages with release promotion.

## How the pieces fit

```
             developer                               end user
                 │                                       │
              portal ──tenant API──┐            app + client SDK
                                   ▼              │          │
                           control plane ◄────────┘ accounts │ WebTransport
                          (API, workers)                     │ (attested,
                                   │ spawn                   │  cert-pinned)
                                   ▼                         ▼
                        confidential runtime ──► SEV-SNP microVM: micro-backend
                        (on the host)            (gateway + SQLite app)
```

The control plane never holds a user's data keys: keys are created in the browser, and
only an attested micro-backend can unlock the user's data.

| Component | Role |
|---|---|
| Control plane | End-to-end encrypted accounts, tenant API, micro-backend lifecycle and placement, attestation relay |
| Micro-backend | The per-user backend inside each microVM: a gateway (WebTransport, pinned certificate, session handshake) and the app (SQLite REST/GraphQL, realtime, functions, storage) |
| Client SDK | All browser code: accounts and keys, attestation verification, HPKE, the pinned WebTransport session |
| Confidential runtime | The microVM runtime targeting AMD SEV-SNP |
| Portal | The developer UI: projects with Dev/Prod, database designer, functions with live logs, secrets, users, docs |
