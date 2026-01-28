[← home](README.md) · [protocol ops map](protocol-ops-map.md)

## 101-Point Protocol Operations Runbook

## Node Configuration & Deployment

| # | What | Tools |
| ---: | --- | --- |
| 1 | Dedicated machine/VM for node (not dev workstation) | libvirt, Qubes, bare metal |
| 2 | Minimal OS install (no GUI, no extras) | Debian netinst, Alpine |
| 3 | Non-root user for node process | useradd, systemd DynamicUser |
| 4 | Systemd service with hardening directives | ProtectSystem, PrivateTmp, NoNewPrivileges |
| 5 | Working directory permissions (700) | chmod, chown |
| 6 | Separate partition/volume for chain data | LVM, btrfs subvolume |
| 7 | Config file permissions (600) | chmod |
| 8 | Environment-based config (no secrets in CLI args) | EnvironmentFile, .env |
| 9 | Predictable data directory structure | XDG or explicit --prefix |
| 10 | Read-only filesystem where possible | systemd ReadOnlyPaths |

## RPC Hardening

| # | What | Tools |
| ---: | --- | --- |
| 11 | RPC bind to localhost only | --rpc-host=127.0.0.1 |
| 12 | RPC authentication enabled | --rpc-api-key, HTTP Basic Auth |
| 13 | RPC over TLS | reverse proxy (nginx/caddy) + cert |
| 14 | RPC rate limiting | nginx limit_req, application-level |
| 15 | RPC method allowlist (disable dangerous methods) | config allowlist |
| 16 | Separate RPC credentials per consumer | unique API keys |
| 17 | RPC access logging | access.log, structured logging |
| 18 | No wallet RPC on public-facing node | --no-wallet or separate process |
| 19 | RPC timeout configuration | request timeout limits |
| 20 | CORS policy (if HTTP exposed) | strict origin allowlist |

## P2P Network & Peer Policy

| # | What | Tools |
| ---: | --- | --- |
| 21 | Max inbound peer limit | --max-inbound |
| 22 | Max outbound peer limit | --max-outbound |
| 23 | Peer banning policy (misbehavior score) | ban score threshold |
| 24 | Seed node diversity (no single seed dependency) | multiple seeds, DNS seeds |
| 25 | Manual trusted peers for critical nodes | --peers, addnode |
| 26 | Eclipse attack mitigation (peer diversity by ASN/subnet) | bucketing, eviction policy |
| 27 | Tor/I2P support for privacy | --proxy, onion service |
| 28 | P2P port not exposed unnecessarily | firewall, port filtering |
| 29 | Connection rate limiting | per-IP limits |
| 30 | Peer protocol version enforcement | minimum version check |
| 31 | DNS seed integrity verification | hardcoded seeds, DNSSEC |
| 32 | Feeler connections for peer table hygiene | addr manager rotation |

## Key Management (Protocol Level)

| # | What | Tools |
| ---: | --- | --- |
| 33 | Hot/warm/cold key separation | architecture design |
| 34 | Hot wallet = minimal balance only | sweep policy |
| 35 | Cold keys on air-gapped device | offline signing |
| 36 | Hardware wallet/HSM for signing | Ledger, Trezor, YubiHSM |
| 37 | Multisig for high-value operations | 2-of-3, 3-of-5 |
| 38 | Key derivation path documentation | BIP32/44/84 paths |
| 39 | Mnemonic seed backup (metal, distributed) | Cryptosteel, Shamir's Secret Sharing |
| 40 | Key rotation schedule | periodic rotation policy |
| 41 | Wallet encryption at rest | --wallet-passphrase, LUKS |
| 42 | Remote signer (keys ≠ node) | separate signing service |
| 43 | Passphrase timeout (auto-lock wallet) | --wallet-timeout |
| 44 | Transaction signing review (human approval for large tx) | manual threshold |

## Time Synchronization

| # | What | Tools |
| ---: | --- | --- |
| 45 | NTP/chrony configured and monitored | chrony, systemd-timesyncd |
| 46 | Clock drift alerting | chrony tracking, custom alert |
| 47 | Multiple NTP sources (no single point) | pool + explicit servers |
| 48 | NTP authentication (NTS) | chrony NTS support |

## Monitoring & Alerting

| # | What | Tools |
| ---: | --- | --- |
| 49 | Sync status monitoring (height vs network) | RPC getblockchaininfo |
| 50 | Peer count monitoring | RPC getpeerinfo |
| 51 | Mempool size monitoring | RPC getmempoolinfo |
| 52 | Disk usage alerting (chain growth) | df, node metrics |
| 53 | CPU/memory usage per node process | prometheus, node_exporter |
| 54 | Block propagation latency tracking | timestamp comparison |
| 55 | Reorg/fork detection alerting | chain tip monitoring |
| 56 | Finality monitoring (confirmation depth) | custom script |
| 57 | Error log aggregation | journald, loki, ELK |
| 58 | Uptime monitoring (node liveness) | healthcheck endpoint, ping |
| 59 | Network partition detection | peer diversity + external check |
| 60 | Ban list growth monitoring | getbanned, custom alert |

## Chain & Consensus Integrity

| # | What | Tools |
| ---: | --- | --- |
| 61 | Checkpoint verification enabled | built-in checkpoints |
| 62 | assumevalid / assumeutxo awareness | config flag |
| 63 | Full validation mode (not pruned) for critical nodes | --no-prune |
| 64 | Chain state backup (periodic snapshots) | btrfs snapshot, rsync |
| 65 | UTXO set integrity verification | gettxoutsetinfo |
| 66 | Block index corruption detection | reindex triggers |
| 67 | Consensus rule change tracking (soft/hard fork awareness) | release notes, BIPs |

## Testing & QA

| # | What | Tools |
| ---: | --- | --- |
| 68 | Testnet/signet before mainnet | --network=testnet |
| 69 | Regtest for local development | --network=regtest |
| 70 | Fuzzing critical parsers (network messages, scripts) | AFL, libFuzzer |
| 71 | Property-based testing | fast-check, hypothesis |
| 72 | Differential testing (compare implementations) | cross-client validation |
| 73 | Stress testing (high peer count, large blocks) | custom harness |
| 74 | Chaos testing (kill node mid-sync, corrupt DB) | manual, chaos-monkey |
| 75 | CI/CD pipeline for node software | GitHub Actions, Jenkins |
| 76 | Code review before merge (2+ reviewers for consensus code) | PR policy |
| 77 | Reproducible builds verification | guix, diffoscope |

## Upgrade & Release Management

| # | What | Tools |
| ---: | --- | --- |
| 78 | Staged rollout (testnet → staging → mainnet) | deployment pipeline |
| 79 | Binary signature verification before deploy | GPG verify, checksums |
| 80 | Rollback plan documented | snapshot + previous binary |
| 81 | Database migration testing | regtest dry-run |
| 82 | Changelog review for consensus changes | release notes |
| 83 | Downtime notification (if public service) | status page |
| 84 | Blue-green deployment (zero-downtime) | load balancer, dual nodes |

## Backup & Recovery

| # | What | Tools |
| ---: | --- | --- |
| 85 | Chain data backup (periodic) | btrfs snapshot, restic |
| 86 | Wallet backup (separate from chain data) | encrypted export |
| 87 | Key material backup (separate from wallet) | offline, metal seed |
| 88 | Config backup (versioned) | git, dotfiles |
| 89 | Restore drill (full node from backup) | scheduled test |
| 90 | Restore drill (wallet recovery from seed) | scheduled test |
| 91 | Backup integrity verification | checksum, test decrypt |

## Incident Response (Protocol-Specific)

| # | What | Tools |
| ---: | --- | --- |
| 92 | Consensus bug response plan | emergency shutdown procedure |
| 93 | Key compromise response plan | revoke, rotate, sweep funds |
| 94 | Network partition response plan | manual peer connection |
| 95 | Stolen funds response plan | freeze, trace, notify |
| 96 | Vulnerability disclosure policy | security.txt, responsible disclosure |
| 97 | Communication channel for emergencies | Signal group, encrypted channel |

## Logging & Audit

| # | What | Tools |
| ---: | --- | --- |
| 98 | Structured logging (JSON) | node config, pino, winston |
| 99 | Log rotation policy | logrotate, journald MaxRetention |
| 100 | Sensitive data redaction in logs (no keys, no seeds) | log filter |
| 101 | Audit trail for wallet operations (who signed what when) | custom log, append-only |
