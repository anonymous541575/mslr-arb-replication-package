# Function-Level Aging-Related Bug Detection

This repository provides the anonymized replication package for the paper *Function-Level Aging-Related Bug Detection via Multi-Scale Local Structural Modeling and Relation-Specific Graph Views*.

The study advances aging-related bug (ARB) detection from file-level prediction to function-level detection. Existing ARB studies mostly identify source files that may contain aging-related defects, but file-level prediction is often too coarse to support precise localization and repair. This work therefore defines the task of determining whether a given function is an aging-related function.

The study constructs function-level ARB datasets for Linux, MySQL, and NetBSD, and proposes a unified structural learning framework. The framework uses multi-scale local program graphs as the backbone and integrates control-flow graph (CFG) and control-dependence graph (CDG) views for relation-specific enhancement. In this way, the model jointly captures function-body structure, execution-path organization, and control-dependence complexity.

The repository is anonymized for double-blind review. Author-identifying information will be added after acceptance.

---

## Repository Contents

This replication package includes:

* curated function-level bug/function annotations;
* candidate bug filtering rules and filtering records;
* function-level positive sample annotation rules;
* preprocessed graph datasets;
* scripts for reproducing the main experimental results;
* baseline scripts for Devign and CodeBERT;
* graph-encoder comparison scripts for GCN, GAT, GraphSAGE, and the proposed encoder;
* commands for reproducing the main result tables.

---

## Data Sources and Availability

The preliminary component scope for the three software systems was first derived from component-level CSV/ARFF files used in prior file-level ARB studies. Based on these CSV/ARFF files, this study determined the candidate component ranges for function-level data construction.

Candidate aging-related bug IDs were mainly collected from the following prior ARB studies:

* **Linux and MySQL:** *Predicting Aging-Related Bugs Using Software Complexity Metrics*
* **NetBSD:** *The Impact of Feature Selection Techniques on Aging-Related Bug Prediction Models: An Empirical Investigation*

For these candidate bugs, official fix records and bug reports were further examined using the corresponding official project resources:

* **Linux:** [https://bugzilla.kernel.org/show_bug.cgi](https://bugzilla.kernel.org/show_bug.cgi)
* **MySQL:** [https://bugs.mysql.com/](https://bugs.mysql.com/)
* **NetBSD:** [https://www.netbsd.org/support/query-pr.html](https://www.netbsd.org/support/query-pr.html)

The final function-level bug/function list used in this study was manually derived from these sources.

### Availability Note

This replication package includes the final curated bug/function lists and the data-construction metadata used in this study. It does **not** redistribute third-party papers.

For Linux and MySQL, the original prior study provides access information for the corresponding component-level CSV/ARFF files. Therefore, this package refers users to the original paper rather than redistributing those materials here.

For NetBSD, part of the preliminary metadata was obtained through private communication associated with *The Impact of Feature Selection Techniques on Aging-Related Bug Prediction Models: An Empirical Investigation*. This replication package does not redistribute those original files. Users interested in the original NetBSD metadata should contact the authors of that study directly. Instead, this package provides the processed function-level annotations and the rules used to construct the final dataset adopted in the present work.

---

## Final Dataset Statistics

| Dataset | Positive functions | Negative functions | Positive:Negative ratio |
| ------- | -----------------: | -----------------: | ----------------------: |
| Linux   |                  8 |                 40 |                     1:5 |
| MySQL   |                 39 |                195 |                     1:5 |
| NetBSD  |                 20 |                100 |                     1:5 |

---

## Candidate Bug Filtering

The final function-level datasets were not constructed by directly accepting all candidate bug IDs from prior studies. Candidate bugs were checked against official bug/fix information, software version availability, and the file-level aging-file scope from prior ARB studies.

A candidate bug/function entry was retained only when all of the following conditions were satisfied:

1. the affected file was within the file-level aging-file scope used by prior ARB studies;
2. the corresponding official bug/fix information was available;
3. the affected version could be mapped to an officially released and downloadable version;
4. the available fix information provided sufficient function-level evidence.

Candidate entries were excluded when the affected file was outside the file-level aging-file scope, the version was only a test/release-candidate/non-official version without a suitable official-release mapping, or the available documents did not provide sufficient function-level evidence.

### Linux Candidate Bug Filtering

| Bug ID | Original Version          | Original File                          | Original Function               | Final Status                                                                   | Final Version/File/Function or Exclusion Reason                           |
| ------ | ------------------------- | -------------------------------------- | ------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| 1209   | 2.6.0-test5               | `drivers/scsi/sym53c8xx_2/sym_glue.c`  | `sym53c8xx_queue_command`       | Included with official-release mapping                                         | 2.6.4 / `drivers/scsi/sym53c8xx_2/sym_glue.c` / `sym53c8xx_queue_command` |
| 2425   | 2.6.4                     | Not clear                              | Not clear                       | Excluded                                                                       | Insufficient file/function-level evidence                                 |
| 3171   | 2.6.12.6                  | `fs/ext3/balloc.c`                     | `ext3_try_to_allocate_with_rsv` | Included                                                                       | 2.6.12.6 / `fs/ext3/balloc.c` / `ext3_try_to_allocate_with_rsv`           |
| 3431   | 2.6.9-rc2-mm1             | `fs/ext3/ialloc.c`                     | `ext3_new_inode`                | Included with official-release mapping                                         | 2.6.12.6 / `fs/ext3/ialloc.c` / `ext3_new_inode`                          |
| 3431   | 2.6.9-rc2-mm1             | `fs/ext3/inode.c`                      | `ext3_discard_reservation`      | Included with function adjustment based on official-release source/fix context | 2.6.12.6 / `fs/ext3/inode.c` / `ext3_truncate`                            |
| 3699   | 2.6.10-rc1                | `drivers/scsi/aacraid/*`               | `aac_rkt_check_health`          | Included with official-release mapping                                         | 2.6.9 / `drivers/scsi/aacraid/rkt.c` / `aac_rkt_check_health`             |
| 3699   | 2.6.10-rc1                | `drivers/scsi/aacraid/*`               | `aac_rx_check_health`           | Included with official-release mapping                                         | 2.6.9 / `drivers/scsi/aacraid/rx.c` / `aac_rx_check_health`               |
| 5137   | 2.6.12.5                  | `drivers/net/r8169.c`                  | Not clear                       | Excluded                                                                       | Insufficient function-level evidence                                      |
| 5144   | 2.6.13                    | Not clear                              | Not clear                       | Excluded                                                                       | Insufficient file/function-level evidence                                 |
| 5284   | 2.6.12.6                  | `drivers/net/r8169.c`                  | `rtl8169_rx_vlan_skb`           | Included                                                                       | 2.6.12.6 / `drivers/net/r8169.c` / `rtl8169_rx_vlan_skb`                  |
| 5711   | 2.6.15-rc4-686            | Not clear                              | Not clear                       | Excluded                                                                       | Non-official/test version and insufficient file/function-level evidence   |
| 6043   | 2.6.15                    | Not clear                              | `sas_alloc_task`                | Excluded                                                                       | Corresponding source file could not be reliably located                   |
| 6114   | 2.6.15                    | `drivers/ieee1394/sbp2.c`              | Not clear                       | Excluded                                                                       | File not included in the file-level ARB CSV scope                         |
| 7718   | 2.6.22.5                  | `drivers/net/pcmcia/xircom_cb.c`       | Not clear                       | Excluded                                                                       | Insufficient function-level evidence                                      |
| 7718   | 2.6.22.5                  | `drivers/net/pcmcia/xircom_tulip_cb.c` | Not clear                       | Excluded                                                                       | Insufficient function-level evidence                                      |
| 9468   | 2.6.24-rc3                | Not clear                              | Not clear                       | Excluded                                                                       | Non-official/test version and insufficient file/function-level evidence   |
| 11100  | 2.6.25.9                  | `drivers/net/via-velocity.c`           | Not clear                       | Excluded                                                                       | Insufficient function-level evidence                                      |
| 11377  | 2.6.27-rc3-git6           | `drivers/net/mv643xx_eth.c`            | `rxq_refill`                    | Excluded                                                                       | Non-official/test version or unavailable official-release mapping         |
| 11377  | 2.6.27-rc3-git6           | `drivers/net/mv643xx_eth.c`            | `mv643xx_eth_poll`              | Excluded                                                                       | Non-official/test version or unavailable official-release mapping         |
| 11937  | 2.6.28-rc2                | `fs/jbd/log.c`                         | `__log_wait_for_space`          | Excluded                                                                       | Corresponding file not available under the target source tree             |
| 13293  | 2.6.30-rc4                | Not clear                              | Not clear                       | Excluded                                                                       | Non-official/test version and insufficient file/function-level evidence   |
| 20882  | 2.6.36.1                  | `drivers/net/r8169.c`                  | Not clear                       | Excluded                                                                       | Insufficient function-level evidence                                      |
| 32832  | 2.6.38                    | `net/ipv4/inet_connection_sock.c`      | `inet_csk_bind_conflict`        | Included                                                                       | 2.6.38 / `net/ipv4/inet_connection_sock.c` / `inet_csk_bind_conflict`     |
| 32832  | 2.6.38                    | `net/ipv6/inet6_connection_sock.c`     | `inet6_csk_bind_conflict`       | Excluded                                                                       | File not recorded in the file-level ARB CSV scope                         |
| 34622  | 2.6.39-rc6-00569-g5895198 | `net/core/skbuff.c`                    | `sock_queue_err_skb`            | Excluded                                                                       | Non-official/test version or unavailable official-release mapping         |

### MySQL Candidate Bug Filtering

The original file-level ARB resources contained 13 candidate aging-related MySQL bugs. During function-level dataset construction, each candidate bug was checked against version availability, official fix information, and the file-level ARB scope. The final function-level annotations were retained only when the affected version was available and the corresponding file/function evidence could be matched to the file-level ARB resources.

| Bug ID | Original/Related Version | Status                             | Final Version/File/Function or Filtering Reason                                                                                                     |
| ------ | ------------------------ | ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 34335  | 5.1.23                   | Excluded                           | The corresponding MySQL version is no longer downloadable or available.                                                                             |
| 32709  | before 5.1.29            | Excluded                           | The affected version range could not be mapped to an available downloadable version.                                                                |
| 33247  | before 5.1.24            | Excluded                           | The affected version range could not be mapped to an available downloadable version.                                                                |
| 38191  | 5.1.28, 6.0.6            | Excluded                           | The corresponding versions are no longer downloadable or available.                                                                                 |
| 40013  | fixed in 5.1.33          | Included                           | `5.1.32 / sql/sql_class.cc / THD::cleanup(void)`                                                                                                    |
| 40386  | 5.1.30                   | Included                           | `5.1.30 / storage/innobase/handler/ha_innodb.cc / ha_innobase::open()`; `5.1.30 / storage/innobase/handler/ha_innodb.cc / ha_innobase::close(void)` |
| 45989  | 5.1.43                   | Included with file-scope filtering | See details below.                                                                                                                                  |
| 46656  | 5.1.37                   | Included with file-scope filtering | See details below.                                                                                                                                  |
| 48993 | 5.1.45 | Included | `5.1.45 / client/mysqlbinlog.cc / main`; `5.1.45 / client/mysqlbinlog.cc / parse_args`; `5.1.45 / client/mysqlbinlog.cc / process_event`; `5.1.45 / sql/log_event.cc / Log_event::read_log_event`; `5.1.45 / sql/log_event.cc / Query_log_event::print_query_header`; `5.1.45 / sql/log_event.cc / Query_log_event::print` |
| 49535 | 5.1.45 | Included with extraction filtering | `5.1.45 / storage/innodb_plugin/log/log0recv.c / recv_scan_log_recs`. Another related function was excluded because it only appeared in `mem0mem.ic` and could not be reliably extracted as a function-level graph sample. |
| 52814 | 5.1.48 | Included | `5.1.48 / storage/innobase/handler/ha_innodb.cc / thd_to_trx(THD* thd)`; `5.1.48 / storage/innobase/handler/ha_innodb.cc / innobase_close_connection` |
| 56340 | 5.1.52 | Included | `5.1.52 / storage/innodb_plugin/row/row0mysql.c / row_update_for_mysql`; `5.1.52 / storage/innodb_plugin/row/row0mysql.c / row_update_statistics_if_needed` |
| 56709 | 5.1.48 | Included with file-scope filtering | `5.1.48 / sql/item_timefunc.cc / Item_extract::fix_length_and_dec()`; `5.1.48 / sql/item_timefunc.cc / Item_extract::val_int()`. Other mentioned files, including `dbug.c` and `sql_load.cc`, were excluded because `dbug.c` was outside the ARB component scope and `sql_load.cc` was not marked as an aging file in the file-level ARB resources. |

#### Details for MySQL Bug 45989

Bug 45989 was retained with file-scope filtering. The retained aging-related file is:

* `sql/sql_select.cc`

The retained functions are:

* `sub_select`
* `evaluate_join_record`
* `end_write_group`
* `copy_fields`
* `do_select`
* `JOIN::cleanup`
* `JOIN::destroy`
* `mysql_select`
* `mysql_explain_union`
* `JOIN::exec()`

The following mentioned files were excluded because they were not included as aging files in the file-level ARB ARFF resources:

* `field_conv.cc`
* `sql_string.cc`
* `sql_string.h`

#### Details for MySQL Bug 46656

Bug 46656 was retained with file-scope filtering. The retained file/function pairs are:

| File                               | Function                             |
| ---------------------------------- | ------------------------------------ |
| `storage/innobase/srv/srv0start.c` | `innobase_start_or_create_for_mysql` |
| `sql/sql_table.cc`                 | `mysql_rm_table_part2`               |
| `sql/sql_table.cc`                 | `mysql_rm_table`                     |
| `storage/innobase/ut/ut0mem.c`     | `ut_realloc`                         |
| `storage/innobase/ut/ut0mem.c`     | `ut_malloc`                          |
| `storage/innobase/ut/ut0mem.c`     | `ut_malloc_low`                      |
| `storage/innobase/pars/lexyy.c`    | `yylex`                              |
| `storage/innobase/pars/lexyy.c`    | `string_append`                      |
| `storage/innobase/pars/srv0srv.c`  | `srv_general_init`                   |
| `storage/innobase/pars/srv0srv.c`  | `srv_boot`                           |
| `storage/innobase/pars/mem0dbg.c`  | `mem_init`                           |
| `storage/innobase/pars/thr0loc.c`  | `thr_local_init`                     |
| `storage/innobase/row/row0mysql.c` | `row_drop_table_for_mysql`           |


The following involved files were excluded because they were not marked as aging files in the file-level ARB ARFF resources:

* `mem0pool.c`
* `pars0pars.c`
* `pars0grm.c`
* `que0que.c`
* `mem0mem.c`
* `hash0hash.c`
* `handler.cc`
* `sql_plugin.cc`
* `sql_parse.cc`
* `mysqld.cc`

### NetBSD Candidate Bug Filtering

For NetBSD, all candidate bugs used in this study correspond to NetBSD 7.99.21. The official problem reports/fix information provided sufficient function-level evidence for the corresponding faulty functions. Therefore, no candidate NetBSD bug was excluded during the function-level filtering process. The final NetBSD bug/function list is provided in the curated bug/function table below.

---

## Function-Level Positive Sample Annotation Rules

The function-level positive samples in this study were manually derived from file-level ARB resources and official bug/fix information. The annotation process followed the rules below.

1. **Start from file-level ARB resources.**
   Aging-related files were first identified from the component-level CSV/ARFF files used in prior file-level ARB studies. These files were treated as the initial candidate aging-file scope.

2. **Inspect official bug/fix documents.**
   For each candidate aging-related bug ID, the corresponding official bug report, fix record, patch description, and available contextual information were inspected using official project resources.

3. **Match official fixes with file-level aging files.**
   A candidate bug was retained only if its official fix information involved at least one file that was also marked as an aging-related file in the prior file-level ARB resources. If the official fix documents only referred to files outside the file-level aging-file scope, the candidate bug was excluded from the final function-level dataset.

4. **Identify aging-related functions.**
   For retained bugs, the official fix information were further examined to identify the specific functions involved in the aging-related behavior or fix. If the official documents clearly indicated the affected function(s), these functions were annotated as aging-related functions.

5. **Exclude bugs without function-level evidence.**
   If a candidate bug was associated with an aging-related file but the available official fix information did not provide sufficient evidence to localize the issue to one or more specific functions, the bug was excluded from the function-level positive sample set.

6. **Retain multiple functions when justified.**
   A single bug may correspond to multiple aging-related functions. When the official fix indicated that multiple functions were involved in the aging-related behavior or fix, all confirmed functions were retained as independent positive samples.

7. **Prefer reproducible official releases.**
   When a bug was only present in test, release-candidate, or non-official versions and could not be mapped to an officially released and downloadable version, it was excluded from the final dataset to improve reproducibility.

In summary, a function was annotated as an aging-related function only when all of the following conditions were satisfied:

* the corresponding file was included in the file-level aging-file scope from prior ARB studies;
* the candidate bug was supported by official bug/fix information;
* the official fix provided sufficient evidence to associate the aging-related behavior or fix with the function;
* the corresponding software version was available as an official release.

---

## Curated Function-Level Positive Samples

The final retained bug/function entries used for function-level data construction are listed below. The format is:

```text
Bug ID | Version | File | Function
```

### Linux

| Bug ID | Version  | File                                  | Function                        |
| ------ | -------- | ------------------------------------- | ------------------------------- |
| 1209   | 2.6.4    | `drivers/scsi/sym53c8xx_2/sym_glue.c` | `sym53c8xx_queue_command`       |
| 3171   | 2.6.12.6 | `fs/ext3/balloc.c`                    | `ext3_try_to_allocate_with_rsv` |
| 3431   | 2.6.12.6 | `fs/ext3/ialloc.c`                    | `ext3_new_inode`                |
| 3431   | 2.6.12.6 | `fs/ext3/inode.c`                     | `ext3_truncate`                 |
| 3699   | 2.6.9    | `drivers/scsi/aacraid/rkt.c`          | `aac_rkt_check_health`          |
| 3699   | 2.6.9    | `drivers/scsi/aacraid/rx.c`           | `aac_rx_check_health`           |
| 5284   | 2.6.12.6 | `drivers/net/r8169.c`                 | `rtl8169_rx_vlan_skb`           |
| 32832  | 2.6.38   | `net/ipv4/inet_connection_sock.c`     | `inet_csk_bind_conflict`        |

### MySQL

| Bug ID | Version | File                                    | Function                              |
| ------ | ------- | --------------------------------------- | ------------------------------------- |
| 40013  | 5.1.32  | `sql/sql_class.cc`                      | `THD::cleanup(void)`                  |
| 40386  | 5.1.30  | `storage/innobase/handler/ha_innodb.cc` | `ha_innobase::open()`                 |
| 40386  | 5.1.30  | `storage/innobase/handler/ha_innodb.cc` | `ha_innobase::close(void)`            |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `sub_select`                          |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `evaluate_join_record`                |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `end_write_group`                     |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `copy_fields`                         |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `do_select`                           |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `JOIN::cleanup`                       |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `JOIN::destroy`                       |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `mysql_select`                        |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `mysql_explain_union`                 |
| 45989  | 5.1.43  | `sql/sql_select.cc`                     | `JOIN::exec()`                        |
| 46656  | 5.1.37  | `storage/innobase/row/row0mysql.c`      | `row_drop_table_for_mysql`            |
| 46656  | 5.1.37  | `storage/innobase/srv/srv0start.c`      | `innobase_start_or_create_for_mysql`  |
| 46656  | 5.1.37  | `sql/sql_table.cc`                      | `mysql_rm_table_part2`                |
| 46656  | 5.1.37  | `sql/sql_table.cc`                      | `mysql_rm_table`                      |
| 46656  | 5.1.37  | `storage/innobase/ut/ut0mem.c`          | `ut_realloc`                          |
| 46656  | 5.1.37  | `storage/innobase/ut/ut0mem.c`          | `ut_malloc`                           |
| 46656  | 5.1.37  | `storage/innobase/ut/ut0mem.c`          | `ut_malloc_low`                       |
| 46656  | 5.1.37  | `storage/innobase/pars/lexyy.c`         | `yylex`                               |
| 46656  | 5.1.37  | `storage/innobase/pars/lexyy.c`         | `string_append`                       |
| 46656  | 5.1.37  | `storage/innobase/pars/srv0srv.c`       | `srv_general_init`                    |
| 46656  | 5.1.37  | `storage/innobase/pars/srv0srv.c`       | `srv_boot`                            |
| 46656  | 5.1.37  | `storage/innobase/pars/mem0dbg.c`       | `mem_init`                            |
| 46656  | 5.1.37  | `storage/innobase/pars/thr0loc.c`       | `thr_local_init`                      |
| 48993  | 5.1.45  | `client/mysqlbinlog.cc`                 | `main`                                |
| 48993  | 5.1.45  | `client/mysqlbinlog.cc`                 | `parse_args`                          |
| 48993  | 5.1.45  | `client/mysqlbinlog.cc`                 | `process_event`                       |
| 48993  | 5.1.45  | `sql/log_event.cc`                      | `Log_event::read_log_event`           |
| 48993  | 5.1.45  | `sql/log_event.cc`                      | `Query_log_event::print_query_header` |
| 48993  | 5.1.45  | `sql/log_event.cc`                      | `Query_log_event::print`              |
| 49535  | 5.1.45  | `storage/innodb_plugin/log/log0recv.c`  | `recv_scan_log_recs`                  |
| 52814  | 5.1.48  | `storage/innobase/handler/ha_innodb.cc` | `thd_to_trx(THD* thd)`                |
| 52814  | 5.1.48  | `storage/innobase/handler/ha_innodb.cc` | `innobase_close_connection`           |
| 56340  | 5.1.52  | `storage/innodb_plugin/row/row0mysql.c` | `row_update_for_mysql`                |
| 56340  | 5.1.52  | `storage/innodb_plugin/row/row0mysql.c` | `row_update_statistics_if_needed`     |
| 56709  | 5.1.48  | `sql/item_timefunc.cc`                  | `Item_extract::fix_length_and_dec()`  |
| 56709  | 5.1.48  | `sql/item_timefunc.cc`                  | `Item_extract::val_int()`             |

### NetBSD

| Bug ID | Version | File                                                    | Function               |
| ------ | ------- | ------------------------------------------------------- | ---------------------- |
| 50298  | 7.99.21 | `src/sys/kern/kern_exec.c`                              | `execve_runproc`       |
| 50300  | 7.99.21 | `src/sys/kern/kern_exit.c`                              | `proc_reparent`        |
| 50318  | 7.99.21 | `src/sys/kern/kern_synch.c`                             | `suspendsched`         |
| 50381  | 7.99.21 | `src/sys/fs/tmpfs/tmpfs_subr.c`                         | `tmpfs_construct_node` |
| 50577  | 7.99.21 | `src/usr.bin/msgs/msgs.c`                               | `ask`                  |
| 50578  | 7.99.21 | `src/usr.sbin/isdn/isdnd/exec.c`                        | `upd_callstat_file`    |
| 50579  | 7.99.21 | `src/usr.bin/error/filter.c`                            | `getignored`           |
| 50666  | 7.99.21 | `src/usr.sbin/lpr/pac/pac.c`                            | `dumpit`               |
| 50712  | 7.99.21 | `src/usr.bin/showmount/showmount.c`                     | `xdr_exports`          |
| 50713  | 7.99.21 | `src/usr.sbin/sysinst/disks.c`                          | `make_filesystems`     |
| 50713  | 7.99.21 | `src/usr.sbin/sysinst/disks.c`                          | `fsck_preen`           |
| 50714  | 7.99.21 | `src/usr.sbin/sysinst/savenewlabel.c`                   | `savenewlabel`         |
| 50730  | 7.99.21 | `src/sys/kern/kern_event.c`                             | `kqueue_scan`          |
| 50730  | 7.99.21 | `src/sys/kern/kern_event.c`                             | `knote_detach`         |
| 50897  | 7.99.21 | `src/usr.bin/elf2aout/elf2aout.c`                       | `saveRead`             |
| 50918  | 7.99.21 | `src/crypto/dist/ipsec-tools/src/racoon/isakmp_xauth.c` | `xauth_rmconf_dup`     |
| 50923  | 7.99.21 | `src/usr.bin/infocmp/infocmp.c`                         | `use_terms`            |
| 50924  | 7.99.21 | `src/usr.sbin/sup/source/scan.c`                        | `parserelease`         |
| 50925  | 7.99.21 | `src/usr.sbin/sup/source/cvt.c`                         | `main`                 |
| 51040  | 7.99.21 | `src/usr.sbin/makemandb/makemandb.c`                    | `read_and_decompress`  |

---

## Negative Sample Construction

Negative functions were sampled from non-aging files within the same component scope used by prior file-level ARB studies. To reduce file-level redundancy, at most one function was sampled from each non-aging file. The final positive-to-negative ratio was fixed to 1:5 for each dataset.

The final negative samples are included in the preprocessed `.pkl` graph datasets.

---

## Environment

The main experiments were conducted with the following environment:

| Dependency       | Version   |
| ---------------- | --------- |
| Python           | 3.10.9    |
| NumPy            | 1.23.5    |
| scikit-learn     | 1.2.1     |
| imbalanced-learn | 0.10.1    |
| PyTorch          | 2.4.0+cpu |
| Transformers     | 5.3.0     |
| Gensim           | 4.3.0     |

The scripts also use standard Python libraries, which are included with Python and do not require separate installation.

Joern was used to extract program graphs during data construction. Since the preprocessed `.pkl` graph datasets are included in this replication package, Joern is not required to reproduce the main experimental tables.

---

## Reproducing the Main Results

All data files, including. pkl and. json, as well as the code file. py, are included in the uploaded MSLR_LARB. zip compressed file. All main results are obtained with 5-fold cross-validation using a fixed threshold of 0.5.

### Dataset Files

The following preprocessed graph datasets are included:

```text
linux_joern_multirel_func.pkl
mysql_joern_multirel_func.pkl
netbsd_joern_multirel_func.pkl
```

To reproduce results on a specific software system, replace the `--data_path` argument with the corresponding `.pkl` file.

---

### Reproducing Table 2: Function-Level Local-Only Results

Table 2 reports the single-scale local-only results.

#### Local-50

Use:

```bash
--local_scales 50
--use_cfg 0
--use_cdg 0
```

Example:

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 0 \
  --use_cdg 0 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50
```

#### Local-100

Use:

```bash
--local_scales 100
--use_cfg 0
--use_cdg 0
```

Example:

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 0 \
  --use_cdg 0 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 100
```

For Linux or MySQL, replace `netbsd_joern_multirel_func.pkl` with `linux_joern_multirel_func.pkl` or `mysql_joern_multirel_func.pkl`.

---

### Reproducing Table 3: Effect of CFG/CDG on Single-Scale Local Modeling

Table 3 reports the effect of adding CFG/CDG under single-scale local settings.

#### Local-50 + CFG/CDG

Use:

```bash
--local_scales 50
--use_cfg 1
--use_cdg 1
```

Example:

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50
```

#### Local-100 + CFG/CDG

Use:

```bash
--local_scales 100
--use_cfg 1
--use_cdg 1
```

Example:

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 100
```

Each dataset in Table 3 contains four settings:

| Setting             | Arguments                                    |
| ------------------- | -------------------------------------------- |
| local-50            | `--local_scales 50 --use_cfg 0 --use_cdg 0`  |
| local-50 + cfg+cdg  | `--local_scales 50 --use_cfg 1 --use_cdg 1`  |
| local-100           | `--local_scales 100 --use_cfg 0 --use_cdg 0` |
| local-100 + cfg+cdg | `--local_scales 100 --use_cfg 1 --use_cdg 1` |

---

### Reproducing Table 4: Effect of Multi-Scale Local Modeling under CFG/CDG Enhancement

Table 4 reports the effect of multi-scale local modeling under fixed CFG/CDG enhancement.

#### Local-50 + CFG/CDG

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50
```

#### Local-100 + CFG/CDG

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 100
```

#### Local-[50,100] + CFG/CDG

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100
```

Each dataset in Table 4 contains three settings:

* local-50 + cfg+cdg;
* local-100 + cfg+cdg;
* local-[50,100] + cfg+cdg.

These settings are controlled only by changing `--local_scales` while keeping `--use_cfg 1` and `--use_cdg 1` fixed.

---

### Reproducing Table 5: Final Unified Framework across Datasets

Table 5 reports the final unified framework:

```text
local-[50,100] + CFG/CDG
```

Use:

```bash
--local_scales 50,100
--use_cfg 1
--use_cdg 1
```

#### Linux

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path linux_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100
```

#### MySQL

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path mysql_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100
```

#### NetBSD

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100
```

---

### Reproducing Table 6: Comparison with Representative Function-Level Bug/Vulnerability Prediction Models

Table 6 compares the proposed framework with Devign and CodeBERT.

#### Proposed Framework

Use the same commands as Table 5.

#### Devign Baseline

```bash
python devign_cfg_only_baseline.py --data_path linux_joern_multirel_func.pkl
python devign_cfg_only_baseline.py --data_path mysql_joern_multirel_func.pkl
python devign_cfg_only_baseline.py --data_path netbsd_joern_multirel_func.pkl
```

#### CodeBERT Baseline

```bash
python codebert_cv5_train.py --data_path CodeBERT/linux_codebert_with_code.jsonl
python codebert_cv5_train.py --data_path CodeBERT/mysql_codebert_with_code.jsonl
python codebert_cv5_train.py --data_path CodeBERT/netbsd_codebert_with_code.jsonl
```

For each dataset, Table 6 contains:

* Devign;
* CodeBERT;
* Ours, i.e., the final unified framework with local-[50,100] + CFG/CDG.

---

### Reproducing Table 7: Comparison with Representative Graph Encoders

Table 7 fixes the same framework and input setting:

```text
local-[50,100]
CFG/CDG enabled
```

Only the graph encoder is switched using `--usemodel`.

#### GCN

```bash
python train_multiscale_local_cfg_cdg_cv5_switchable.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100 \
  --usemodel GCN
```

#### GAT

```bash
python train_multiscale_local_cfg_cdg_cv5_switchable.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100 \
  --usemodel GAT
```

#### GraphSAGE

```bash
python train_multiscale_local_cfg_cdg_cv5_switchable.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100 \
  --usemodel GraphSAGE
```

#### Proposed Encoder

Use the Table 5 command:

```bash
python train_multiscale_local_cfg_cdg_cv5.py \
  --data_path netbsd_joern_multirel_func.pkl \
  --use_cfg 1 \
  --use_cdg 1 \
  --n_splits 5 \
  --threshold_mode fixed \
  --threshold 0.5 \
  --local_scales 50,100
```

For Linux or MySQL, replace the dataset path accordingly.

For each dataset, Table 7 contains:

* GCN;
* GAT;
* GraphSAGE;
* the proposed encoder.

All four settings use the same local-[50,100] input, CFG/CDG relation views, 5-fold cross-validation, and fixed threshold 0.5. They differ only in the encoder choice.

---

## Parameter Summary

### Change Dataset

Replace the `--data_path` argument with one of the following files:

```text
linux_joern_multirel_func.pkl
mysql_joern_multirel_func.pkl
netbsd_joern_multirel_func.pkl
```

### Change Local Scale

Use one of the following settings:

```bash
--local_scales 50
--local_scales 100
--local_scales 50,100
```

### Enable or Disable Relation Views

Use:

```bash
# local-only
--use_cfg 0 --use_cdg 0

# local + CFG/CDG
--use_cfg 1 --use_cdg 1
```

### Change Graph Encoder

For encoder comparison, use:

```bash
--usemodel GCN
--usemodel GAT
--usemodel GraphSAGE
```

with:

```text
train_multiscale_local_cfg_cdg_cv5_switchable.py
```
