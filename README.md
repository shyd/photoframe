# Photo Frame

This is a setup guide for a digital photo frame based on [immich-kiosk](https://github.com/damongolding/immich-kiosk) running on a Raspberry Pi.

## Prerequisites

- Ready and running [Immich](https://immich.app) instance.
- A physical frame with a Raspberry Pi in it.

## Raspberry Pi Setup

### OS

Install a fresh Raspberry Pi OS with Desktop. <https://www.raspberrypi.org/downloads/raspberry-pi-os/>

### Docker

Install Docker from <https://docs.docker.com/engine/install/debian/> in order to run the prebuilt container.

### Chromium

To autostart Chromium after autologin add a autostart config for labwc in `.config/labwc/autostart` (create it if not existing):

```
/usr/bin/chromium \
--kiosk \
--noerrdialogs \
--disable-infobars \
--no-first-run \
--ozone-platform=wayland \
--enable-features=OverlayScrollbar \
--start-fullscreen \
--enable-auto-reload \
--check-for-update-interval=31536000 \
--hide-crash-restore-bubble \
--app=http://127.0.0.1:8080 \
--password-store=basic
```

Reboot.

Now everything should run by default.

### LED Setup

In order to turn off the LEDs of the RPi, add the following lines to `/boot/firmware/config.txt`:

```plain
# Disable Activity LED
dtparam=act_led_trigger=none
dtparam=act_led_activelow=off

# Disable Power LED
dtparam=pwr_led_trigger=none
dtparam=pwr_led=activelow=off
```

In case the PWR LED stays on, adding this to `/etc/rc.local` helped:

```bash
#sudo sh -c 'echo none > /sys/class/leds/led0/trigger'
#sudo sh -c 'echo none > /sys/class/leds/led1/trigger'
#sudo sh -c 'echo 0 > /sys/class/leds/led0/brightness'
sudo sh -c 'echo 0 > /sys/class/leds/led1/brightness'
```

## Nginx Setup as Reverse Proxy on a Raspberry Pi with Docker

Copy `nginx/pf-startup.html` to `/usr/share/nginx/html/pf-startup.html` to a beautiful startup page instead of the default error page.

Copy `nginx/proxy.conf` to `/etc/nginx/conf.d/proxy.conf` to setup the reverse proxy.

Change the port mapping in docker from 8080:8080 to 8081:8080 to separate the ports.

In short:

```bash
sudo wget https://raw.githubusercontent.com/shyd/photoframe/refs/heads/main/nginx/pf-startup.html -O /usr/share/nginx/html/pf-startup.html
sudo wget https://raw.githubusercontent.com/shyd/photoframe/refs/heads/main/nginx/proxy.conf -O /etc/nginx/conf.d/proxy.conf
```
