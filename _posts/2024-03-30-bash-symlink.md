## How to Create a Global Symlink in Bash

A symbolic link in Linux and other Unix-like systems (such as MacOS) is essentially a shortcut that points to the original file, allowing a user to create a symlink and place it in a more convenient directory. You can even create a symlink and put it in a directory that gives it global access from any directory on the system, which is what we're going to do now.

Having a globally available symlink is incredibly useful if, like me, you have a custom script that you want to be able to access from anywhere, but you don't want to place the files in the /usr/local/bin directory for certain reasons.

### The ln command

Navigate to the directory where your script is, and use the following command (with your own file names) to create a symlink:
```bash
ln -s source_file /usr/local/bin/link_name
```
The directory /usr/local/bin is the place where user-space programs are kept that are not managed by a package manager. A common mistake is to place a file or program in /usr/bin, which could potentially cause problems due your package manager upgrading those programs and modifying your locally compiled program.
The -s option specifies that a symbolic link is being created. A hard link will be created if that option is omitted. The name for the symlink is optional, and the link will have the same name as the original file if that is omitted.

### Elevate permissions

After placing any type of file in the /usr/local/bin directory, you typically need to have super-user permissions in order to access it. Since no one wants to type in "sudo" every time they need to call the symlink, let's change the permissions for the symbolic link we just created.
```bash
sudo chmod 700 /usr/local/bin/link_name
```
Now we'll be able to access that file from anwhere in the file system.

### Deleting symlinks

If you ever need to delete a symlink, you can do so just like deleting any other type of file:
```bash
rm <symlink_name>
```

### Finishing up

That's the basics of symbolic links and how to make them accessible anywhere.
To learn more about the ln command, check out its man page:
```bash
man ln
```
