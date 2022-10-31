---
permalink: /exporting-whatsapp-media-from-iphone-backup
type: posts
---

# Bulk export of all Whatsapp Media files from an iPhone backup

This works more generally to extract any file from an iPhone, but my use case was that I needed to take out all Whatsapp Media history from my iPhone and the path wasn't clear, so I came up with this.


Steps:

1. Make a complete, non-encrypted backup of your iPhone on your mac
2. Navigate to where your backup is (~/Library/Application Support/MobileSync/Backup holds all backups, you need to select the most recent one)
3. Now you have found all files that are currently on your iPhone, hooray!!, BUT they are not properly named. Luckily for you and me, there is a Manifest.db file (which is an sqlite3 database file) where the original names are. You can do the following to explore it:

```
$ sqlite3 Manifest.db
> .tables
> .schema Files
(etc)
```

Or you can take advantage of the fact that I already did, and first create the file 'whatsapp-media-exporter.sql' with the following content:

```
-- This query extracts a list of (source filepath, destination filepath) pairs, one per line.
-- Paths are relative and consider the '~/Library/Application Support/MobileSync/Backup/{backupid}/' folder as root

select substr(fileID, 1, 2)||'/'||fileID origin
     , 'Whatsapp-export/'||relativePath destination
from Files
where domain = 'AppDomainGroup-group.net.whatsapp.WhatsApp.shared'
  and relativePath like 'Message/Media/%'
  and flags = 1 -- this seems like 1 is file, 2 is folder, 4 is something else
order by destination;
```

And then secondly execute this command (again assuming that you're working from the base directory of the backup):

```
$ mkdir ~/Downloads/Whatsapp-export
$ sqlite3 Manifest.db < whatsapp-media-exporter.sql | awk -F '|' '{print $1 " " $2}' | xargs -L1 sh -c 'cp $0 ~/Downlads/Whatsapp-export/`basename $1`';
```

Note: I had some sort of issue (I think with file permissions) with writing to $1 at the end of the command, that's why I decided to throw away the folder structure. With some more time you could mkdir the `dirname $1` prior to cp'ing $0 to $1. But I'm done with this :)
