---
title: 'Deploying a Private Git Server with Soft-Serve'
excerpt: A simple and private git server running Charm's soft-serve on fly.io
date: '2024-03-29'
slug: soft-serve-git-server
coverImage: /images/soft-serve.png
coverWidth: 16
coverHeight: 9
categories:
  - homelab
---

# Private Git Server

![soft-serve my private git repo](/images/private-git-example.png)

## Intro

I use [Logseq](https://logseq.com/) as an open-source notes app across all my devices, but historically, I haven't worried about syncing data between the devices.

I keep my tech stuff on my personal computer, my work stuff on my work computer, and my shopping lists on my phone.

However, I generally have a rule against storing anything important to me on just a single device. If I lose my laptop, it shouldn't be a big deal to get back to full productivity on a new one.

Notes were a miss though. While I've reinstalled my system a few times, I always just upload an export to Proton Drive and then re-import.

This guide follows the path I took trying to ensure my notes were constantly backed up and synced securely.

This repo uses [soft-serve](https://github.com/charmbracelet/soft-serve) as a primarily ssh-driven self-hostable git server.

## Getting Started

Prerequisites:

* A fly.io account
* [`flyctl`](https://fly.io/docs/hands-on/install-flyctl/) installed


Steps:

* Clone or fork the repo [bianchidotdev/git](https://github.com/bianchidotdev/git)
* Update the following in `fly.toml`
  * app name
  * primary_region
  * SOFT_SERVE_NAME
  * SOFT_SERVE_INITIAL_ADMIN_KEYS
  * SOFT_SERVE_SSH_PUBLIC_URL
  * SOFT_SERVE_HTTP_PUBLIC_URL
* Run the following commands:

```sh
fly auth login
fly launch
# copy the configuration and optionally tune the fly settings
# allocate a dedicate ipv4 address (this is necessary for raw TCP connections to function correctly on fly.io!) - if you miss this, you can always assign an ipv4 address with `fly ip allocate-v4`

# the output should end with something like the following:
# => Visit your newly deployed app at https://bianchidotdev-git2.fly.dev/

# you should now be able to ssh into the newly deployed instance
ssh bianchidotdev-git2.fly.dev
```

![empty soft-serve over ssh](/images/private-git-ssh.png)

And with that, we should have a soft-serve git server.

Test it out by creating importing a public repo:
```sh
ssh bianchidotdev-git2.fly.dev repo import git https://github.com/bianchidotdev/git.git

# check out the git server now that it contains data!
ssh bianchidotdev-git2.fly.dev
```

![soft-serve showing imported public repo](/images/private-git-with-repo.png)

And then clone it:
```sh
git clone bianchidotdev-git2.fly.dev:git.git
# =>  λ  git clone bianchidotdev-git2.fly.dev:git.git
# => Cloning into 'git'...
# => remote: Enumerating objects: 4, done.
# => remote: Counting objects: 100% (4/4), done.
# => remote: Compressing objects: 100% (4/4), done.
# => remote: Total 4 (delta 0), reused 4 (delta 0), pack-reused 0
# => Receiving objects: 100% (4/4), done.
```

Optionally go ahead and set up a custom domain. You can set it up by logging into fly.io, navigating to your app, and following the instructions under certificates.