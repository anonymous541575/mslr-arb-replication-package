# Function-Level Aging-Related Bug Detection

This repository provides the anonymized replication package for the paper:

**Function-Level Aging-Related Bug Detection via Multi-Scale Local Structural Modeling and Relation-Specific Graph Views**

This study investigates **function-level aging-related bug (ARB) detection**. Existing ARB prediction studies mainly focus on file-level prediction, which can identify risky files but cannot directly localize the actual aging-related functions. To support finer-grained detection and analysis, this work advances ARB detection from the file level to the function level. The task is to determine whether a given function is an aging-related function.

The study constructs function-level ARB datasets for three software systems:

- Linux
- MySQL
- NetBSD

It also proposes a unified structural learning framework that uses multi-scale local program graphs as the main representation and enhances them with control flow graph (CFG) and control dependence graph (CDG) views. The framework jointly models function-body structure, execution-path organization, and control-dependence complexity.

This repository is anonymized for double-blind review. Author-identifying information will be added after acceptance.

---

## Repository Contents

This replication package includes:

- curated function-level bug/function annotations;
- data-construction and filtering rules;
- candidate bug filtering records;
- preprocessed graph datasets;
- training scripts for the proposed framework;
- baseline scripts for Devign, CodeBERT, GCN, GAT, and GraphSAGE;
- commands for reproducing the main experimental tables.

---

## Availability Note

This replication package includes the final curated bug/function lists and the data-construction metadata used in this study. It does not redistribute third-party papers.

For Linux and MySQL, the original prior study provides access information for the corresponding component-level CSV/ARFF files. Therefore, this package refers users to the original paper rather than redistributing those materials here.

For NetBSD, part of the preliminary metadata was obtained through private communication associated with the prior study *The Impact of Feature Selection Techniques on Aging-Related Bug Prediction Models: An Empirical Investigation*. This replication package does not redistribute those original files. Users interested in the original NetBSD metadata should contact the authors of that study directly. Instead, this package provides the processed function-level annotations and the rules used to construct the final dataset adopted in the present work.

---

## Data Sources

The preliminary component scope for the three software systems was derived from component-level CSV/ARFF files used in prior file-level ARB studies. Based on these files, this study determined the candidate component ranges for function-level data construction.

Candidate aging-related bug IDs were mainly collected from two previous ARB studies:

- **Linux and MySQL:** *Predicting Aging-Related Bugs Using Software Complexity Metrics*
- **NetBSD:** *The Impact of Feature Selection Techniques on Aging-Related Bug Prediction Models: An Empirical Investigation*

For these candidate bugs, official fix records and bug reports were further examined using the corresponding official project resources:

- Linux: `https://bugzilla.kernel.org/show_bug.cgi`
- MySQL: `https://bugs.mysql.com/`
- NetBSD: `https://www.netbsd.org/support/query-pr.html`

The final function-level bug/function list used in this study was manually derived from these sources.

---

## Final Dataset Statistics

| Dataset | Positive Functions | Negative Functions | Positive:Negative Ratio |
|---|---:|---:|---:|
| Linux | 8 | 40 | 1:5 |
| MySQL | 39 | 195 | 1:5 |
| NetBSD | 20 | 100 | 1:5 |

---

## Function-Level Positive Sample Annotation Rules

The function-level positive samples in this study were manually derived from file-level ARB resources and official bug/fix information. The annotation process followed the rules below.

1. **Start from file-level ARB resources.**  
   We first identified aging-related files from the component-level CSV/ARFF files used in prior file-level ARB studies. These files were treated as the initial candidate aging-file scope.

2. **Inspect official bug/fix documents.**  
   For each candidate aging-related bug ID, we inspected the corresponding official bug report, fix record, patch description, and available contextual information from the official project resources.

3. **Match official fixes with file-level aging files.**  
   A candidate bug was retained only if its official fix information involved at least one file that was also marked as an aging-related file in the prior file-level ARB resources. If the official fix documents only referred to files outside the file-level aging-file scope, the candidate bug was excluded from the final function-level dataset.

4. **Identify aging-related functions.**  
   For retained bugs, we further examined the official fix information and source-code context to identify the specific functions involved in the aging-related behavior or fix. If the official documents or patch context clearly indicated the affected function(s), these functions were annotated as aging-related functions.

5. **Exclude bugs without function-level evidence.**  
   If a candidate bug was associated with an aging-related file but the available official fix information did not provide sufficient evidence to localize the issue to one or more specific functions, the bug was excluded from the function-level positive sample set.

6. **Retain multiple functions when justified.**  
   A single bug may correspond to multiple aging-related functions. When the official fix and source-code context indicated that multiple functions were involved in the aging-related behavior or fix, all confirmed functions were retained as independent positive samples.

7. **Prefer reproducible official releases.**  
   When a bug was only present in test, release-candidate, or non-official versions and could not be mapped to an officially released and downloadable version, it was excluded from the final dataset to improve reproducibility.

In summary, a function was annotated as an aging-related function only when all of the following conditions were satisfied:

- the corresponding file was included in the file-level aging-file scope from prior ARB studies;
- the candidate bug was supported by official bug/fix information;
- the official fix or source-code context provided sufficient evidence to associate the aging-related behavior or fix with the function;
- the corresponding software version was available as an official release.

---

## Candidate Bug Filtering

The final function-level datasets were not constructed by directly accepting all candidate bug IDs from prior studies. Candidate bugs were checked against official bug/fix information, source-code context, software version availability, and the file-level aging-file scope from prior ARB studies.

A candidate bug/function entry was retained only when:

1. the affected file was within the file-level aging-file scope used by prior ARB studies;
2. the corresponding official bug/fix information was available;
3. the affected version could be mapped to an officially released and downloadable version;
4. the available fix information or source-code context provided sufficient function-level evidence.

Candidate entries were excluded when:

- the affected file was outside the file-level aging-file scope;
- the version was only a test, release-candidate, or non-official version without a suitable official-release mapping;
- the available documents did not provide sufficient function-level evidence.

---

### Linux Candidate Bug Filtering

| Bug ID | Original Version | Original File | Original Function | Final Status | Final Version/File/Function or Exclusion Reason |
|---|---|---|---|---|---|
| 1209 | 2.6.0-test5 | drivers/scsi/sym53c8xx_2/sym_glue.c | sym53c8xx_queue_command | Included with official-release mapping | 2.6.4 / drivers/scsi/sym53c8xx_2/sym_glue.c / sym53c8xx_queue_command |
| 2425 | 2.6.4 | Not clear | Not clear | Excluded | Insufficient file/function-level evidence |
| 3171 | 2.6.12.6 | fs/ext3/balloc.c | ext3_try_to_allocate_with_rsv | Included | 2.6.12.6 / fs/ext3/balloc.c / ext3_try_to_allocate_with_rsv |
| 3431 | 2.6.9-rc2-mm1 | fs/ext3/ialloc.c | ext3_new_inode | Included with official-release mapping | 2.6.12.6 / fs/ext3/ialloc.c / ext3_new_inode |
| 3431 | 2.6.9-rc2-mm1 | fs/ext3/inode.c | ext3_discard_reservation | Included with function adjustment based on official-release source/fix context | 2.6.12.6 / fs/ext3/inode.c / ext3_truncate |
| 3699 | 2.6.10-rc1 | drivers/scsi/aacraid/* | aac_rkt_check_health | Included with official-release mapping | 2.6.9 / drivers/scsi/aacraid/rkt.c / aac_rkt_check_health |
| 3699 | 2.6.10-rc1 | drivers/scsi/aacraid/* | aac_rx_check_health | Included with official-release mapping | 2.6.9 / drivers/scsi/aacraid/rx.c / aac_rx_check_health |
| 5137 | 2.6.12.5 | drivers/net/r8169.c | Not clear | Excluded | Insufficient function-level evidence |
| 5144 | 2.6.13 | Not clear | Not clear | Excluded | Insufficient file/function-level evidence |
| 5284 | 2.6.12.6 | drivers/net/r8169.c | rtl8169_rx_vlan_skb | Included | 2.6.12.6 / drivers/net/r8169.c / rtl8169_rx_vlan_skb |
| 5711 | 2.6.15-rc4-686 | Not clear | Not clear | Excluded | Non-official/test version and insufficient file/function-level evidence |
| 6043 | 2.6.15 | Not clear | sas_alloc_task | Excluded | Corresponding source file could not be reliably located |
| 6114 | 2.6.15 | drivers/ieee1394/sbp2.c | Not clear | Excluded | File not included in the file-level ARB CSV scope |
| 7718 | 2.6.22.5 | drivers/net/pcmcia/xircom_cb.c | Not clear | Excluded | Insufficient function-level evidence |
| 7718 | 2.6.22.5 | drivers/net/pcmcia/xircom_tulip_cb.c | Not clear | Excluded | Insufficient function-level evidence |
| 9468 | 2.6.24-rc3 | Not clear | Not clear | Excluded | Non-official/test version and insufficient file/function-level evidence |
| 11100 | 2.6.25.9 | drivers/net/via-velocity.c | Not clear | Excluded | Insufficient function-level evidence |
| 11377 | 2.6.27-rc3-git6 | drivers/net/mv643xx_eth.c | rxq_refill | Excluded | Non-official/test version or unavailable official-release mapping |
| 11377 | 2.6.27-rc3-git6 | drivers/net/mv643xx_eth.c | mv643xx_eth_poll | Excluded | Non-official/test version or unavailable official-release mapping |
| 11937 | 2.6.28-rc2 | fs/jbd/log.c | __log_wait_for_space | Excluded | Corresponding file not available under the target source tree |
| 13293 | 2.6.30-rc4 | Not clear | Not clear | Excluded | Non-official/test version and insufficient file/function-level evidence |
| 20882 | 2.6.36.1 | drivers/net/r8169.c | Not clear | Excluded | Insufficient function-level evidence |
| 32832 | 2.6.38 | net/ipv4/inet_connection_sock.c | inet_csk_bind_conflict | Included | 2.6.38 / net/ipv4/inet_connection_sock.c / inet_csk_bind_conflict |
| 32832 | 2.6.38 | net/ipv6/inet6_connection_sock.c | inet6_csk_bind_conflict | Excluded | File not recorded in the file-level ARB CSV scope |
| 34622 | 2.6.39-rc6-00569-g5895198 | net/core/skbuff.c | sock_queue_err_skb | Excluded | Non-official/test version or unavailable official-release mapping |

---

### MySQL Candidate Bug Filtering

The original file-level ARB resources contained 13 candidate aging-related MySQL bugs. During function-level dataset construction, each candidate bug was further checked against version availability, official fix information, source-code context, and the file-level ARB scope. The final function-level annotations were retained only when the affected version was available and the corresponding file/function evidence could be matched to the file-level ARB resources.

| Bug ID | Original/Related Version | Status | Final Version/File/Function or Filtering Reason |
|---|---|---|---|
| 34335 | 5.1.23 | Excluded | The corresponding MySQL version is no longer downloadable/available. |
| 32709 | before 5.1.29 | Excluded | The affected version range could not be mapped to an available downloadable version. |
| 33247 | before 5.1.24 | Excluded | The affected version range could not be mapped to an available downloadable version. |
| 38191 | 5.1.28, 6.0.6 | Excluded | The corresponding versions are no longer downloadable/available. |
| 40013 | fixed in 5.1.33 | Included | 5.1.32 / sql/sql_class.cc / THD::cleanup(void) |
| 40386 | 5.1.30 | Included | 5.1.30 / storage/innobase/handler/ha_innodb.cc / ha_innobase::open(); 5.1.30 / storage/innobase/handler/ha_innodb.cc / ha_innobase::close(void) |
| 45989 | 5.1.43 | Included with file-scope filtering | Retained functions in sql/sql_select.cc: sub_select; evaluate_join_record; end_write_group; copy_fields; do_select; JOIN::cleanup; JOIN::destroy; mysql_select; mysql_explain_union; JOIN::exec(). Other mentioned files, including field_conv.cc, sql_string.cc, and sql_string.h, were excluded because they were not included as aging files in the file-level ARB ARFF resources. |
| 46656 | 5.1.37 | Included with file-scope filtering | Retained functions: storage/innobase/srv/srv0start.c / innobase_start_or_create_for_mysql; sql/sql_table.cc / mysql_rm_table_part2; sql/sql_table.cc / mysql_rm_table; storage/innobase/ut/ut0mem.c / ut_realloc; storage/innobase/ut/ut0mem.c / ut_malloc; storage/innobase/ut/ut0mem.c / ut_malloc_low; storage/innobase/pars/lexyy.c / yylex; storage/innobase/pars/lexyy.c / string_append; storage/innobase/pars/srv0srv.c / srv_general_init; storage/innobase/pars/srv0srv.c / srv_boot; storage/innobase/pars/mem0dbg.c / mem_init; storage/innobase/pars/thr0loc.c / thr_local_init; storage/innobase/row/row0mysql.c / row_drop_table_for_mysql. Other involved files, including mem0pool.c, pars0pars.c, pars0grm.c, que0que.c, mem0mem.c, hash0hash.c, handler.cc, sql_plugin.cc, sql_parse.cc, mysqld.cc, and other non-matched files, were excluded because they were not marked as aging files in the file-level ARB ARFF resources. |
| 48993 | 5.1.45 | Included | 5.1.45 / client/mysqlbinlog.cc / main; 5.1.45 / client/mysqlbinlog.cc / parse_args; 5.1.45 / client/mysqlbinlog.cc / process_event; 5.1.45 / sql/log_event.cc / Log_event::read_log_event; 5.1.45 / sql/log_event.cc / Query_log_event::print_query_header; 5.1.45 / sql/log_event.cc / Query_log_event::print |
| 49535 | 5.1.45 | Included with extraction filtering | 5.1.45 / storage/innodb_plugin/log/log0recv.c / recv_scan_log_recs. Another related function was excluded because it only appeared in mem0mem.ic and could not be reliably extracted as a function-level graph sample. |
| 52814 | 5.1.48 | Included | 5.1.48 / storage/innobase/handler/ha_innodb.cc / thd_to_trx(THD* thd); 5.1.48 / storage/innobase/handler/ha_innodb.cc / innobase_close_connection |
| 56340 | 5.1.52 | Included | 5.1.52 / storage/innodb_plugin/row/row0mysql.c / row_update_for_mysql; 5.1.52 / storage/innodb_plugin/row/row0mysql.c / row_update_statistics_if_needed |
| 56709 | 5.1.48 | Included with file-scope filtering | 5.1.48 / sql/item_timefunc.cc / Item_extract::fix_length_and_dec(); 5.1.48 / sql/item_timefunc.cc / Item_extract::val_int(). Other mentioned files, including dbug.c and sql_load.cc, were excluded because dbug.c was outside the ARB component scope and sql_load.cc was not marked as an aging file in the file-level ARB resources. |

---

### NetBSD Candidate Bug Filtering

For NetBSD, all candidate bugs used in this study correspond to NetBSD 7.99.21. The official problem reports/fix information provided sufficient function-level evidence for the corresponding faulty functions. Therefore, no candidate NetBSD bug was excluded during the function-level filtering process. The final NetBSD bug/function list is provided in the curated bug/function table.

---

## Curated Positive Bug/Function List

The following tables list the final aging-related functions used for function-level dataset construction.

Format:

```text
Bug ID | Version | File | Function
