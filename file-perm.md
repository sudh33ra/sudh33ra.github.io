# Taking backups of file permissions and restoring them

By using a getfacl command we can take a snapshot of file permissions :

`getfacl -R /var/www/myweb > permissions.acl`

This will backup permissions of all files and directories within /var/www/myweb and myweb directory itself. -R ensures that the /var/www/myweb will be traversed recursively to include all files and directories.

Now, that we have set of permissions stored in a single file permissions.acl we can restore them by:

`setfacl --restore=permissions.acl`

Since file permissions.acl contains a full path to all files and directories within /var/www/myweb there is no need to specify where a path to where permissions should be restored.
