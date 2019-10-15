Linux Performance Tools	
  

Some info about linux performance tools and Methodologies.


Methodologies &	Tools

The USE method: 

For every resource check:

• Utilization
• Saturation
• Errors

Other methods:

• Workloads characterization, drill-down analysis , event tracing,
    baseline stats, static performance tuning


Start with the questions, then find the tools.


Command line tools


Usefulk to study even if you never use them:
GUIs and commercial products often use the 
same interfaces.

Type   		Characteristic

Observability   Watch. Safe, usually, depending on resource overhead.

Benchmarking   Load test. Caution: production test can cause issues due to contention.

Tuning 	 	Change. Danger: changes could hurt performance, nor or later with load.




Observability Tools	
  

Observability Tools:Basic	

• uptime
• top (or htop)
• ps
• vmstat
• iostat
• mpstat
• free

Uptime

One way to print load averages:

$ uptime 

 output:

 09:28:02 up  1:44,  2 users,  load average: 0,47, 0,34, 0,34


A measure if resource demand: CPUs + disk
 Other OSes only show CPUs: easier to interpret

Exponentially-damped moving averages with time
constant of 1, 5, and 15 min.
 Historic trend without the line graph

Load > # of CPUs, may mean CPU saturation
 Don`t spend more than 5 seconds studying these



top (or htop)

Sytem and per-process interval summary: 


$ top 

output: 


top - 09:34:56 up  1:51,  2 users,  load average: 0,15, 0,32, 0,33
Tasks: 361 total,   1 running, 266 sleeping,   0 stopped,   1 zombie
%Cpu(s):  3,4 us,  1,6 sy,  0,0 ni, 94,7 id,  0,0 wa,  0,0 hi,  0,2 si,  0,0 st
KiB Mem : 20304192 total, 14349044 free,  2974936 used,  2980212 buff/cache
KiB Swap:   999420 total,   999420 free,        0 used. 16118756 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                                                        
 3411 ebbestad  20   0  934200 223024 197488 S   9,9  1,1   3:22.43 Xorg                                                                                                                                           
 3571 ebbestad  20   0 4046500 328688 135088 S   8,6  1,6   3:53.04 gnome-shell                                                                                                                                    
 7063 ebbestad  20   0 3253540 405344 136428 S   8,6  2,0   3:01.77 Web Content  



%CPU is summed across all CPUs 

Can miss shot-lived processes (atop won`t)

Can consume noticeable CPU to read /proc

ps

Process status listing:

$ ps -ef f 

output: 

ebbestad  3386     1  0 08:12 ?        00:00:00 /lib/systemd/systemd --user
ebbestad  3388  3386  0 08:12 ?        00:00:00 (sd-pam)
ebbestad  3404     1  0 08:12 ?        00:00:00 /usr/bin/gnome-keyring-daemon --daemonize --login
ebbestad  3409  3374  0 08:12 tty2     00:00:00 /usr/lib/gdm3/gdm-x-session --run-script env GNOME_SHELL_SESSION_MODE=ubuntu gnome-session --session=ubuntu
ebbestad  3411  3409  4 08:12 tty2     00:03:33 /usr/lib/xorg/Xorg vt2 -displayfd 3 -auth /run/user/1000/gdm/Xauthority -background none -noreset -keeptty -verbose 3
ebbestad  3420  3386  0 08:12 ?        00:00:02 /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
ebbestad  3423  3409  0 08:12 tty2     00:00:00 /usr/lib/gnome-session/gnome-session-binary --session=ubuntu
ebbestad  3529  3423  0 08:12 ?        00:00:00 /usr/bin/ssh-agent /usr/bin/im-launch env GNOME_SHELL_SESSION_MODE=ubuntu gnome-session --session=ubuntu
ebbestad  3532  3386  0 08:12 ?        00:00:00 /usr/lib/gvfs/gvfsd
ebbestad  3537  3386  0 08:12 ?        00:00:00 /usr/lib/gvfs/gvfsd-fuse /run/user/1000/gvfs -f -o big_writes
ebbestad  3548  3386  0 08:12 ?        00:00:00 /usr/lib/at-spi2-core/at-spi-bus-launcher
ebbestad  3553  3548  0 08:12 ?        00:00:00 /usr/bin/dbus-daemon --config-file=/usr/share/defaults/at-spi2/accessibility.conf --nofork --print-address 3
ebbestad  3555  3386  0 08:12 ?        00:00:02 /usr/lib/at-spi2-core/at-spi2-registryd --use-gnome-session
ebbestad  3571  3423  4 08:12 tty2     00:04:04 /usr/bin/gnome-shell
ebbestad  3581     1  0 08:12 ?        00:00:00 /usr/bin/pulseaudio --start --log-target=syslog
root      3589     2  0 08:12 ?        00:00:00 [krfcommd]
ebbestad  3603  3571  0 08:12 tty2     00:00:31 ibus-daemon --xim --panel disable


Custom fields:

$ ps -eo user,sz,rss,minflt,majflt,pcpu,args

ebbestad 19321  8404   5266      0  0.0 /lib/systemd/systemd --user
ebbestad 28649  3068     49      0  0.0 (sd-pam)
ebbestad 109347 8140    525      1  0.0 /usr/bin/gnome-keyring-daemon --daemonize --login
ebbestad 53417  6416    907      0  0.0 /usr/lib/gdm3/gdm-x-session --run-script env GNOME_SHELL_SESSION_MODE=ubuntu gnome-session --session=ubuntu
ebbestad 235216 229640 339703   11  4.1 /usr/lib/xorg/Xorg vt2 -displayfd 3 -auth /run/user/1000/gdm/Xauthority -background none -noreset -keeptty -verbose 3
ebbestad 12937  6088   7145      0  0.0 /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
ebbestad 144886 16324  9369      0  0.0 /usr/lib/gnome-session/gnome-session-binary --session=ubuntu
ebbestad  2825   320     33      0  0.0 /usr/bin/ssh-agent /usr/bin/im-launch env GNOME_SHELL_SESSION_MODE=ubuntu gnome-session --session=ubuntu
ebbestad 91832  7396   1620      0  0.0 /usr/lib/gvfs/gvfsd
ebbestad 108005 7956    604      0  0.0 /usr/lib/gvfs/gvfsd-fuse /run/user/1000/gvfs -f -o big_writes
ebbestad 91939  8840    705      0  0.0 /usr/lib/at-spi2-core/at-spi-bus-launcher
ebbestad 12513  4284    343      0  0.0 /usr/bin/dbus-daemon --config-file=/usr/share/defaults/at-spi2/accessibility.conf --nofork --print-address 3
ebbestad 55196  6904    369      0  0.0 /usr/lib/at-spi2-core/at-spi2-registryd --use-gnome-session


vmstat



 


