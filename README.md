# Bitbucket Issues Migration

This is a small script that will migrate Bitbucket issues to a GitHub project.

It will import issues (and close them as needed) and their comments. Labels are
supported.

## Before running:

Requires Python 3 and a couple of libraries. It's probably a good idea to use
Python 3's built-in `venv` tool:

    $ pyvenv ./py3
    $ source ./py3/bin/activate
    $ pip3 install -r requirements.pip

## Example:

    $ python3 migrate.py -h

    usage: migrate.py [-h] [-n] [-f START]
                      bitbucket_username bitbucket_repo github_username
                      github_repo

    A tool to migrate issues from Bitbucket to GitHub.
    Note: The Bitbucket repository and issue tracker have to be public

    positional arguments:
      bitbucket_username    Your Bitbucket username
      bitbucket_repo        Bitbucket repository to pull issues from.
                            Format:
                            <user or organization name>/<repo name> Example:
                            jeffwidman/bitbucket-issue-migration
      github_username       Your GitHub username
      github_repo           GitHub repository to add issues to.
                            Format:
                            <user or organization name>/<repo name> Example:
                            jeffwidman/bitbucket-issue-migration

    optional arguments:
      -h, --help            show this help message and exit
      -n, --dry-run         Perform a dry run and print everything.
      -f START, --start_id START
                            Bitbucket issue ID from which to start the import

    $ python3 migrate.py -f 1 <bitbucket_username> <bitbucket_repo> <github_username> <github_repo>

## Additional notes:

* The GitHub repository can be owned by either an individual or an organization.

* In addition to normal label migration, this script also creates GitHub labels
that map to the Bitbucket issue type (bug, task, etc). If you don't want these,
just delete the new GitHub labels post-migration.

* The migrated issues and issue comments are annotated with both Bitbucket and
GitHub links to user who authored the comment/issue. This assumes the user
reused their Bitbucket username on GitHub.

* Within the body of issues and issue comments, hyperlinks to other issues
in this Bitbucket repo will be rewritten as `#<ID>`, which GitHub will
automatically hyperlink to the GitHub issue with that particular ID. This
assumes that you are migrating to a GitHub repository that has no existing
issues, otherwise the imported issues will have a different ID on GitHub than
on Bitbucket and the links will be incorrect. If you are migrating to a GitHub
repo with existing issues, just edit the code to offset the imported issue IDs
by the correct amount.

* This script is not idempotent--re-running it will leave the first set of
imported issues intact, and then create a duplicate set of imported issues after
the first set. If you want to re-run the import, it's best to delete your GitHub
repo and start over so that the GitHub issue IDs start from 1. Alternatively, if
an import breaks midway, you can restart from the breakage point using the
`--start_id` option.

* The maximum allowable size per individual issue is 1MB. This limit is
imposed by GitHub's
[Import API](https://gist.github.com/jonmagic/5282384165e0f86ef105).



Currently maintained by [Jeff Widman](http://www.jeffwidman.com/).
Originally written and open-sourced by [Vitaly Babiy](http://www.howsthe.com/).
