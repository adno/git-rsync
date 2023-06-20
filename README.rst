git-rsync
#########

Synchronize git repositories via rsync.

Use cases:

- You do not want to litter your commit history with minor tweaks, debugging etc., but you need to sync them between your local and remote working environment.
- You want to sync files that are untracked (or too large to be handled with services like Github): e.g. debugging outputs, large models etc.

This fork adds features to the original `git-rsync <https://github.com/de9uch1/git-rsync>`_ by `de9uch1 <https://github.com/de9uch1>`_, namely syncing only updated files or files specified as arguments.

Installation
============

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

All configurations are set via :code:`git config`.

The remote host and path are set by :code:`rsync.remote`.

.. code:: bash

   % cd your_git_repository/
   % git config --local rsync.remote "your_server_name:/path/to/remote/repository"

If you want to sepecify the identity file or the login name, set :code:`rsync.rsh`.

.. code:: bash

   % git config --local rsync.rsh "ssh -i ~/.ssh/id_rsa -l your_login_name"

Usage
=====

This tool syncs repositories A and B (the one on the local host and the one on the remote host), which typically have the same "remote repository" (in git terminology) C. We do not interact with C in any way.

Examples:

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

Full help:

.. code:: bash
  
   % git rsync -h

Note that excluded files are set by :code:`.gitignore`.

License
=======

This software is released under the MIT License.
