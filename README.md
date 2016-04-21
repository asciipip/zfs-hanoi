zfs-hanoi
=========

This is a Python script that creates and manages a series of ZFS
snapshots which are rotated according to the Towers of Hanoi backup
scheme.


The Towers of Hanoi Scheme
--------------------------

The Tower of Hanoi backup scheme has that name because of its
similarity to the recursive solution for the puzzle of the same name.
It has the effect that it keeps multiple recent backups available
while also keeping some older backups in a very space-efficient
manner.  It more or less works like this:

 * Use your first backup tape on alternating days.
 * Use your second backup tape alternating on the remaining days.
 * Use your third backup tape alternating on the remaining days.
 * etc.

The more tapes you use, the longer it is between uses of your
highest-order tape, and so the further back in time you can go.
Conversely, you always have at least the last two backups available.
(And three out of the last four backups, and four out of the last
eight backups, and so on.)

For example, if you have five tapes labeled "A", "B", "C", "D", and "E",
then after the 22nd daily backup, the tapes would have the following
days' data on them:

 * A: 21
 * B: 22
 * C: 20
 * D: 8
 * E: 16

For more information, please see Wikipedia:

  https://en.wikipedia.org/wiki/Backup_rotation_scheme#Tower_of_Hanoi
 
This script uses snapshots instead of tapes, and it uses an
effectively unlimited number of snapshots; every time the Towers of
Hanoi backup scheme could use another tape, the script adds a
snapshot.


Requirements
------------

The script should work with Python versions as old as 2.4.  It has
been tested with Python 2.6.  No Python modules are necessary.

The ZFS command line programs should be available.


Usage
-----

The script's `--help` parameter should give a reasonable summary of
its parameters.  It must be given a list of ZFS datasets to work with.
Options of note are:

 * `-r`, `--recursive`: Apply snapshots to children of the provided
   datasets, too.
 * `-p`, `--prefix`: Use a different string to use at the beginning of
   snapshots generated by the script.  The default is "hanoi".  If you
   change the prefix after running this script, it will ignore all of
   the old snapshots.
 * `-m`, `--keep-min`: The minimum number of consecutive snapshots to
   retain.  The Towers of Hanoi scheme always keeps at least the last
   two snapshots, but the third-most-recent snapshot isn't always
   available.  This option lets you tweak that.  For example, if you
   take snapshots every day, `-m 14` will give you a week's worth of
   daily snapshots before they start getting pruned.