git-rsync
#########

Synchronize git repositories via rsync.

Use cases:

- You do not want to litter your commit history with minor tweaks, debugging etc., but you need to sync them between your local and remote working environment.
- You want to sync files that are untracked (or too large to be handled with services like Github): e.g. debugging outputs, large models etc.

This fork adds features to the original `git-rsync <https://github.com/de9uch1/git-rsync>`_ by `de9uch1 <https://github.com/de9uch1>`_, namely syncing only updated files or files specified as arguments.

Installation
============

You only need to place the git-rsync file in your :code:`PATH` and make sure it's exacutable. Here is one way to do that:

Clone the repository and create the symlink in a directory contained in the PATH variable.

.. code:: bash

   % git clone https://github.com/adno/git-rsync.git
   % cd git-rsync/
   % ln -s $(pwd)/git-rsync ~/.local/bin/

If you use Bash, add the direcotry to :code:`PATH` by the following:

.. code:: bash

   % [[ ":${PATH}" != *:"$HOME/.local/bin":* ]] && echo "$HOME/.local/bin:$PATH" >> ~/.bash_profile
   % source ~/.bash_profile

Setup
=====

All options are set via :code:`git config`.

The remote host and path are set by :code:`rsync.remote`.

.. code:: bash

   % cd your_git_repository/
   % git config --local rsync.remote "your_server_name:/path/to/remote/repository"

If you want to specify the identity file or the login name, set :code:`rsync.rsh`.

.. code:: bash

   % git config --local rsync.rsh "ssh -i ~/.ssh/id_rsa -l your_login_name"

To configure globally (per user), without repository-specific settings use:
  
.. code:: bash

   % git config --global rsync.remoteprefix your_remote_host:/pathprefix
   % git config --global rsync.rsh "ssh -i <indentity_file> -l <login_name>"

The remote path is then composed as :code:`rsync.remoteprefix/local_repo_name`.
Only the last component of the local repo path is used as :code:`local_repo_name`.


Usage
=====

This tool syncs repositories A and B (the one on the local host and the one on the remote host), which typically have the same "remote repository" (in git terminology) C. We do not interact with C in any way.

**Examples:**

- Sync (pull) the whole repository from the remote host:

  .. code:: bash
  
     % cd your_git_repository/
     % git rsync pull
   
- Sync (push) the whole repository to the remote host:

  .. code:: bash
    
    % cd your_git_repository/
    % git rsync push

- Sync (push) only added, modified, and untracked files since the last commit to the remote host:

  .. code:: bash
  
     % cd your_git_repository/
     % git rsync push -u

  **Note:** For larger repositories, syncing only files updated since the last commit is significantly faster (and often just what you need). For consistent results, however, it requires both the local and the remote repository to be up to date except for uncommited/untracked changes (e.g. by using :code:`git pull` on repository B after you :code:`commit` and :code:`push` on repository A).

- Check which files will be transferred, without actually syncing, with the :code:`-n` option:

  .. code:: bash
  
     % git rsync push -n

Note: Options (e.g. :code:`-u` or :code:`-n`) and commands (:code:`push` and :code:`pull`) are fully orthogonal.

**Full help:**

.. code::
  
   % git rsync -h
   git-rsync v0.1 (adno)
   
   Usage: git rsync [-nusf] [-x <pattern>] <command> [files]
   
     Synchronize the git repository via rsync.
   
     Remote host and path are set via git config. Local (per-repository)
     configuration can be done in the following way:
   
   	git config --local rsync.remote your_remote_host:/path/to/remote/repo
   
     If you transfer files via the SSH connection, it can be load the SSH
     config / SSH agent, or specify the login name and private key, like this:
   
   	git config --local rsync.rsh "ssh -i <indentity_file> -l <login_name>"
   
     To configure globally (per user), without repository-specific settings use:
     
   	git config --global rsync.remoteprefix your_remote_host:/pathprefix
   	git config --global rsync.rsh "ssh -i <indentity_file> -l <login_name>"
   
     The remote path is then composed as rsync.remoteprefix/local_repo_name.
     Only the last component of the local repo path is used as local_repo_name.
     A local rsync.remote setting overrides any rsync.remoteprefix setting.
   
     Excluded files are set automatically by .gitignore (unless individual
     file arguments are supplied). You can also configure files to be excluded at all
     times using rsync.exclude in local or global config, e.g.:
     
   	  git config --global rsync.exclude ".DS_Store __pycache__"
     
     If the command is "pull", the ignored files or diff/untracked files are
     queried from the remote host over SSH (which may result in an additional
     password prompt).
     
     Typical usage examples:
   
   	  git rsync -u pull       -- pull updated files from the remote host
   	  git rsync -u push       -- push updated files to the remote host
   	  git rsync push X Y      -- push specific files (possibly ignored by git)
   	  git rsync pull -f X Y   -- pull specific files, overwriting any newer local ones
   
   Options:
     -n, --dry-run
   			  Dry run.
     -u, --update
   			  Push or pull added, modified, and untracked files since the
   			  last commit (mimicks "git status").
     -s, --staged, -c, --cached
   			  Push or pull added and modified files staged for commit
   			  (mimicks output "git diff --cached").
     -f, --force
   			  Force overwrite newer files. (By default files that are
   			  newer on the receiver are skipped during sync.)
     -x, --exclude <pattern>
   			  Exclude files matching <pattern>. (Can be used multiple times.)
   
   Arguments:
     <command>   push, pull
     [files]     Push or pull individual files instead of the whole repository.
   			  If directories are supplied, sync them recursively. Honors -x,
   			  rsync.exclude, ignores .gitignore, and cannot be used with -s or -u.
   
   Known issues:
     The following (harmless) error message may appear:
     
   	  Pseudo-terminal will not be allocated because stdin is not a terminal.
   

License
=======

This software is released under the MIT License.
