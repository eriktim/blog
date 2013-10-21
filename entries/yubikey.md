    apt-get install libpam-yubico

# vim /etc/yubikey

	<user>:<yubikey tokan id>[:<yubikey tokan id>]

    chmod 600 /etc/yubikey

# vim /etc/security/access-local.conf
	# yubikey can be skipped on local network
	+ : ALL : 192.168.178.0/24
	+ : ALL : LOCAL
	- : ALL : ALL

# vim /etc/pam.d/sshd
	# PAM configuration for the Secure Shell service

	auth [success=1 default=ignore] pam_access.so accessfile=/etc/security/access-local.conf
	auth required pam_yubico.so authfile=/etc/yubikey id=<yubikey-api-id> key=<yubikey-api-key>

# vim /etc/ssh/sshd_config
	# Change to yes to enable challenge-response passwords (beware issues with
	# some PAM modules and threads)
	ChallengeResponseAuthentication no

	# Change to no to disable tunnelled clear text passwords
	PasswordAuthentication yes

	# Set this to 'yes' to enable PAM authentication, account processing,
	# and session processing. If this is enabled, PAM authentication will
	# be allowed through the ChallengeResponseAuthentication and
	# PasswordAuthentication.  Depending on your PAM configuration,
	# PAM authentication via ChallengeResponseAuthentication may bypass
	# the setting of "PermitRootLogin without-password".
	# If you just want the PAM account and session checks to run without
	# PAM authentication, then enable this but set PasswordAuthentication
	# and ChallengeResponseAuthentication to 'no'.
	UsePAM yes

# vim /etc/pam.d/common-auth
	# here are the per-package modules (the "Primary" block)
	auth    [success=1 default=ignore]      pam_unix.so nullok_secure try_first_pass
	# here's the fallback if no module succeeds
	auth    requisite                       pam_deny.so
	# prime the stack with a positive return value if there isn't one already;
	# this avoids us returning an error just because nothing sets a success code
	# since the modules above will each just jump around
	auth    required                        pam_permit.so
	# and here are more per-package modules (the "Additional" block)
	auth    optional                        pam_cap.so
	# end of pam-auth-update config

# service ssh restart
