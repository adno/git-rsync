git-rsync
#########

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

Examples:

- Sync whole repository from the remote host:

  .. code:: bash
  
     % cd your_git_repository/
     % git rsync pull
   
- Sync whole repository to the remote host:

  .. code:: bash
    
    % cd your_git_repository/
    % git rsync push -u

- Sync added, modified, and untracked files since the last commit to the remote host:

  .. code:: bash
  
     % cd your_git_repository/
     % git rsync push -u

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
