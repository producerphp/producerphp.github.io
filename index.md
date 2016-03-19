---
layout: site
---

# Producer

Producer is a command-line tool to validate, and then release, your PHP library package. It supports Git and Mercurial for version control, as well as Github, Gitlab, and Bitbucket for remote origins.

## Installing

Producer works in concert with [Composer](https://getcomposer.org). Install it first.

Then add `$COMPOSER_HOME/vendor/bin` to your `$PATH` ([instructions here](https://getcomposer.org/doc/03-cli.md#global)).

Finally, issue `composer global require producer/producer ~1.0` to install Producer.

To test the installation, issue `producer` at the command line to see some "help" output.

## Configuring

Before you get going, you'll need to create a `~/.producer/config` file. Copy and paste the following into your terminal:

```
mkdir ~/.producer

echo "; Github
github_username =
github_token =
; Gitlab
gitlab_token =
; Bitbucket
bitbucket_username =
bitbucket_password =" > ~/.producer/config
```

You can then edit `~/.producer/config` to enter your access credentials, any or all of:

- [Github personal API token](https://github.com/settings/tokens),
- [Gitlab private token](https://gitlab.com/profile/account), or
- Bitbucket username and password.

> WARNING: Saving your username and password for Bitbucket in plain text is not very secure. Bitbucket doesn't have personal API tokens, so it's either "username and password" or bring in an external OAuth1 library with all its dependencies just to authenticate to Bitbucket. The latter option might show up in a subsequent release.

## Getting Started

Now that you have Producer installed and configured, change to the directory for your library package repository. From there, you can call the following commands:

- `producer issues` will show all the open issues from the remote origin
- `producer phpdoc` will check the PHP docblocks in the `src/` directory
- `producer validate <version>` will validate the package for release, but won't actually release it
- `producer release <version>` will validate, and then actually release, the package

> NOTE: Producer reads the `.git` or `.hg` configuration data from the repository, so it knows whether you are using Github, Gitlab, or Bitbucket as the remote origin.

## Validating

When you validate the library package, Producer will:

- Sync with the remote origin (i.e., pull from the remote origin, then push any local changes, then check the local status to make sure everything is committed and pushed)
- Validate the composer.json file
- Check for informational files (see below) and for a `phpunit.xml.dist` file
- Check that the LICENSE file has the current year in it
- Call `composer update`, run the unit tests, and make sure they cleaned up after
- Check that the PHP docblocks in the `src/` directory are valid (see below)
- Check that the CHANGES file is in the most-recent commit to the repository

If any of those fails, then the package is not considered valid for release.

In addition, the `validate` command will show any open issues from the remote origin, but these are presented only as a reminder, and will not be considered invalidators.

### Informational Files

Producer wants you to have these informational files in the package root:

- `CHANGES`, a list of changes for the release;
- `CONTRIBUTING`, describing how to contribute to the library;
- `LICENSE`, the package licensing text; and,
- `README`, an introduction to the library.

These informational files may have a `.md` extension.

### Docblocks

Producer will not attempt to check docblocks for 0.*, -dev, or -alpha releases. It seems reasonable to expect that the codebase is not ready for documenting before a beta release.

## Releasing

When you `release` the package, Producer will first `validate` it as a pre-flight step.

Then it will use the Github or Gitlab API to create a release. In the case of Bitbucket (which does not have an API for releases) it will tag the repository locally.

Finally, Producer will sync with the remote origin so that the release is represented locally, and/or pushed to the remote.
