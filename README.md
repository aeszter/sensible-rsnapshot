# sensible-rsnapshot
## A better way to invoke rsnapshot

Rsnapshot is [flawed](http://www.backupcentral.com/forum/18/226122), claims Espen. 
I've stumbled upon a problem he does not directly mention: when a backup run fails, it won't be retried until (ana)cron decides to run the job again. This should not be a problem for hourly runs, but can become troublesome for the weeklies and monthlies. Even worse, cron runs might be correlated with backup failures: when anacron tends to run your backup job two minutes before you connect the external hard drive, you might be without any long-term backup while merrily retiring old hourlies.

I am convinced a better solution will consider all backup lines (including weekly and monthly runs) at each invocation (say, hourly). Here's a proposition: for each line, compare the time stamp of the most recent copy to the oldest copy in the next (more frequent) line. If the difference is sufficiently large, perform a migration.

In other words: if the oldest daily backup is more recent than the latest weekly by at least a week, call rsnapshot weekly to move that copy over.

The script goes into /etc/cron.hourly, and this is the only place rsnapshot is called -- no more fiddling around with half a dozen cron entries.

A word of **caution**: There may be mistakes in the script, and since it directly impacts on your backups, mistakes mean danger to your data. So, be careful, and do not trust the code blindly.
