# uos – Basic "Operating System" Services
The uos module contains functions for filesystem access and urandom function.

### Port Specifics
The filesystem has / as the root directory and the available physical drives are accessible from here. They are currently:

- ``/flash`` – the internal flash filesystem

- ``/sd`` – the SD card (if it exists)

### Functions

#####<function>uos.uname()</function>

Return information about the system, firmware release version, and micropython interpreter version.

#####<function>uos.chdir(path)</function>

Change current directory.

#####<function>uos.getcwd()</function>

Get the current directory.

#####<function>uos.listdir([dir])</function>

With no argument, list the current directory. Otherwise list the given directory.

#####<function>uos.mkdir(path)</function>

Create a new directory.

#####<function>uos.remove(path)</function>

Remove a file.

#####<function>uos.rmdir(path)</function>

Remove a directory.

#####<function>uos.rename(old_path, new_path)</function>

Rename a file.

#####<function>uos.stat(path)</function>

Get the status of a file or directory.

The return value is a tuple with the following 10 values, in order:

- ``st_mode``: protection bits.
- ``st_ino``: inode number. (not implemented, returns 0)
- ``st_dev``: device. (not implemented, returns 0)
- ``st_nlink``: number of hard links. (not implemented, returns 0)
- ``st_uid``: user id of owner. (not implemented, returns 0)
- ``st_gid``: group id of owner. (not implemented, returns 0)
- ``st_size``: size of file in bytes.
- ``st_atime``: time of most recent access.
- ``st_mtime``: time of most recent content modification.
- ``st_ctime``: time of most recent metadata change.

#####<function>uos.getfree(path)</function>

Returns the free space (in KiB) in the drive specified by path.

#####<function>uos.sync()</function>

Sync all filesystems.

#####<function>uos.urandom(n)</function>

Return a bytes object with n random bytes.

#####<function>uos.unlink(path)</function>

Alias for the remove() method.

#####<function>uos.mount(block_device, mount_point, * , readonly=False)</function>

Mounts a block device (like an SD object) in the specified mount point. Example:

```python
os.mount(sd, '/sd')
uos.unmount(path)
```

Unmounts a previously mounted block device from the given path.

#####<function>uos.mkfat(block_device)</function>

Instantiate a VFS (Virtual File System) object with underlying FAT file system. 


Example:

```python
from machine import SD
import os
sd = SD()
vfs = os.mkfat(sd)	# Creating a VFS 
vfs.mkfs(sd)     		# Formating the SD card
# Now we can use normal os mount
os.mount(vfs, '/sd')

```

#####<function>uos.fsformat(path)</function>

Formats the block device mounted under the input path, must be either ``/flash`` or ``/sd``


#####<function>uos.dupterm(stream_object)</function>

Duplicate the terminal (the REPL) on the passed stream-like object. The given object must at least implement the ``read()`` and ``write()`` methods.

### Constants
<constant>uos.sep</constant>

Separation character used in paths
