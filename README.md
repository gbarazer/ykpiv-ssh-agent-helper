# ykpiv-ssh-agent-helper

This is a dumb little utility to streamline the use of ssh-agent with
PIV-enabled YubiKeys on OS X. While is specifically intended for use
with the YubiKey 4's PIV applet, it probably could be tweaked for use
with other PKCS#11 modules.

In particularly, this tool is designed to solve two problems:

* It allows users to store their PIV PIN in the OS X keychain (like
  what OS X does out-of-the-box with password-protected SSH keys)

* After a Mac goes to sleep, we need to remove and re-add the PKCS#11
  module from the agent. (The sleep/wake cycle appears to reset the
  PIN-unlock state of the PIV applet).

# Build / Install

Grab the latest yubico-piv-tool-x.x.x-mac.zip distribution from
https://developers.yubico.com/yubico-piv-tool/Releases/, and place it
in the same directory containing this README.md. Then, run:

    ./pkg_build.sh

To install, open the package file generated by the above command.

# Setup

If you haven't provisioned a keypair / certificate in your YubiKey's
PIV applet yet, consider running the included "provision_token.py"
script.

Once this is done, run:

    ykpiv-ssh-agent-helper --reset-pin

This will generate a new PIN for your PIV applet and store it in the
keychain. Once you've done this, ykpiv-ssh-agent-helper will start
automatically (as an OS X 'LaunchAgent') every time you log in to
OS X.

To see if ykpiv-ssh-agent-helper is functioning correctly, run:

    ssh-add -l

and look for a public key with a path like
'/opt/yubico-piv-tool/lib/libykcs11.dylib'. Then, put this key in your
authorized_keys file on some remote server and away you go!

# Notes

On OS X 10.11 and earlier, you will need to update your CCID
driver. Yubico provides an installation package for this here:
https://github.com/Yubico/ifd-yubico/releases/download/ifd-yubico-1.0.0/ifd-yubico.pkg

If you ever remove and reinsert your YubiKey, or if the tool fails for
some other reason, you may need to restart ykpiv-ssh-agent-helper. To
do this, run

    ykpiv-ssh-agent-helper --reload-service

Some basic debugging output for ykpiv-ssh-agent-helper should be
visible in Console.app
