---
title: DVC get Error
---

Reproduction:

```
# ~/workspace/test_NER_Error_Recovery
dvc get --rev b302dfe . dvc_model/training.log -o /tmp/training.log  # errored. b302dfe is a commit from dvc exp run
dvc get --rev b36aa17 . dvc_model/training.log -o /tmp/training.log  # successful
```


```bash
2021-04-05 12:36:15,473 DEBUG: Creating external repo .@b302dfe
2021-04-05 12:36:15,474 DEBUG: erepo: git clone '.' to a temporary dir
2021-04-05 12:36:16,170 DEBUG: Preparing to download data from 'ssh://cwhsu@140.109.19.70/mnt/d/dvc_storage/ner_error_recovery'
2021-04-05 12:36:16,170 DEBUG: Preparing to collect status from ssh://cwhsu@140.109.19.70/mnt/d/dvc_storage/ner_error_recovery
2021-04-05 12:36:16,170 DEBUG: Collecting information from local cache...
2021-04-05 12:36:16,172 DEBUG: Collecting information from remote cache...
2021-04-05 12:36:16,172 DEBUG: Matched '0' indexed hashes
2021-04-05 12:36:16,174 DEBUG: Establishing ssh connection with '140.109.19.70' through port '22' as user 'cwhsu'
2021-04-05 12:36:16,598 WARNING: Some of the cache files do not exist neither locally nor on remote. Missing cache files:
name: dvc_model, md5: 6ef97703e078147f15e74ec2de83db9c.dir
2021-04-05 12:36:16,601 DEBUG: Removing '/tmp/.6QvK4G4jAGxTWkQJ5SPPQg'
2021-04-05 12:36:16,622 ERROR: unexpected error - 'NoneType' object has no attribute 'trie'
------------------------------------------------------------
Traceback (most recent call last):
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/main.py", line 55, in main
    ret = cmd.run()
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/command/get.py", line 31, in run
    return self._get_file_from_repo()
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/command/get.py", line 37, in _get_file_from_repo
    Repo.get(
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/repo/get.py", line 55, in get
    repo.repo_fs.download(
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/fs/base.py", line 277, in download
    if not _only_file and self.isdir(from_info):
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/fs/repo.py", line 181, in isdir
    meta = dvc_fs.metadata(path)
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/fs/dvc.py", line 224, in metadata
    meta.isdir = meta.isdir or self.check_isdir(meta.path_info, meta.outs)
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/fs/dvc.py", line 118, in check_isdir
    self._get_granular_hash(path_info, out)
  File "/home/cwhsu/anaconda3/envs/dvc/lib/python3.9/site-packages/dvc/fs/dvc.py", line 51, in _get_granular_hash
    obj = out.obj.trie.get(key)
AttributeError: 'NoneType' object has no attribute 'trie'
------------------------------------------------------------
2021-04-05 12:36:16,832 DEBUG: Version info for developers:
DVC version: 2.0.17 (conda)
---------------------------------
Platform: Python 3.9.2 on Linux-4.4.0-140-generic-x86_64-with-glibc2.23
Supports: http, https, ssh
Cache types: hardlink, symlink
Cache directory: ext4 on /dev/sdb1
Caches: local
Remotes: ssh, ssh
Workspace directory: ext4 on /dev/sdb1
Repo: dvc, git

Having any troubles? Hit us up at https://dvc.org/support, we are always happy to help!
2021-04-05 12:36:16,836 DEBUG: Analytics is enabled.
2021-04-05 12:36:16,905 DEBUG: Trying to spawn '['daemon', '-q', 'analytics', '/tmp/tmp_n9lsh9t']'
2021-04-05 12:36:16,907 DEBUG: Spawned '['daemon', '-q', 'analytics', '/tmp/tmp_n9lsh9t']'
```

**Possible causes:**
 - always download from the remote cache instead of local one, which does not have the committed results yet in the remote
 - no results even in the local one because the results have not been committed when using `dvc exp run --no-commit`
