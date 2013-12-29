# Monitor /etc/ using git

1. Initialize a new git repository at `/etc/`

        cd /etc/
		sudo git init
   
2. Setup `.gitignore`

        cat << EOF | sudo tee .gitignore
        ld.so.cache
        ld.so.conf
        mtab
        group-
        gshadow-
        passwd-
        shadow-
		EOF

3. Make initial commit

        sudo git add .
		sudo git commit -m "initial commit"

4. Setup hourly commits using crontab:

        sudo crontab -e

        # Add the following line to crontab
        0 * * * * cd /etc/ && git add . && git commit -a -m "hourly commit" 2>&1 >> /var/log/cron.log

     Use `tail -f /var/log/cron.log` and `tail -f /var/log/syslog | grep CRON` to monitor cron activities.

5. Use `sudo gitg` to review/undo changes
