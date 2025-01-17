---
description: >-
  GitHub and GitLab provide a mechanism to verify signed commits using an
  uploaded public SSH or GPG key. GitButler can be configured automatically sign
  all your commits.
---

# ✅ Signing Commits

Git provides a mechanism to sign your commits with a GPG key or SSH key. This enables other developers to make sure that you were actually the person who committed it, rather than someone else just setting their email to yours and committing it as if they were you.

To make this work, a signature is added to the commit header and then that signature is checked against public key stored somewhere, generally for most people the most useful way to verify these signatures is through GitHub or GitLab.

This is what a verified commit looks like on both systems:

<div align="center">

<figure><img src="../../../.gitbook/assets/CleanShot 2023-09-23 at 16.40.14@2x.png" alt=""><figcaption><p><em>A verified commit on GitLab.</em></p></figcaption></figure>

</div>

<div align="center">

<figure><img src="../../../.gitbook/assets/CleanShot 2023-09-23 at 16.42.31@2x.png" alt=""><figcaption><p><em>Verified and non-verified commits on GitHub.</em></p></figcaption></figure>

</div>

This means that the server has a public key that you used to sign the commits that is associated to your account and has verified that this user actually signed this commit.

In order for this to work, you need to:

1. Tell GitButler to sign your commits
2. Upload your key as a "signing key" to GitHub or GitLab (or elsewhere)

## Telling GitButler to Sign

For GitButler to sign commits, you need to setup Git to sign commits, as we do roughly the same thing that Git itself tries to do, and we read and respect most of the same Git config settings.

The main difference is that instead of `commit.gpgSign` as the flag that tells Git to automatically sign commits, we look for `gitbutler.signCommits`. If this is set, GitButler will attempt to sign your commits with the normal Git settings.

We look to see if we have a signing key in `user.signingkey`. If we have a key, we look for 'ssh' in `gpg.format`, otherwise we use GPG. We will respect `gpg.ssh.program` for ssh if there is a different binary path, and `gpg.program` for GPG. We also identify literal SSH keys in the `user.signingkey` field.

The only major thing we don't support yet is `gpg.ssh.defaultKeyCommand` for other ways to get a key other than the `user.signingkey` field. We also don't support the X.509 smime stuff.

Here is an example, if you have a public key here `.ssh/id_ed25519.pub` then you can setup signing with something like this:

```
$ git config --global user.signingkey "/Users/schacon/.ssh/id_ed25519.pub"
$ git config --global gpg.format ssh
$ git config --global gitbutler.signCommits true
```

There are lots of other ways to set up GPG or SSH commit signing:

* 1Password is a very easy way to [SSH sign commits](https://blog.1password.com/git-commit-signing/).
* GitHub has a [good guide](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key) on how to setup GPG or SSH keysigning.
* Here is a nice simple gist for [GPG signing on Windows](https://gist.github.com/BoGnY/f9b1be6393234537c3e247f33e74094a).

## Upload Your Signing Key

For GitHub or GitLab to verify your signatures, you need to say that the SSH or GPG public key we are using is a valid signing key for your user.&#x20;

### Adding to GitHub

You can click on the "Add key to GitHub" link in the settings page right about the signing toggle, or you can go [here](https://github.com/settings/ssh/new) ([https://github.com/settings/ssh/new](https://github.com/settings/ssh/new)) to paste that public key in.

<div align="center">

<figure><img src="../../../.gitbook/assets/CleanShot 2023-09-23 at 21.48.05@2x.png" alt=""><figcaption><p><em>Be sure to change the type to "Signing Key"</em></p></figcaption></figure>

</div>

Now your signed commits should show up as "Verified".

### Adding to GitLab

For GitLab you need to go to "SSH Keys" in your profile: [https://gitlab.com/-/profile/keys](https://gitlab.com/-/profile/keys) and click the "Add new key" button.

<div align="center">

<figure><img src="../../../.gitbook/assets/CleanShot 2023-09-23 at 21.50.22@2x.png" alt="" width="563"><figcaption><p><em>Add new key here.</em></p></figcaption></figure>

</div>

Now paste in the public SSH key you copied from GitButler, name it and make sure the "Usage Type" is either "Signing" or "Authentication and Signing".

<div align="center">

<figure><img src="../../../.gitbook/assets/CleanShot 2023-09-23 at 21.51.22@2x.png" alt="" width="563"><figcaption></figcaption></figure>

</div>

Now all your GitButler generated commits will be verified on that platform!
