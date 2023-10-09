
Ls is used to show what files are in your directory.
Cd is used to follow the directory

```bash
pwd # shows the current directory you are in
ls #gets the files in the directory that you are in
cd (directory name here) #goes into the file stated after cd as long as its the next one
cd .. #goes back a directory
cd ../.. #goes back 2 directories
cd ../(directory name here) #goes back and then forwards
ls -a  #shows all files in the directory, including hidden ones
ls -l #shows file details (permissions etc)
```

Touch command is used to create a file
Echo can be used to find files  or used to write to a file if you don't want to edit with vi
The rm command is used to remove folders or files. Cp and mv are used to modify files

```bash
touch (filename) #creates filename
touch -c (filename) #checks if filename exists and then creates if it doesn't
echo (text) #writes the text to the terminal
echo *.txt #writes all files that end in .txt to the terminal
echo "text" > (filename) #writes 'text' to the filname
cp (filename) (new filename) #creates a copy of a file with a new name
mv (filename) (new filename) #renames the original file to the new name
rm (filename) #deletes a file but not a folder
rm -r (folder) #deletes a whole folder
```

mkdir creates a directory if it already doesn't exist

```bash
mkdir hello #creates directory of hello in the directory you are already in
rm -r hello #will delete the directory called hello if you are in its parent directory
mkdir -v hello #will put in the terminal 'created directory hello' once completed
mkdir -p first/second/third #creates the parent directories. So if first and second didnt exist then they would be created. Wouldn't work without the -p
```

