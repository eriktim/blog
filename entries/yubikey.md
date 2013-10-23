# Yubikey

This post (inspired by [[2]][berrange] and [[3]][yubico-pam]) describes how to enable Yubikey [[1]][yubico] access to any server.
The setup aims at forcing a Yubikey when logging in over the Internet
but allows for direct access on your Local Area Network.


> **system** Debian  

## Installation

Firstly, install the Yubico PAM library

    $ su
    # apt-get install libpam-yubico

## Configuration

Store all Yubikey tokan ids that have access to the server

    # vim /etc/yubikey

>     <user>:<yubikey tokan id>[:<yubikey tokan id>]

Make sure only root can read the file

    # chmod 600 /etc/yubikey

Disable Yubikey for local network access

    # vim /etc/security/access-local.conf

>     # yubikey can be skipped on local network
>     + : ALL : 192.168.1.0/24
>     + : ALL : LOCAL
>     - : ALL : ALL

Configure PAM to use the Yubikey for SSH.
The API key and id can be generated on <https://upgrade.yubico.com/getapikey/>.

    # vim /etc/pam.d/sshd

>     # PAM configuration for the Secure Shell service
>     auth [success=1 default=ignore] pam_access.so accessfile=/etc/security/access-local.conf
>     auth required pam_yubico.so authfile=/etc/yubikey id=<yubikey-api-id> key=<yubikey-api-key>

Configure the SSH daemon to use PAM

    # vim /etc/ssh/sshd_config

>     # Change to yes to enable challenge-response passwords (beware issues with
>     # some PAM modules and threads)
>     ChallengeResponseAuthentication no
> 
>     # Change to no to disable tunnelled clear text passwords
>     PasswordAuthentication yes
> 
>     # Set this to 'yes' to enable PAM authentication, account processing,
>     # and session processing. If this is enabled, PAM authentication will
>     # be allowed through the ChallengeResponseAuthentication and
>     # PasswordAuthentication.  Depending on your PAM configuration,
>     # PAM authentication via ChallengeResponseAuthentication may bypass
>     # the setting of "PermitRootLogin without-password".
>     # If you just want the PAM account and session checks to run without
>     # PAM authentication, then enable this but set PasswordAuthentication
>     # and ChallengeResponseAuthentication to 'no'.
>     UsePAM yes

Configure PAM authorisation

    # vim /etc/pam.d/common-auth

>     # here are the per-package modules (the "Primary" block)
>     auth    [success=1 default=ignore]      pam_unix.so nullok_secure try_first_pass
>     # here's the fallback if no module succeeds
>     auth    requisite                       pam_deny.so
>     # prime the stack with a positive return value if there isn't one already;
>     # this avoids us returning an error just because nothing sets a success code
>     # since the modules above will each just jump around
>     auth    required                        pam_permit.so
>     # and here are more per-package modules (the "Additional" block)
>     auth    optional                        pam_cap.so
>     # end of pam-auth-update config

Restart SSH to enable Yubikey access

    # service ssh restart

## References

1. <http://www.yubico.com/products/yubikey-hardware/yubikey/>
2. <https://www.berrange.com/posts/2011/12/18/multi-factor-ssh-authentication-using-yubikey-and-ssh-public-keys-together/>
3. <https://code.google.com/p/yubico-pam/wiki/YubikeyAndSSHViaPAM>

[yubico]: http://www.yubico.com/products/yubikey-hardware/yubikey/
[berrange]: https://www.berrange.com/posts/2011/12/18/multi-factor-ssh-authentication-using-yubikey-and-ssh-public-keys-together/ 
[yubico-pam]: https://code.google.com/p/yubico-pam/wiki/YubikeyAndSSHViaPAM

