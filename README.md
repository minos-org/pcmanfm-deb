# pcmanfm-deb

minos' patch on Ubuntu's own pcmanfm package.

A right click on the background of a folder opens its menu (create new, paste, sort, properties) without a
way to open a terminal there, although F4 and the menu of a folder icon have one.
`folder-menu-terminal.patch` adds "Open Current Folder in Terminal", the same action as F4, to that menu,
right after "Create New…".

## Branches

One branch per Ubuntu release, since each ships its own pcmanfm: `jammy` (22.04, 1.3.2 with GTK2),
`noble` (24.04, 1.3.2 with GTK3) and `resolute` (26.04, 1.4.0).

Each one is that release's `ubuntu/<series>-devel` branch from Ubuntu's git of the package
(https://git.launchpad.net/ubuntu/+source/pcmanfm), with its full history, plus one commit that adds
`debian/patches/folder-menu-terminal.patch` and its line at the top of `debian/patches/series`. Ubuntu
appends its own patches at the end of that file, so a new Ubuntu patch never touches this line. Nothing
else changes, not even `debian/changelog`.

## How it is built

One Launchpad recipe per release, building into the minos PPA (`ppa:minos-archive/main`), starts from
Ubuntu's branch and merges this one:

    # git-build-recipe format 0.4 deb-version {debversion}+minos{time}
    lp:ubuntu/+source/pcmanfm ubuntu/noble-devel
    merge minos lp:~minos-archive/minos/+git/pcmanfm-deb noble

The version is Ubuntu's followed by `+minos` and the time of the build: it stays just above Ubuntu's, every
build gets a new one, and any newer Ubuntu upload sorts above it. A version taken from the branches alone
repeats when the same commits are built twice, and the PPA refuses the second upload, whose changelog entry
carries another date. The recipes build
daily when a branch they read has changed, so a new Ubuntu upload is rebuilt with the patch; if that build
fails, Ubuntu's newer package wins over the old minos one.

## When Ubuntu uploads a new version

Rebase the one commit onto Ubuntu's branch and push it:

    git remote add ubuntu https://git.launchpad.net/ubuntu/+source/pcmanfm
    git fetch ubuntu
    git checkout noble
    git rebase ubuntu/ubuntu/noble-devel
    git push --force-with-lease origin noble

If Ubuntu starts a `debian/patches/series` of its own, keep this patch's line first. A new Ubuntu release
gets its own branch, made the same way, and its own recipe.
