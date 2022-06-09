#!/bin/bash

[ -f ~/README.txt ] && exit 2

rm -v README.txt
echo 'cat *.txt > README.txt'
cat *.txt > ~/README.txt
mv -v ~/README.txt ./
