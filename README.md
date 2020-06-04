<h1 align="center">Bash Oneliners</h1>

<p align="center">
<a href="https://github.com/labbots/bash-oneliners/blob/master/LICENSE"><img src="https://img.shields.io/github/license/labbots/bash-oneliners.svg?style=for-the-badge" alt="License"></a>
<a href="https://www.codacy.com/manual/labbots/bash-oneliners?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=labbots/bash-oneliners&amp;utm_campaign=Badge_Grade"><img src="https://img.shields.io/codacy/grade/a72b150d9e8b4166996abf8f871c42b0?style=for-the-badge"/></a>
</p>
Curated list of useful bash onliner commands and functions.

## Table of Contents
- [Files & Directory](#files--directory)
- [Git](#git)
- [Network](#network)
- [System](#system)
- [Terminal](#terminal)

## Files & Directory
List only empty directories and delete safely.

```shell
find . -type d -empty -exec rm -i -R {} \;
```

Find duplicate files in directory using MD5.

```shell
find . -not -empty -type f -printf "%s\n" | sort -rn | uniq -d | xargs -I{} -n1 find . -type f -size {}c -print0 | xargs -0 md5sum | sort | uniq -w32 --all-repeated=separate
```

Sort disk usage in human-readable format on current directory.

```shell
for i in $(echo -e 'G\nM\nK'); do du -hsx *  2>/dev/null | grep '[0-9]'$i | sort -rn; done
```

## Git

Print the list of your Git commits this month.

```shell
git log --since='last month' --author="$(git config user.name)" --oneline
```

Delete all local git branches that have been merged and deleted from remote.

```shell
git branch -d $( git branch -vv | grep '\[[^:]\+: gone\]' | awk '{print $1}' | xargs )
```

Revert files with changed mode, not content.

```shell
git diff --numstat | awk '{if ($1 == "0" && $2 == "0") print $3}' | xargs git checkout HEAD
```

List all authors of a git project.

```shell
git log --format='%aN <%aE>' | awk '{arr[$0]++} END{for (i in arr){print arr[i], i;}}' | sort -rn | cut -d ' '  -f2-
```

Shows a short descriptive information inline including branch details in a coloured log tree.

```shell
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Cblue - %cn %Creset' --abbrev-commit --date=relative
```

## Network

Get your public IP address.
```shell
curl -s http://checkip.dyndns.org | sed 's/[a-zA-Z<>/ :]//g'
```

## System

Show all the startup applications that are hidden in "startup applications".

```shell
sudo sed -i "s/NoDisplay=true/NoDisplay=false/g" /etc/xdg/autostart/*.desktop
```

Quick and dirty way to update system time in Linux if there is any issues with ntp.

```shell
sudo date -s "$(wget -qSO- --max-redirect=0 google.com 2>&1 | grep Date: | cut -d' ' -f5-8)Z"
```
Count the total number of hours of your music in the directory.

```shell
find . -print0 | xargs -0 -P 40 -n 1 sh -c 'ffmpeg -i "$1" 2>&1 | grep "Duration:" | cut -d " " -f 4 | sed "s/.$//" | tr "." ":"' - | awk -F ':' '{ sum1+=$1; sum2+=$2; sum3+=$3; sum4+=$4; if (sum4 > 100) { sum3+=1; sum4=0 }; if (sum3 > 60) { sum2+=1; sum3=0 }; if (sum2 > 60) { sum1+=1; sum2=0 } if (NR % 100 == 0) { printf "%.0f:%.0f:%.0f.%.0f\n", sum1, sum2, sum3, sum4 } } END { printf "%.0f:%.0f:%.0f.%.0f\n", sum1, sum2, sum3, sum4 }'
```

## Terminal

Get top 10 used commands from history.

```shell
history | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl |  head -n10
```
Print all the supported terminal colours and their reference code to use with tput.

```shell
( x=`tput op` y=`printf %$((${COLUMNS}-6))s`;for i in {0..256};do o=00$i;echo -e ${o:${#o}-3:3} `tput setaf $i;tput setab $i`${y// /=}$x;done; )
```
