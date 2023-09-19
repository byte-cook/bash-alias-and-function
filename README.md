# Bash Alias and Function

This project contains useful bash aliases and functions. Bash aliases are essentially shortcuts that save you from having to remember long commands and save you a lot of typing when working on the command line.

To make the alias or function persistent you need to declare it in the ~/.bashrc file. If you want to make your ~/.bashrc more modular, you can store your aliases in a separate file. Some distributions, such as Ubuntu and Debian, include a ~/.bash_aliases file that is referenced in ~/.bashrc.

Pick the shortcuts you are interested in and copy the code into one of the files mentioned above. 

## Run alias commands as root

Alias definition:
```
sudo='sudo '
```

Usage: 
```
sudo <youralias>
```

How it works:
If the last character of the alias value is a blank, then the next command word following the alias is also checked for alias expansion. 
See <https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html?ref=linuxhandbook.com#Aliases>.

## Mount iso file

Alias definition:
```
function mount.iso() {
    # $1: the iso file
    if [ $# -eq 1 ]; then
        sudo mkdir -p /media/iso 
        sudo mount -o loop -t iso9660 "$1" /media/iso
    else
        echo Usage: mount.iso \<iso-file\>
    fi
}
function umount.iso() {
    sudo umount /media/iso
    sudo rmdir /media/iso
}
```

## Convert video files

Convert MP4 files to MP3 files:
```
alias convert.mp42mp3='for f in *.mp4; do ffmpeg -i "$f" "${f%.mp4}.mp3"; done'
```

Convert MOV files to MP4 files
```
alias convert.mov2mp4='for i in *.mov; do ffmpeg -i "$i" -vcodec h264 -crf 30 -acodec aac "${i%.mov}_new.mp4"; done'
```

Convert high quality MP4 files to MP4 files with lower quality:
```
alias convert.mp42mp4='for i in *.mp4; do ffmpeg -i "$i" -vcodec h264 -crf 30 -acodec aac "${i%.mp4}_new.mp4"; done'
```
Or if the conversion is to be done for individual files:
```
function convert.mp42mp4.file() {
    # $1: input file name
    if [ "$#" == 0 ]; then
        echo Usage: convert.mp42mp4.file \<file\> [\<file\> ...]
        return 1
    else
        for n in "$@"
        do
            if [ "${n##*.}" == "mp4" ]; then
                outputFile="${n%.*}"_new.mp4
                ffmpeg -i "$n" -vcodec h264 -crf 30 -acodec aac "$outputFile"
            fi
        done
    fi
}

```

## Download m3u8 file

```
function download.m3u8 {
    # $1: m3u8 url
    # $2: output filename
    if [ $# -eq 2 ]; then
        cd ~/Downloads
        ffmpeg -protocol_whitelist file,http,https,tcp,tls,crypto -i "$1" -c copy "$2"
    else
        echo Usage: download.m3u8 \<m3u8-url\> \<output-file\>
    fi
}
```

## Create folders for MP3 CD

```
function mkdir.mp3() {
    # $1: start index
    if [ "$#" != 1 ]; then
        echo Usage: mkdir.mp3 \<start-index\>
        return 1
    else
        # check input
        if [ $(($1 % 6)) != 1 ]; then
            echo Error: Input is not a valid start index
            return 1
        fi
        
        lastIndex=$(($1+5))
        folder=MP3_$1-$lastIndex
        for i in $(seq $1 $lastIndex);
        do
            mkdir -p $folder/MP3_$i
        done
    fi
}
```

## Appendix: Bash programming reference

<https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html>

${param#pattern} : pattern must match the beginning of param, the rest is returned  
${param%pattern} : pattern must match the ending of param, the rest is returned  


