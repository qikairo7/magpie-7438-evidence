# magpie#7438 证据包

对应 issue：yetone/magpie#7438。事发时刻 2026-11-21 20:01 前后，本地时间 UTC+8。

## 文件清单

| 文件 | 内容 |
|---|---|
| `README.md` | 本说明 |
| `app.log` | magpie 全天日志（2026-11-14 起，含事发当天 10 次版本重启，0.1.4624 到 0.1.4652）。1.5 MB。已扫描，无密钥类字符串 |
| `usn_c.csv.gz` | C 盘 USN 日志全量导出（gzip）。解压后 80 MB、392,310 行。由 `fsutil usn readjournal C: csv` 于 11-21 20:13 导出 |
| `usn-summary.txt` | 对上述导出的分析：每分钟删除数、每分钟新建数、删除按父目录分组、命中关键字的记录 |
| `directory-timestamps.md` | 8 个受影响目录的 NTFS CreationTime / LastWriteTime 表，附机器状态排查结论 |
| `salvage-manifest.txt` | 事后 MFT 抢救的目录清单（只有名字和数量，不含内容） |

## USN 导出的已知限制

C 盘 USN 日志上限 32 MB。20:02:51 开始的删除和重装风暴（约 9.3 万删除、27.6 万新建）把更早的记录全部回卷覆盖。本导出最早一条记录是 20:02:51。**20:01:35 至 20:02:50 之间的删除记录（含 8 个目录被删的瞬间）无法提供**。这一窗口的时刻只能靠 `directory-timestamps.md` 里的目录创建时间约束。

CSV 编码：文件名和原因列是 GBK 中文，用 GBK 或 cp936 解码读取。列顺序：`Usn, 文件名, 文件名长度, 原因编号, 原因, 时间戳, 文件属性编号, 文件属性, 文件 ID, 父文件 ID, …`。时间戳是本地时间。

## 抢救结论摘要

- `/d/recovered`：从 MFT 抢救出的被删文件，共 68,753 个、5.5 GB。覆盖 `.dsh`（437 个严格校验通过的会话，含触发 ENOENT 的 session-67af0368，做了帧级截断修复）、`.codex`、`.config`、`.zcode`
- `/d/recovered_chrome`：Chrome `Local State` 相关抢救，377 MB，进行中
- 会话内容不在这个包里。需要进一步信息可以在 issue 里说
