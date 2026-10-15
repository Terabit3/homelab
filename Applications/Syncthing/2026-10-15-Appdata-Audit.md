# Syncthing appdata replication — October 15, 2026

- Servius Syncthing v2.1.6 (TrueNAS container `ix-syncthing-syncthing-1`), `Send Only` folder ID `cju72-hkhem`, `/mnt/speedpool/appdata`.
- h711nt Syncthing v1.30.0, `Receive Only` at `E:\TrueNAS Syncthing\speedpool\appdata`, roughly 259 GiB indexed; GUI `https://192.168.50.25:8384` via LAN and NPM proxy.
- Failures were from live database sidecar files, PostgreSQL WAL and `.pid`; after exclusions, no Failed Items or Out of Sync Items showed. One `arr_common` local addition at 0 bytes remained. Operator chose to leave it; don't force Revert or rebuild the index.

Current source ignore patterns observed:

```text
arr_common/
nextcloud/postgres_data/
syncthing/config/index-v2/
**/*.db-shm
**/*.db-wal
**/*.sqlite-shm
**/*.sqlite-wal
authentik/postgres/
fail2ban/data/db/
**/*.pid
```

**WARNING:** Syncthing does not create an application-consistent database backup. Add verified dumps for PostgreSQL and SQLite services, and ensure exclusions do not accidentally remove valuable Windows copies without separate archive backups. Keep source `arr_common` intact. Windows upgrade and receiver's 0-byte warning are deferred.

Snapshot policy: parent task #7 6 weeks excludes Syncthing; dataset-only task #14 1 week. Historical snapshots were pruned, October 15 baseline preserved. This is not off-device backup.
