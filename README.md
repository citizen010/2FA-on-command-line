# Time-based One-Time-Password for 2FA on CLI
<h2>About</h2>
<p>There is no shortage of <a href="https://en.wikipedia.org/wiki/One-time_password">OTP</a> 2FA apps availiable for your phone, such as <a href="https://authy.com">Authy</a> , <a href="https://freeotp.github.io/">FreeOTP</a> or even the not so recommended <a href="https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en_us">Google Authenticator</a>.<br />These apps take an initial secret code and create a <a href="https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm">TOTP</a> anytime you  need a 2FA code for login.</p>
<p>Using <code>oathtool</code> in the command line has the advantages that we'll be able to use TOTP authentication on Linux machines without mobile phone and it forces us to backup all our TOTP secrets and not only some recovery keys.</p>
<h2>Usage</h2>
<p>Make sure you're logged in as a regular user (not as root) and install as follow:<br /><br />
<code>sudo apt install oathtool gpg</code></p>
<p>Then, store the following source code to a shell script <code>totp</code>:<br /><br />
<code>sudo nano /usr/local/bin/totp</code>
<pre><code>
#!/bin/bash
# 
# Time-based One-time Password algorithm (TOTP) helper script
# Save shared secrets on disk protected with GnuPG encryption
# Easily generate OTPs for two-factor authorization (2FA)
#
# Setup:
# Install requirements with `sudo apt install oathtool gpg`
# Setup gpg as per https://keyring.debian.org/creating-key.html
#
# Adapt the 3 variables below:
# - KEYFILE: file that holds the name/key pairs
# - UID: GnuPG user ID to use for encryption
# - KEYID: GnuPG key ID to use for encryption
#
# Good to know:
# - get gpg keys with: `gpg --list-keys --keyid-format short user@example.com'
#
# - the $KEYFILE itself is in clear and has the format:
#     aws=hQIMAxevVAas6A+AAQ//cJL/v3O6CCurdzVkCk5yEGa6sZgWWw6AkH/QenVmTSj...
#     twitter=hQIMAxevVAas6A+AAQ/9H8h0yde7zErfF/8qwohD5Zw7q85FlI+IIFC1Kk5Ifpw...
#     github=hQIMAxevVAas6A+AARAAm8T//mqNyBEz4Y/HGGlNgFUzk8vOaylMdE/TbDzVI...
#
# - the shared secrets are stored encrypted with gpg then base64-ed
# - keys are never deleted, only appended
# - the last available key for the chosen service is used
# - to restore the previous key, manually delete the last key from $KEYFILE
#
# Authors:
# - https://www.sendthemtomir.com/blog/cli-2-factor-authentication and
# - https://karl-voit.at/2019/03/03/oathtool-otp/, Karl Voit, tools@Karl-Voit.at
# - Paolo Greppi, paolo.greppi@libpf.com
# LICENSE: GPLv3

set -e

KEYFILE="$HOME/.totpkeys"
UID="user@example.com"
KEYID="9E2A4CEF"

if [ -z "$1" ]; then
  echo
  echo "Usage:"
  echo "   totp list"
  echo "   totp get google"
  echo "   totp set google QUBAYAYXV5KANLHI"
  exit
fi

if [ "$1" = 'list' ]; then
   KEYS=$(sed 's/^\([^=]*\)=.*$/- \1/g' "$KEYFILE")
   echo "Available keys:"
   echo "$KEYS"
   exit
fi

if [ "$1" = 'get' ]; then
  if [ -z "$2" ]; then
    echo "$0: Missing service name"
    $0
    exit
  fi
  TOTPKEY=$(sed -n "s/${2}=//p" "$KEYFILE" | tail -n 1)
  if [ -z "$TOTPKEY" ]; then
    echo "$0: Bad Service Name '$2'"
    $0
    exit
  fi
  TOTPKEY=$(echo "$TOTPKEY" | base64 -d | gpg --decrypt -r "$UID" -u "$KEYID" 2> /dev/null)
  oathtool --totp -b "$TOTPKEY"
  exit
fi

if [ "$1" = 'set' ]; then
  if [ -z "$2" ]; then
    echo "$0: Missing service name"
    $0
    exit
  fi
  if [ -z "$3" ]; then
    echo "$0: Missing key"
    $0
    exit
  fi
  oathtool --totp -b "$3" > /dev/null # verify secret
  TOTPKEY=$(echo "$3" | gpg --encrypt -r "$UID" -u "$KEYID" | base64 -w0)
  echo "$2=$TOTPKEY" >> "$KEYFILE"
  exit
fi

echo "Command $1 unknown"
$0

</code></pre>
</p>
<p>Make it executable with:<br /><br />
<code>sudo chmod +x /usr/local/bin/topt</code></p>
<h2>License</h2>
<p><a href="https://raw.githubusercontent.com/citizen010/empty-site-template/master/LICENSE" rel="nofollow"><img src="https://camo.githubusercontent.com/890acbdcb87868b382af9a4b1fac507b9659d9bf/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6c6963656e73652d4d49542d626c75652e737667" alt="GitHub license" data-canonical-src="https://img.shields.io/badge/license-MIT-blue.svg" style="max-width:100%;"></a>
