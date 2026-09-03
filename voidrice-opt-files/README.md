## optional files that can be used as user choice

# simple lock screen systemd files
* slock-lang systemd file can be used to change lang to english if using multiple langueges and slock
* slock systemd file to handle lock screen when closing laptop lid.

# installation
1. cp slock* /etc/systemd/system/
2. chmod 644 /etc/systemd/system/slock*
3. systemctl enable slock@your_username.service
4. systemctl enable slock-lang@your_username.service
5. systemctl systemctl daemon-reload
