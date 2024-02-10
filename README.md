This is repo contains scripts for Artix to start pipewire, pipewire-pulse and wireplumber from openrc.
This is just for my own user and I am not going to support it. But you are free to fork it to make it better.

**Prerequisite**

Pipewire

Wireplumber


**Mechanism**

After login, PAM would change the runlevel to user runlevel by UID.

It will await the session (for me, its gnome session) to be started and export the DBUS_SESSION_BUS_ADDRESS to a file. (timeout is 30s)

runlevel switching would be completed after the dbus session file is found so that the pipewire servic can retrieve the DBUS_SESSION_BUS_ADDRESS to work.


**File Structure**

/etc/init.d/pipewire

/etc/init.d/pipewire-pulse

/etc/init.d/wireplumber

/usr/local/bin/await-user-dbus

/usr/local/bin/user-dbus

/usr/local/bin/user-service-start

~/.config/autostart/user-dbus.desktop


**Setup**

*create runlevel by user ID (e.g. default UID is 1000, can check it bay "echo $UID")*

mkdir /etc/runlevels/1000

*Stack the run level*

rc-update -s add default 1000

*Append PAM to change runlevel after login*

echo "session   optional  pam_exec.so /usr/local/bin/user-service-start" >> /etc/pam.d/system-local-login


sample:


#%PAM-1.0

auth      include   system-login

account   include   system-login

password  include   system-login

session   include   system-login

session   optional  pam_exec.so /usr/local/bin/user-service-start

*Add service to user runlevel*
rc-update add pipewire 1000
rc-update add pipewire-pulse 1000
rc-update add wireplumber 1000
