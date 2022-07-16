#!/bin/bash

echo 'cat *.txt > README.md'
cat *.txt > README.md
perl -i -pe 'BEGIN{undef $/;}s/^/\`\`\`/;s/$/\`\`\`/' README.md
