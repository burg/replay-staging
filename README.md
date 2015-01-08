This is a staging repository for work on the WEB_REPLAY feature in
WebKit. It's used to create demos and prototypes without being blocked
on reviews, while also helping to consolidate stable implementation
pieces into patches to be submitted upstream.

The repository contains a patch series under version control, as well
as a WebKit repository submodule at a particular version to which the
patches should be applied. This allows patches to be based on a
specific commit of WebKit, and to be updated as that base commit
catches up to the master branch of the WebKit git repository.

The patch series is split into different directories, so that forks
which add patches to the repository do not have to worry about patch
numberings changing in the original replay-staging repository.

---

# Getting

    git clone git://github.com/burg/replay-staging.git

Then follow the steps below for updating.

# Updating

Pull new commits to the staging repository, then pull commits to the
submodule. The initial update will take a long time, because it needs
to download the entire WebKit git repository (over 5GB in size).

    git pull
    git submodule update --init --recursive
    git submodule update --recursive
    scripts/import-patches


Now you should be able to go into the OpenSource directory and build
WebKit as normal.
[More details on building WebKit itself](http://www.webkit.org/building/build.html).

# Importing/Applying Patches

From within the WebKit submodule working copy or within the staging
working copy, you can run the following command to export any commits
between the merge-base and head of the submodule.

    scripts/import-patches

Note: this script requires the working copy to be clean, and will
abort if there are any patches already applied to the WebKit submodule
(i.e., HEAD is not merge-base).

# Exporting Patches

From within the WebKit submodule working copy or within the staging
working copy, you can run the following command to export any commits
between the merge-base and head of the submodule.

    scripts/export-patches

Note: this script requires the working copy to be clean, and does
not reset the submodule's head to point to the merge-base commit. This
is intentional, so that exporting and committing patches does not
necessarily clobber the submodule working copy's build state.


# Updating the WebKit base commit

To update the merge-base commit of the submodule---say, you want to
incorporate new WebKit commit changes---you must rebase patches in the submodule and update the submodule commit hash to point to the new merge-base commit. This is done automatically by the `export-patches` script.

To update to a newer WebKit, do the following from the submodule:

1. (Commit any outstanding changes)
2. `git pull --rebase`
3. `../scripts/export-patches`
4. (Go to staging repository; make commit)

It is recommended that you update the base commit by first applying
all patches and using `git rebase` rather than trying to apply patches
to a new base commit using `git am` or `import-patches`. The former
lets you use the familiar tools for rebasing, while the latter
requires digging into the import script's implementation if patches
fail to apply.

# Repository Structure

```
    OpenSource/    # A submodule that points to the official WebKit git mirror.
                   # (git://git.webkit.org/WebKit.git)

    patches/       # Base directory for all patches.
        replay/    # Contains replay infrastructure patches.
    other_dir/ # See note [1]
```

[1] Forks of this repository should use a separate patch directory if
they merely build on top of the replay infrastructure patches.
