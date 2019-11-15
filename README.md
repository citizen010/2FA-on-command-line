# Time-based One-Time-Password for 2FA on CLI
<h2>About</h2>
<p>There is no shortage of <a href="https://en.wikipedia.org/wiki/One-time_password">OTP</a> 2FA apps availiable for your phone, such as <a href="https://authy.com">Authy</a> , <a href="https://freeotp.github.io/">FreeOTP</a> or even the not so recommended <a href="https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en_us">Google Authenticator</a>.<br />These apps take an initial secret code and create a <a href="https://en.wikipedia.org/wiki/Time-based_One-time_Password_algorithm">TOTP</a> anytime you  need a 2FA code for login.</p>
<p>Using <code>oathtool</code> in the command line has the advantages that we'll be able to use TOTP authentication on Linux machines without mobile phone and it forces us to backup all our TOTP secrets and not only some recovery keys.</p>
<h2>Usage</h2>
Make sure you're logged in as a regular user (not as root) and install as follow:<br /><br />
<code>sudo apt install oathtool gpg</code>
Then, store the following source code to a shell script <code>totp</code>:<br /><br />
<code>sudo nano /usr/local/bin/totp</code>

Make it executable with:<br /><br />
<code>sudo chmod +x /usr/local/bin/topt</code>
<h2>License</h2>
<p><a href="https://raw.githubusercontent.com/citizen010/empty-site-template/master/LICENSE" rel="nofollow"><img src="https://camo.githubusercontent.com/890acbdcb87868b382af9a4b1fac507b9659d9bf/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f6c6963656e73652d4d49542d626c75652e737667" alt="GitHub license" data-canonical-src="https://img.shields.io/badge/license-MIT-blue.svg" style="max-width:100%;"></a>
