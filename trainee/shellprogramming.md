# Shell Programming

## Group 1

## Count number of even length words in the resume ##

```
#!/bin/sh
fpath=$1 #command line input
echo "File path is =$fpath"
base2=$(basename "$fpath")  #get the file name
base="resume.txt"

dire=$(dirname "$fpath")   #get the file directory

cd .. #change directory to initial
cd $dire #change directory to file's directory
#fpath="Downloads//example.txt"
pandoc -f markdown -t plain --wrap=none $base2 -o $base #converting md file to txt

cou=$(cat $base) #cat command fetches the contents of the file
arr=( $cou ) #convert it to array

mi=${arr[0]} 
ma=${arr[0]}

#min and max calculation

for i in $cou
do
if (( ${#i}%2 == 0 ))
then
if [ ${#i} -lt ${#mi} ]
then
mi=$i
#echo $mi
elif [ ${#i} -gt ${#ma} ]
then
ma=$i
fi
fi
done

#printing the words and length
echo "Minimum length word is = $mi"
echo "Size of minimum length word is =${#mi}" 
echo "Maximum length word is = $ma"
echo "Size of maximum length word is =${#ma}"

```
