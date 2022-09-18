---
title: Configuring Eduroam on Linux
date: '2022-07-15'
---

This is a short guide on how to configure eduroam on Linux.

[UMU created a guide in
Swedish](https://manual.its.umu.se/stalla-in-eduroam-pa-ubuntu-manuellt/). It is
eerily similar to mine...  Well! The WPA-supplicant guide still holds, however.

This is a brief guide for how to install Eduroam on a Linux computer at Umeå
University. I made this guide since at the time of writing there is no definite
guide on ITS as how this is done, so until then, this site exists.

There are two methods shown here - one is for when you have access to a
wifi-settings GUI (which is the case for most people), and the other is for
configuring manually using wpa\_supplicant.

Note that there might be slight differences between your flavour of Linux and
what I show here (Debian with GNOME and Raspbian), but the instructions should
be very similar independent on what distribution you are using.

## Downloading certificates

For both methods you need to acquire certificates for the specific machine you
are going to configure eduroam on.

* Go to [eduroam.umu.se](https://www.eduroam.umu.se/) and log in using your CAS.
  Then, press ''Create certificate''. Add a reasonable comment so you remember
  what machine the certificate goes to, and press ''Create Certificate''.
* **Important:**  make sure that you write down the given password to your
  certificate. It is not possible to retrieve this after you have closed this
  window.
* Press ''Download personal certificate'' and ''Download root certificate'' to
  download your certificates. Your personal certificate should have the name
  `<your-cas>.pfx` and the root certificate `UMU-CAROOT-CA2.cer`.
* For both instructions your personal certificate has to have a different file
  ending. To fix this, simply rename your file from `<your-cas>.pfx` to
  `<your-cas>.p12`. In the terminal, this is done by two simple commands:
  ```
  cd <path-to-your-certificate>
  mv <your-cas>.pfx <your-cas>.p12
  ```
* Put the certificates in a proper folder on your system. I recommend putting
  them in a new folder like `/etc/certs/eduroam`, or wherever you feel like
  putting them.

## Configuration using a GUI

* Open your wifi settings menu. On GNOME this is done by pressing the
  upper-right corner on your computer and opening up the menu with the
  Wifi-symbol and pressing ''Wi-Fi Settings''.
* Choose 'Connect to Hidden Network...'.
* Under 'Network name' write 'eduroam'.
* Then the security settings are as follows:
    * 'Wi-Fi security' should be 'WPA & WPA2 Enterprise'
    * 'Authentication' should be 'TLS'
    * 'Identity' should be your CAS name followed by '@ad.umu.se'. So for a CAS
      'abcd1234' it will be 'abcd1234@ad.umu.se'
    * 'User certificate' does not have to be filled in
    * 'CA certificate' should be the path to the UMU-certificate on your system
      ('UMU-CAROOT-CA2.cer')
    * 'Private key' should be the path to the personal certificate on your
      system (<your-cas>.p12)
    * 'Private key password' should be the password you wrote down earlier when
      downloading the certificates
* After this is filled out you press 'Apply' and Voilá! You are connected to
  eduroam. Congratulations!

## Configuration using `wpa_supplicant`

I will assume in this guide that you already have `wpa_supplicant` installed.
This is installed and configured by default on Raspbian. If this is not the
case, search for an installation guide online.
* First you need to find your wifi interface. On a Raspberry Pi 3
  Model B running raspbian (and for most systems) it usually called
  something like `wlanX`, where `X` is some number. To find the
  name, type the following command in the terminal
  ```
  ip a
  ```
  Which should show all your network interfaces.
* Now when you know the name of your wifi interface, you need to configure some
  files within the system. First, your interfaces has to be configured. This is
  done by editing file `/etc/network/interfaces`. To do this, you can use `nano`
  as a superuser
  ```
  sudo nano /etc/network/interfaces
  ```
  To connect your interface to `wpa_supplicant` add the following lines to the
  end of the file (change your interface if not applicable)
  ```
  auto wlan0
  allow-hotplug wlan0
  iface wlan0 inet manual
  wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
  ```
  and save the file by pressing `Ctrl+x` and then `Y`.
* To add the network eduroam to your `wpa_supplicant` you need to edit
  `wpa_supplicant.conf`.
  ```
  sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
  ```
  and add the following lines to the end of the file<br>
  ```
  network={
      ssid="eduroam"
      key_mgmt=WPA-EAP
      eap=TLS
      identity="<your-cas>@ad.umu.se"
      ca_cert="/etc/certs/UMU-CAROOT-CA2.cer"
      private_key="/etc/certs/<your-cas>.p12"
      private_key_passwd="<your-cert-pass>"
  }
  ```
  where the password is the same as saved before.  Change the paths if you
  placed your certs somewhere else than `/etc/certs`.
* Now your wifi should automatically connect to eduroam!
