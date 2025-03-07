<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [QA](#qa)

<!-- /TOC -->
</details>

## QA

出现`database disk image is malformed`:

解决:
```bash
sqlite3 db.sqlite "PRAGMA integrity_check;"
sqlite3 db.sqlite ".dump" > dump.sql
sqlite3 new.sqlite < dump.sql
COMMIT;
cat ./dump.sql | grep -v 'BEGIN TRANSACTION;' | grep -v 'ROLLBACK;' >./new_dump.sql
```
