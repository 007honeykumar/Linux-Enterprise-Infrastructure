# 10 · Backup with rsync

![Backup flow](../diagrams/05-rsync-backup-flow.svg)

## What rsync is

`rsync` synchronises files efficiently. It can copy only changed data and preserve useful metadata.

## Why it was used

Project data from Server1 was copied to Server2 so that the backup did not exist only on the source host.

## Source and destination

```text
Source:      Server1 ~/project-data/
Destination: Server2 /backup/server1/
Transport:   SSH
```

Representative command:

```bash
rsync -avz ~/project-data/ \
  ec2-user@172.31.42.133:/backup/server1/
```

The exact command used in the lab should be saved in:

```text
configs/backup/rsync-command.txt
```

## Testing

```bash
ls -la ~/project-data
ssh ec2-user@172.31.42.133
ls -la /backup/server1
```

Run the transfer again after changing a file and observe that rsync processes only differences.

## Problems encountered

Errors included:

```text
chgrp failed
mkstemp Permission denied
```

The destination directory was not writable by `ec2-user`.

Fix:

```bash
sudo chown -R ec2-user:ec2-user /backup/server1
```

## Lesson

A successful SSH login does not guarantee write access to the destination directory. Backup verification must confirm both transfer success and readable destination files.
