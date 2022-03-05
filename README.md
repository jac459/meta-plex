# meta-plex
driver for Plex

Install is super easy.

### 0 - Search for plex in the "add device" menu of neeo. Choose the meta one.
When you install, a security will be asked to you.
### 1 - just sign in to your Plex account  in Plex Web App (using any browser on the same machine than your neeo interface)
### 2 - Browse to a library item and view the XML for it (detailled info on how to do is here https://support.plex.tv/articles/201998867-investigate-media-information-and-formats/ it is very easy, just matter of clicking on the getinfo button)
### 3 - Copy paste the whole url as your security code
### 4 - All your plex clients will be detected, just choose the plex client you want to use.


## Neeo Brain with new Let's Encrypt certificates

As Let's Encrypt has changed his root certificate in September 2021 this new root CA is not part of the Neeo Brain and is not able to load any HTTPS content from sites using LE certs.
(https://letsencrypt.org/docs/dst-root-ca-x3-expiration-september-2021/)

To get Neeo working with the new LE root certificate you need SSH access on your brain. How to get ssh access is described here: https://github.com/jac459/NeeoDriversInBrain

To get the new LE root ca work do the following on your brain:

1. remount root partition with write mode (if not already done):
```
sudo mount -o remount,rw /
```

2. load new LE root CA file to the brain:
```
cd /etc/ca-certificates/extracted/cadir/
curl -s "https://letsencrypt.org/certs/isrgrootx1.pem" -o /etc/ca-certificates/extracted/cadir/ISRG_Root_X1.pem
```

3. Add ENV to neeoenv file to make Node use the new CA file too:
```
sudo vi /etc/profile.d/neeoenv.sh
```

and add the following line to the end of the file:
```
export NODE_EXTRA_CA_CERTS=/etc/ca-certificates/extracted/cadir/ISRG_Root_X1.pem
```

4. Add environment variable to root account and make use of them by logout/login with root account:
```
sudo su
export PM2_HOME=/var/opt/pm2
exit
sudo su
```

5. to get pm2/node use the new environment variables at reboot do the following:
```
cd /var/opt/pm2
pm2 list
```
Command shuld now show all running node services. If not, following steps won't work!


```
pm2 restart all --update-env
chattr -i dump.pm2
pm2 save
chattr +i dump.pm2
exit
```

6. reboot the brain
```
sudo reboot now
```
