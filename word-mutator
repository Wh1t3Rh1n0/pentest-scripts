#!/bin/bash
##
## word-mutator 9000
## -----------------
## Generates a wordlist by running all of hashcat's built-in rules on a
## single word (such as a company name) or small list of words.
##
## Primarily intended for targeted, offline password cracking attacks.
##
## Usage: bash ./word-mutator [optional output file] [optional input wordlist]
##
## Because I'm being lazy, you must specify an output file name in order
## to specify an input file. :P
##
## *This script has only been tested with the legacy hashcat 2.00 binaries.*
##
if [ "$3" != "" ] || [ "$1" == "-h" ] || [ "$1" == "--help" ] ; then
  grep -E "^##([^#]|$)" "$0" | sed -E 's/^##.?//g'
  exit
fi

### SETTINGS ###

# Path to hashcat binary
HASHCAT=/opt/hashcat-2.00/hashcat-cli64.bin

# Path to hashcat rules directory
RULESDIR=/opt/hashcat-2.00/rules

### END OF SETTINGS ###


if [ "$(ls $HASHCAT)" == "" ] || [ "$(ls $RULESDIR)" == "" ] ; then
  echo Could not find required hashcat files.
  echo Please check the binary paths defined in $0.
  exit
fi

if [ "$1" != "" ] ; then
  OUTPUT_FILE=$1
else
  OUTPUT_FILE=word-mutator.wordlist
fi

TEMP_PREFIX=/tmp/word-mutator.tmp

rm $TEMP_PREFIX* 2>/dev/null

if [ "$2" != "" ] ; then
  START_FILE=$2
else
  read -p "Base word [Enter for Top 10 common passwords]: " CO_NAME
  if [ "$CO_NAME" == "" ] ; then
    # Top 10 Yahoo Passwords, 2012
    cat <<EOF > $TEMP_PREFIX.0
123456
password
welcome
ninja
abc123
123456789
12345678
sunshine
princess
qwerty
EOF
  else
    echo "$CO_NAME" > $TEMP_PREFIX.0
  fi
  START_FILE=$TEMP_PREFIX.0
fi

echo First pass with selected rules...


# separate phrases into individual words
cat $START_FILE | tr "[:space:]" "\n" >> $TEMP_PREFIX.1
# lowercase only
cat $START_FILE | tr [:upper:] [:lower:] >> $TEMP_PREFIX.1
cat $START_FILE | tr [:upper:] [:lower:] | tr "[:space:]" "\n" >> $TEMP_PREFIX.1
# uppercase only
cat $START_FILE | tr [:lower:] [:upper:] >> $TEMP_PREFIX.1
cat $START_FILE | tr [:lower:] [:upper:] | tr "[:space:]" "\n" >> $TEMP_PREFIX.1
# remove special chars and spaces
cat $START_FILE | tr -d [:punct:] >> $TEMP_PREFIX.1
cat $START_FILE | tr -d [:punct:] | tr -d "[:space:]" >> $TEMP_PREFIX.1
# lowercase only, remove special chars and spaces
cat $START_FILE | tr [:upper:] [:lower:] | tr -d [:punct:] >> $TEMP_PREFIX.1
cat $START_FILE | tr [:upper:] [:lower:] | tr -d [:punct:] | tr -d "[:space:]" >> $TEMP_PREFIX.1
# uppercase only, remove special chars and spaces
cat $START_FILE | tr [:lower:] [:upper:] | tr -d [:punct:] >> $TEMP_PREFIX.1
cat $START_FILE | tr [:lower:] [:upper:] | tr -d [:punct:] | tr -d "[:space:]" >> $TEMP_PREFIX.1

$HASHCAT --stdout -r "$RULESDIR/leetspeak.rule" $TEMP_PREFIX.1 >> $TEMP_PREFIX.2 2>/dev/null
$HASHCAT --stdout -r "$RULESDIR/Ninja-leetspeak.rule" $TEMP_PREFIX.1 >> $TEMP_PREFIX.2 2>/dev/null

echo Removing duplicates...

sort -u $TEMP_PREFIX.2 > $TEMP_PREFIX.3

echo Processing second pass with all rules...

for r in $RULESDIR/*.rule ; do
  $HASHCAT --stdout -r "$r" $TEMP_PREFIX.3 >> $TEMP_PREFIX.4 2>/dev/null
done

echo Removing duplicates...

sort -u $TEMP_PREFIX.* > $OUTPUT_FILE

echo Done.
